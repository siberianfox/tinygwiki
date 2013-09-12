Hard alarms are caused hitting a limit switch or by an unrecoverable internal error. In this case the system is considered unrecoverable and the current job is presumed lost. Soft alarms are not yet supported; see section on behaviors as we work this out.  

##Hard Alarms in Version 0.96 and Earlier
The current system behavior for an alarm is:
* A switch configured as a limit being hit causes the board to go into an ALARM state (status code = 2).  (also the case for an internal asertion failure).
* An exception report is generated indicating the cause (Note: an assertion failure might find the system so compromised that it cannot even do this and remaining steps)
* Everything is shut down and the spindle direction LED flashes quickly. 
* The system will not process any new input, or any commands queued in the planner or serial input buffers.
* The system can only be recovered by hitting RESET, or by sending ^x (control X) to the serial port.

Suggestions for new behavior.
- The hard alarms still work like the above, but the status code is moved to 11 (or some other number) than 2. We want 2 for soft alarms

If a soft alarm is triggered the following happens:
- system halts all movement with a feedhold
- stops processing any commands in the planner or serial queue
- If the alarm was caused by a 
- generates an exception report indicating the type of alarm. The type is returned as a status code and some message text
- generates a status report. The stat value = 2 (alarm)
- Will now only accept 