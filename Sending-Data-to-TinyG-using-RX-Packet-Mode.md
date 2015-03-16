Build 441.xx and later supports packet mode serial transmission. This greatly simplifies communication from the host to TinyG and provides some other advantages.

##Theory of Operation
As of 441.xx TinyG supports two types of serial communications - streaming mode and packet mode. 
###Streaming Mode 
Streaming mode is the "classic" way to communicate with the board and the send Gcode. Little has changed:

- Set streaming mode by setting `{rxm:0}`.
- The serial receive buffer is 254 bytes (unchanged)
- XON/XOFF flow control and RTS/CTS flow control are supported (unchanged)
- JSON footers are now in the following format `[2,0,254]`
  - The first element is the footer ID which is now `2`
  - The second element is status code (unchanged)
  - The third element is the number of bytes removed from RX (unchanged)
  - There is no fourth element. The checksum has been removed.
- The `{rx:n}` command returns the number of bytes available in the RX queue

###Packet Mode 
Packet mode is a new way to communicate with the board and to send Gcode. Packet mode simplifies host control by pre-parsing serial input into individual lines, then classifying them as `data` or `control` packets. Gcode lines (blocks) are data packets. Just about everything else is a control packet. Data and control packets are queued independently, with priority given to reading control packets. This enables the host to keep the board "full" with Gcode commands, yet to reserve one or more packet slots for control, therefore jumping the queue for things like feedhold and other real-time operations.

There are 16 packet slots of 80 characters each. _(Note: Currently lines longer that 80 characters are truncated. We have not seen Gcode blocks > 80 characters, but that doesn't mean they are not out there. We would expect these lines to be either filled with comments, or to have excessive precision (we have seen some naiive Gcode generators provide 15 digits of decimal precision, which is absurd). We may need to do some more work on this character limit, depending on what we see)._

Some details of packet mode:

- Set packet mode by setting `{rxm:1}`
- XON/XOFF flow control and RTS/CTS flow control are still supported
- JSON footers are now in the following format `[3,0,14]`
  - The first element is the footer ID which is `3`
  - The second element is status code (unchanged)
  - The third element is the number of packets free on the board
  - There is no fourth element. The checksum has been removed.
- The `{rx:n}` command returns the number of packet slots available

Note that the number of available (free) slots reported back in the packet report will always be 2 less than the number of free buffers you think you should have, i.e. 14 instead of 16. This is because there is always a buffer FILLING, and there is always a buffer PROCESSING.

####Using Packet Mode from the Host
We may refine this, but here's where we are now:

- Set up serial communications as normal.

- Select RX packet mode by sending `{rx:1}` (or `{"rx":1}` if so inclined)

- You must use JSON mode as the available packet count is returned in the JSON header.

- It's a good idea for the host to have its own independent data and control queues (channels). Send Gcode down the data channel and everything else down the control channel. This way you can always inject a control command in the middle of a Gcode job.

- Set up the sender to read from your Gcode "file". Send a line, then look at the available slots. Keep sending as long as there is at least 1 slot free, but always keep at least 1 slot free for commands. Naturally, if you get a control, send it right way.

- Use `{rx:n}` if you need to find out the number of available slots