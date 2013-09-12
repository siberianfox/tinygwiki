Hard alarms are caused hitting a limit switch or by an unrecoverable internal error. In this case the system is considered unrecoverable and the current job is presumed lost. Soft alarms are not yet supported; see section on behaviors as we work this out.  

##Hard Alarms in Version 0.96 and Earlier
The current system behavior for an alarm is:
* A switch configured as a limit being hit causes the board to go into an ALARM state (status code = 2).  (also the case for an internal assertion failure).
* Everything is shut down immediately and the spindle direction LED flashes quickly. (Note: an assertion failure might find the system so compromised that it cannot even do this and remaining steps)
* An exception report is generated indicating the cause
* The system will not process any new input, or any commands queued in the planner or serial input buffers.
* The system can only be recovered by hitting RESET, or by sending ^x (control X) to the serial port.

##Discussion of Revised Alarms 
We would like to preserve the hard alarm behavior, but introduce soft alarms as well.
* Hard alarms still work like above, but status code is moved from 2 to 11 (or some other number)

If a soft alarm is triggered the following happens:
* System halts all movement with a feedhold and preserves position at the feedhold point.
* Stops processing any commands in the planner or serial queue
* Generates an exception report indicating the type of alarm. The type is returned as a status code (2) and some displayable message text.
* The system will still receive input as full commands. NOTE: This is only possible if there is space in the buffer
