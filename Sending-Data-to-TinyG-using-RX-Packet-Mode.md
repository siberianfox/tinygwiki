Build 441.xx and later supports packet mode serial transmission. This greatly simplifies communication from the host to TinyG and provides some other advantages.

###Theory of Operation
As of 441.xx TinyG supports two types os serial communications - streaming mode and packet mode. Streaming mode is the "classic" way to communicate with the board and the stream Gcode; little has changed:

- The serial receive buffer 254 bytes (unchanged)
- XON/XOFF flow control and RTS/CTS flow control are supported (unchanged)
- JSON footers are now in the following format `[2,0,254]`
  - The first element is the footer format ID which is now '2'
  - The second element is status code (unchanged)
  - The third element is the number of bytes processed in the command (unchanged)
  - There is not fourth element. The checksum has been removed.
