For now this page is just rough notes up for discussion.

_This issue obviously affects the current FTDI-driven USB, but we are also considering methods that will work best as the native USB is brought online. So some of this discussion is forward-looking_

_Updated 11/13/13 to reflect current state - circa 380.08 (master) and build 398.xx in dev_

## Flow Control Problem Statement

The control of the commands going to the TinyG board and the responses returned is a complex problem that we have been working through for some time now. It's one class of problem if the machine is moving at nominal feed rates and long-ish Gcode segment lengths (block lengths); it becomes significantly harder as the feed rates and command rates increase and the segment lengths decrease.

### Flow Control Issues Explained

RS-232-style serial communication like that employed by the TinyG between the FTDI USB-to-serial converter and the Atmel XMega microcontroller has several advantages but also a few disadvantages.

The advantages are that there are only two wires required for two-way communication: one to transmit (TX), and one to receive (RX).  In order to accomplish talking in either direction over only one wire the two sides have to agree to the timing and format of the signaling.

The disadvantage of this style of communication is that the TX and the RX are each transmitted blindly. Other than the device on the other end sending a response (over the other wire) there's no way to know when a transmission has been received, which is usually just a problem of knowing if the other device is listening. There also no way to tell when the receiving device has been able to handle all of the data that it was sent. Solving this problem is called flow control.

There are many type of flow control, but there are two general methods we will employ, software XON/XOFF and hardware RTS/CTS (Request-To-Send, Clear-To-Send)

In software flow control, the two devices will send special data (XON and XOFF characters) over the line to indicate that it is ready for more data or that it doesn't have room for more data, respectively. This is problematic because the XON/XOFF characters are then part of the stream, and in some cases, that means that they go into the end of a transmit buffer. This means that all of the characters ahead of it in the buffer must transmit first. If the hardware on both ends of the line supports XON/XOFF, and will transmit those characters ahead of any internal buffers, then the response time is pretty good.

With hardware flow control, two additional wires are used, one for the RX (RTS, or Request To Send) and one for TX (CTS, or Clear To Send). Once device controls RTS, and the other controls CTS. For each device, they set RTS when they have buffer space, and clear it when the buffer is (almost) full. When transmitting, the device reads the CTS line, and if it's set we send data. This means that the response to a buffer full condition is near immediate.

### How this effects TinyG

The challenge is to accomplish and balance these four things:

1. Never overflow a buffer on the TinyG or FTDI. (The XON/XOFF or RTS/CTS should handle this for us, but *it must be turned on per connection from the host side.*)
1. Keep the planner full enough so it plans optimally and does not starve. This generally requires at least 8 of the 28 buffers to be occupied, 12 is a safer number
1. Keep the serial channel open so that feedhold ("!") and other special characters can be injected at any time
1. Keep a flow of status reports and queue reports back to the host to report on command status and system state changes

Turns out this is actually a hard problem. Here are some things to make note of:

* Many commands other than just Gcode movement will end up in the planner buffer, and it's possible that a line of Gcode will consume from 0 to 4 buffers. Possibly more. Basically, any command that needs to be synchronized with Gcode execution needs to be queued, so this includes M commands, spindle commands, dwells, etc. Since it's possible to put multiple non-modally-related commands on the same Gcode line there can be more than 1 command per line that ends up in the planner queue. This mostly happens at the start of the Gcode file during setup. It's less common during actual Gcode move execution.

* Further complicating things is that the generation of queue reports (QRs) is not always one-to-one with commands sent. When a QR is requested it is not sent until the system "gets around to" generating reports and queueing transmissions. In other words, a request for a QR is logged but the report is not generated or sent yet. If the system is really busy pushing out a lot of really short Gcode lines - e.g. 5 or 10 ms each, it's going to spend it's time generating and pulsing those lines, not generating and sending reports. So in heavy cases there may be multiple requests for QRs made before one is actually sent. When one IS sent it does reflect the current state of the system - it may have "humped" a few states, however. This is unavoidable if you want to the system to prioritize step generation and processing new moves over serial IO.

* The same is true of status reports (SRs). Depending on the rate of command processing and the minimum reporting interval set in the $SI setting there may be multiple state changes occurring before a status report is actually generated and sent. This can look like "skipping ahead" through multiple line numbers, and other jumps. Again, this is unavoidable if the rate of processing exceeds the rate at which the reports can be sent. TinyG manages this so that status reports do not flood.

* Even with very rapid movement the planner queue does not need to be full to optimally plan. Typically about 8 buffers are required for optimal planning. The minimum time for a MOVE in the planner to execute is 5 ms, occasionally less. Non-moves (e.g. M codes) may execute faster. So on average 8 buffers would take a minimum of 40 ms, probably longer. 24 would take 120 ms, probably longer. This gives you an idea of how fast the host must react to prevent queue starvation.
 * If the USB channel is running at 115,200 baud then the channel can handle about 100 characters per millisecond, so if the planner is running maximally fast (5 ms per gcode block) then you could theoretically send 500 characters to TinyG in that time (which is way more than you need for a Gcode block). So the dominant factor is the latency with which the host will respond.

