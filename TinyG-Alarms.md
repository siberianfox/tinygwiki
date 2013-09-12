Hard alarms are caused hitting a limit switch or by an unrecoverable internal error. In this case the system is considered unrecoverable and the current job is presumed lost. Soft alarms are not yet supported; see Discussion of Soft Alarms as we work this out.  

##Hard Alarms in Version 0.96 and Earlier
The current system behavior for an alarm is:
* A switch configured as a limit being hit causes the board to go into an ALARM state (status code = 2). 
* (Another case generating a hard alarm is an internal assertion failure such as a memory corruption or stack overflow).
* Everything is shut down immediately and the spindle direction LED flashes quickly. (Note: an assertion failure might find the system so compromised that it cannot even do this and remaining steps)
* An exception report is generated indicating the cause
* The system will not process any new input, or any commands queued in the planner or serial input buffers.
* The system can only be recovered by hitting RESET, or by sending ^x (control X) to the serial port.

##Discussion of Soft Alarms 
We would like to preserve the hard alarm behavior, but introduce soft alarms as well.
* Hard alarms still work like above, but status code is moved from 2 to 11 (or some other number)

If a soft alarm is triggered the following happens:
* System halts all movement with a feedhold and preserves position at the feedhold point.
* Stops processing any commands in the planner or serial queue
* Generates an exception report indicating the type of alarm. The type is returned as a status code (2) and some displayable message text.

The interesting question is what to do next. If the serial RX buffer is empty it's easy enough to send in a command to release the alarm state (calm), but if it's not that's a different story. Running the system using queue reports can keep the buffer empty. 

(a) One possible solution to a non-empty RX buffer is to read and reject all motion commands with a known error response such as "system alarmed, command rejected" until a calm command is received. This puts the burden of replay back on the controller. Status reports and other queries should be possible prior to receiving a calm.

(b) Another possibility is to add a new single character command that can jump the queue (e.g. ^Y) or overload one of the existing ones, like a feedhold (!) received during an alarm. But even this won't work if the host is jammed up in flow control on its end. Nothing TinyG can do about that.

I suggest (a) even though it's a bit messy. Some of this could be mitigated using line numbers or a machine generated "program counter" so the host knows exactly what was rejected.

Really speaks to managing the flow control using QRs and not serial-level FC.
