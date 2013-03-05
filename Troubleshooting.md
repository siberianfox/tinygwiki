**This page is still in progress**

Here's an attempt to collect and writup some common problems with answers. I'm sure it's not complete. We'll add to it as things come up. Let us know if you want something added or have other comments.

See also: [Homing Troubleshooting](https://github.com/synthetos/TinyG/wiki/TinyG-Homing-and-Limits-Troubleshooting)

## Erratic Gcode operation, Z axis plunges, arc specification errors, etc.
PROBLEM: The Gcode file you are sending behaves erratically and may have these symptoms:
* Rapid moves to random locations
* Z axis plunging unexpectedly
* Gcode errors returned in message line

SOLUTION: This is usually a failure in the program sending Gcode to the board. One of two things is probably happening (1) the serial buffer is being overwritten because the host is not performing adequate flow control, or (2) $ configuration commands are being sent while the tool is in motion or are being sent too rapidly.

Are you using Coolterm to send files? Are you using Xon/Xoff mode? It is enabled in both Coolterm and on TinyG ($ex)?

## Crash/reset on move
PROBLEM: Two users have reported problems where the board crashed (program execution stopped and boot message was sent over serial) upon a move command on a particular axis. (See [forum thread](https://www.synthetos.com/topic/reset-on-move/).) The specific cause is unknown, but one user reported the problem vanishing after some physical manipulation of the stepper motor wires; there is a hypothesis that a problem with an unreliable motor connection may cause back-EMF discharge into the board, disrupting the CPU. (edited - tdierks)
...and other user the problem stopped when they located and fixed a short in the limit switch wiring. 

RESOLUTION: We don't think this is a TinyG problem. Solution - check your switch and motor wiring for reliability and shorts.