Just notes for now:

_This issue obviously affects the current FTDI-driven USB, but we are also looking for the methods that will best be supported as the native USB is brought online. SO some of this discussion if forward-looking_

The control of the commands going to the TinyG board and the responses returned is a complex problem that we have been working through for some time now. It's one class of problem if the machine is moving at nominal feed rates and Gcode segment lengths (block lengths); it becomes significantly harder as the feed rates and command rates increase and the segment lengths decrease. 

The challenge is to accomplish and balance these 3 things:

1. Keep the planner full enough so it plans optimally and does not starve. This generally requires at least 8 of the 28 buffers to be occupied, 12 is a safer number.
1. Keep the serial channel open so that feedholds characters (!'s) can be injected at any time
1. Keep a flow of status reports back to the host to report on command status and system state changes

The following methods are currently possible and supported

* XON/XOFF flow control. Configure $ex=1 and the host obeys XOFF and XON characters. 

* Host character counting

