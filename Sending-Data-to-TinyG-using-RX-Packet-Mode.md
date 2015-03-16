Build 441.xx and later supports packet mode serial transmission. This greatly simplifies communication from the host to TinyG and provides some other advantages.

##Theory of Operation
As of 441.xx TinyG supports two types of serial communications - streaming mode and packet mode. 
###Streaming Mode 
Streaming mode is the "classic" way to communicate with the board and the stream Gcode; little has changed:

- Set streaming mode by setting {rxm:0}
- The serial receive buffer 254 bytes (unchanged)
- XON/XOFF flow control and RTS/CTS flow control are supported (unchanged)
- JSON footers are now in the following format `[2,0,254]`
  - The first element is the footer ID which is now '2'
  - The second element is status code (unchanged)
  - The third element is the number of bytes removed from RX (unchanged)
  - There is no fourth element. The checksum has been removed.
- The {rx:n} command returns the number of bytes available in the RX queue

###Packet Mode 
Packet mode is the new way to communicate with the board and the stream Gcode. Packet mode greatly simplifies host control by pre-parsing serial input into individual lines, then classifying them as `data` or `control` packets. Gcode lines (blocks) are data packets. Just about everything else is a control packet. Data and control packets are queued independently, with priority given to reading control packets. This enables the host to keep the board "full" with Gcode commands, yet to reserve one or more "slots" for control, therefore jumping the queue for things like feedhold and other real-time operations.

There are 16 packet slots of 80 characters each. (Note: Currently lines longer that 80 characters are truncated. We have not seen Gcode blocks > 80 characters, but that doesn't mean they are not out there. We would expect these lines to be either filled with comments, or to have excessive precision (we have seen some naiive Gcode generators provide 15 digits of decimal precision, which is absurd). We may need to do some more work on this character limit, depending on what we see).

Some details to use packet mode:

- Set packet mode by setting {rxm:1}
- XON/XOFF flow control and RTS/CTS flow control are still supported
- JSON footers are now in the following format `[3,0,14]`
  - The first element is the footer ID which is '3'
  - The second element is status code (unchanged)
  - The third element is the number of packets free on the board
  - There is no fourth element. The checksum has been removed.
- The {rx:n} command returns the number of packets available

Note that the number of available (free) buffers reported back in the packet report will always be 2 less than the number of free buffers you think you should have, i.e. 14 instead of 16. This is because there is always a buffer FILLING, and there is always a buffer PROCESSING.