* In order to manage inbound command flow and avoid buffer full conditions the serial buffer will hold an input line until there are at least 4 buffers available in the planner queue. This is to ensure that the line can be processed, and will not result in an exception. The current queue depth is 28 buffers, so at any given point no more than 24 buffers can be filled.


## Current Flow Control Options in Master Branch
The following methods are currently possible and supported in the Master Branch (build 380.xx)

* XON/XOFF flow control with "blast". Configure $ex=1 and the TinyG emits XOFF and XON characters. The FTDI, when properly configured, sends characters until it's told to stop; resumes when XON is received. This is what Coolterm does if XON/XOFF is enabled. The advantage is that this is very simple and works very well; the disadvantage is that there is no convenient way to inject feedholds. _However,_ if the FTDI is _not_ configured to obey XON/XOFF from the TinyG, then the XON/XOFF characters will be passed on to the host and the controlling software must honor them. This is problematic because the XOFF may be received after the transmit buffers of the host software, OS, and FTDI are already loaded with data, and those will still be sent even if the host stops transmitting immediately.

* Managing serial queue depth by character counting. The footer returns the number of bytes removed from the serial queue so that the host can know how many bytes are occupied in the serial queue. If the serial queue is non-zero, then the assumption is that the planner queue is full or near full. This method is brittle as the character count can get out of phase with errors, and is therefore not the preferred method.

* Managing planner queue depth with queue reports. Enabling queue reports ($qv=1) will cause the queue depth to be reported each time a command(s) is added to the planner. As mentioned earlier, these queue reports may be throttled by the system if they are very closely spaced. This method, coupled with some form of flow control, is preferred.

* RTS/CTS flow control
RTS/CTS is currently in master branch (build 380.08) and later builds. RTS/CTS is available in addition to XON/XOFF as mutually exclusive settings: e.g.
$ex=0  no flow control
$ex=1  XON/XOFF flow control
$ex=2  RTS/CTS flow control

## Additional Flow Control Options Available in Edge Branch

### Triple queue reports
The following is in test in dev and edge: Setting $qv=2 (triple reports) will return:

{"qr":27, "qi":1, "qo":0}

Where qr (27) is the number of available buffers in the queue
qi (1) is the number of buffers added to the queue since the previous queue report
qo (0) is the number of buffers removed from the queue since the previous queue report

This allows the host to know not only the queue depth, but the rate at which it is filling and emptying, and therefore manage the transmission of new Gcode blocks. A simple throttling scheme is just to send lines until the buffer fills to some level - say 4 buffers left - then use the buffers removed value as the number of lines to send to replenish the buffer.

This allows the host to make a decision to send more lines or not, and roughly how many more lines it should send to avoid starvation. Keep in mind the following facts:

So here are some cases the host would react to:

- The QRs show available buffers dropping per report, a move coming in for each report and very few moves going out. This indicates long(ish) moves being queued - at least moves that take longer to execute than the serial system can deliver them. In this case stop sending new moves when there are 4 to 6 buffers left.

- The QRs show available buffers dropping fast, one move coming in per report, and many moves going out. This indicates that a lot of short moves are being executed and the queue is being drained faster than it's being replenished. The host should try to replenish the buffer by sending about as many lines to the board as have been removed.

### Other things that have been done:

* Eliminate filtered queue reports
I don't think anyone is using them. This has reduced code and maintenance size and complexity.

## JSON Changes
### JSON Footer Depth
It is more correct (and easier to manage) if the 'f' footer is a sister element to the 'r' response rather than a child. A new footer option has been added to accomplish this. Both live as siblings at depth 0 under the outer enclosing curlies {}. This causes some of the current JSON to change. In order to maintain backwards compatibility with existing UIs TinyG provides a footer-depth setting, $fd. fd=0 is the new behavior, fd=1 is the legacy behavior. Examples:
<pre>
new style ($fd=0): {"r":{"fb":380.05,"fv":0.950,"hv":7,"id":"9H3583-YMZ","msg":"SYSTEM READY"},"f":[1,0,0,4079]}
old style ($fd=1): {"r":{"fb":380.05,"fv":0.950,"hv":7,"id":"9H3583-YMZ","msg":"SYSTEM READY","f":[1,0,0,4079]}}

new style ($fd=0): {"r":{"sr":{"line":0,"posx":0.000,"posy":0.000,"posz":0.000,"feed":0.00,"vel":0.00,"unit":1,"stat":1}},"f":[1,0,10,9193]}
old style ($fd=1): {"r":{"sr":{"line":0,"posx":0.000,"posy":0.000,"posz":0.000,"feed":0.00,"vel":0.00,"unit":1,"stat":1},"f":[1,0,10,9193]}}
</pre>

### Some additional JSON changes:

* A null response is returned as {}.

* Responses to ill-formed JSON. Ill-formed JSON (like a lone { on a line) and is trapped and returned as an exception report inside a well formed JSON response. Something like:

{"r":{"ex":"{"}},"f":[1,48,00,0000]} where the '48' is the response code for a JSON syntax error (malformed JSON).  
