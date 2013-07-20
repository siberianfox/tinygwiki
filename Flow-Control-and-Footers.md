Just notes for now:

_This issue obviously affects the current FTDI-driven USB, but we are also considering methods that will work best as the native USB is brought online. So some of this discussion if forward-looking_

##Flow Control Problem Statement
The control of the commands going to the TinyG board and the responses returned is a complex problem that we have been working through for some time now. It's one class of problem if the machine is moving at nominal feed rates and Gcode segment lengths (block lengths); it becomes significantly harder as the feed rates and command rates increase and the segment lengths decrease. 

The challenge is to accomplish and balance these 3 things:

1. Keep the planner full enough so it plans optimally and does not starve. This generally requires at least 8 of the 28 buffers to be occupied, 12 is a safer number.
1. Keep the serial channel open so that feedholds characters (!'s) can be injected at any time
1. Keep a flow of status reports back to the host to report on command status and system state changes


## Current Flow Control Options
The following methods are currently possible and supported

* XON/XOFF flow control with "blast". Configure $ex=1 and the host obeys XOFF and XON characters. The host sends characters until it's told to stop; resumes when XON is received. THis is what Coolterm does if XON/XOFF is enabled. The advantage is that this is very simple and works very well; the disadvantage is that there is no convenient way to inject feedholds.

* Managing serial queue depth by character counting. The footer returns the number of bytes removed from the serial queue so that the host can know how many bytes are occupied in the serial queue. If the serial queue is non-zero, then the assumption is that the planner queue is full or near full. This method is brittle as the character count can get out of phase with errors, and is therefore not the preferred method.

* Managing planner queue depth with queue reports. Enabling queue reports ($qv=2) will cause the queue depth to be reported each time the planner queue changes. This method, coupled with some form of flow control, is preferred. 

## Changes Being Considered / Options

#### Add RTS/CTS flow control
Add this in addition to XON/XOFF as mutually exclusive settings: e.g. 
$ex=0  no flow control
$ex=1  XON/XOFF flow control
$ex=2  RTS/CTS flow control

####Extend queue reports with more information 
The following is not in test in dev:
Setting $qv=3 will return:

{"qr":[1,2,3]}   

Where '1' is the number of available buffers in the queue
'2' is the number of buffers added to the queue since the previous queue report
'3' is the number of buffers removed from the queue since the previous queue report

####Eliminate filtered queue reports
I don't think anyone is using them. This will reduce code and maintenance size and complexity.

####Eliminate the checksum in the footer
Again, I don't think anyone is using it, and it adds complexity

