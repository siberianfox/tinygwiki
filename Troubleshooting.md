**This page is still in progress**

Here's an attempt to collect and writup some common problems with answers. I'm sure it's not complete. We'll add to it as things come up. Let us know if you want something added or have other comments.

See also: [Homing Troubleshooting](https://github.com/synthetos/TinyG/wiki/TinyG-Homing-and-Limits-Troubleshooting)

## Z Axis Stalls During Gcode File
PROBLEM: The Z axis stalls when running a gcode file. We have seen this happen on some Shapeokos and otehr machines.

DIAGNOSIS: Z axes often have different dynamics than the X and Y axes, and are not capable of the maximum velocities or jerk that the X and Y can sustain. This is especially true for machines that use belts for X and Y and screws for Z (such as the Shapeoko), or have fast, multi-start screws for X and Y and finer pitched screws for Z. The Probotix Fireball has 5 TPI screws for X and Y and 12 TPI for Z.

Try dropping the Z axis to minimal settings, getting it to work, then ramping settings back up. For a typical screw-driven Z like a Shapeoko you might try:
<pre>
Run G21 first so all settings are in mm mode
$3mi=2          set motor3 to 2 microsteps (assuming motor 3 is mapped to the Z axis
$zvm=400        set max velocity to 400 mm/min. Shapeoko Z should be able to do 1000, but don't start there  
$zjm=10000000   10 million. Ramp up to about 50 million once you clear the lower numbers
</pre>


## Erratic Gcode operation, Z axis plunges, arc specification errors, etc.
PROBLEM: The Gcode file you are sending behaves erratically and may have these symptoms:
* Rapid moves to random locations
* Z axis plunging unexpectedly
* Gcode errors returned in message line

DIAGNOSIS: This is usually a failure in the program sending Gcode to the board. One of two things is probably happening (1) the serial buffer is being overwritten because the host is not performing adequate flow control, or (2) $ configuration commands are being sent while the tool is in motion or are being sent too rapidly.

Are you using Coolterm to send files? Are you using Xon/Xoff mode? It is enabled in both Coolterm and on TinyG ($ex)?

## Crash/reset on move
PROBLEM: Two users have reported problems where the board crashed (program execution stopped and boot message was sent over serial) upon a move command on a particular axis. (See [forum thread](https://www.synthetos.com/topic/reset-on-move/).) The specific cause is unknown, but one user reported the problem vanishing after some physical manipulation of the stepper motor wires; there is a hypothesis that a problem with an unreliable motor connection may cause back-EMF discharge into the board, disrupting the CPU. (edited - tdierks)
...and other user the problem stopped when they located and fixed a short in the limit switch wiring. 

DIAGNOSIS: We don't think this is a TinyG problem. Solution - check your switch and motor wiring for reliability and shorts.