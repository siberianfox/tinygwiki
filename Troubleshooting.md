**This page is still in progress**

Here's an attempt to collect and writup some common problems with answers. I'm sure it's not complete. We'll add to it as things come up. Let us know if you want something added or have other comments.

See also: [Homing Troubleshooting](https://github.com/synthetos/TinyG/wiki/TinyG-Homing-and-Limits-Troubleshooting)

## Erratic Gcode operation, arc specification errors popping up, etc.
Are you using Coolterm to send files? Are you using Xon/Xoff mode? It is enabled in both Coolterm and on TinyG ($ex)?

## TinyG crash/reboot on move
Two users have reported problems where the board crashed (program execution stopped and boot message was sent over serial) upon a move command on a particular axis. (See [forum thread](https://www.synthetos.com/topic/reset-on-move/).) The specific cause is unknown, but one user reported the problem vanishing after some physical manipulation of the stepper motor wires; there is a hypothesis that a problem with an unreliable motor connection may cause back-EMF discharge into the board, disrupting the CPU.