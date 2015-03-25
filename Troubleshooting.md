Here's an attempt to collect and writup some common problems with answers. I'm sure it's not complete. We'll add to it as things come up. Please feel free to add to this page or let us know if you want something added or have other comments.

**Topics**
* [Motors hum but don't move](https://github.com/synthetos/TinyG/wiki/Troubleshooting#motors-hums-but-doesnt-move)
* [System Shuts Down and Generates an ER Message](https://github.com/synthetos/TinyG/wiki/Troubleshooting#system-shuts-down-and-generates-an-er-message)
* [Z Axis Stalls During Gcode File](https://github.com/synthetos/TinyG/wiki/Troubleshooting#z-axis-stalls-during-gcode-file)
* [Erratic Gcode operation, Z axis plunges, arc specification errors, etc.](https://github.com/synthetos/TinyG/wiki/Troubleshooting#erratic-gcode-operation-z-axis-plunges-arc-specification-errors-etc)
* [Crash/Reset on Move](https://github.com/synthetos/TinyG/wiki/Troubleshooting#crashreset-on-move)
* [Firmware Update Verification Failure](https://github.com/synthetos/TinyG/wiki/Troubleshooting#firmware-update-verification-failure)

See also: [Homing Setup and Troubleshooting](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting)

## Motor(s) hums but doesn't move 
**PROBLEM**: One or more motors hums but the motors don't move. It doesn't matter how you set the current pots - it still happens. VARIANT: ...but it worked yesterday.

OTHER SYMPTOMS: Buffer corruption; machine wanders off aimlessly in the middle of a file; erratic operation in general 

**DIAGNOSIS #1**: The power supply might be inadequate and be collapsing under load, or it might be excessively noisy. If you have a VOM you can test the voltage - or better yet use an oscilloscope to test the voltage and look at how clean the waveform is. A setup with 3 NEMA17 motors usually requires a power supply of at least 4 amps. 3 NEMA23's requires at least 6 amps. We have seen supply/noise problems on power supplies that are under-rated for the load, and on cheap supplies that do not regulate well.

Also the board runs better at 24 - 30 volts than 12 volts. The motors are snappier and don't heat up as much as they do with lower voltages (this is not a typo - it's true - higher voltage makes the motors and driver chips run cooler as they spend less time in switching). 

**DIAGNOSIS #2**: The current setting pot is broken. If the pot has failed CLOSED then the current reference voltage will prevent the driver from delivering any current. This is more likely to occur on the v6 and earlier boards that use the [Murata PVG3G502C01R00](http://www.mouser.com/ProductDetail/Murata/PVG3G502C01R00/?qs=%2fha2pyFadujnuS%2ft7JadhCuZJcqCPg4UcIYXtdCnkEtP24rXvClytw%3d%3d) 3mm silver pots than the v7 that uses the Bourns 5mm tan pots.

## System Shuts Down and Generates an ER Message
**PROBLEM**: The system shuts down sporadically during cutting, or randomly at startup or during other operations. It generates a message like this: 
<pre>
{“er”:{“fb”:370.08,”st”:27,”msg”:”System shutdown”,”val”:1}}
</pre>

**DIAGNOSIS**: This is most often the result of a noisy limit switch line. Check these conditions:
* One or more limit switches are wired and enabled, e.g. $xsx=3, $ysn=2, etc. See [Limit and Homing Switches](https://github.com/synthetos/TinyG/wiki/TinyG-Homing#switch-configuration) 
* Occurs during movement but not when still. May be indicative of motor noise getting into the switch lines.
* A spindle is on. Spindles can generate a lot of electrical noise which can get into the switch lines.
* If the "val" is something other than 1 this may indicate an internal memory or other error. TinyG shuts down for safety reasons if memory corruption is detected. Please contact the Synthetos gmail address if you see this.

**SOLUTIONS**: There is no silver bullet to stopping erratic limit switch closures, except of course turning off the switches. Try the following steps.
* It's best to address noise at the source. Cheap brush spindles tend to be the noisiest. Higher quality spindles generate less noise both electrically and mechanically, and often are brushless. Some steps to quiet an electrically noisy spindle:
 * Replace or clean the spindle brushes if it is a brush type
 * Wind the AC cord through a ferrite torroid
 * Physically separate the spindle AC cord from limit switch lines, and only cross at 90 degree angles.
 * Isolate the spindle electrically, even going so far as to plug it into a different AC circuit. A spindle relay can still be used to control it.
 * Replace the spindle with a higher-quality unit
* Use Normally Closed (NC) switches or the NC positions on your switches. NC switche configurations are more noise resistent than NO configurations. _Note: All switches in the machine must be the same type. Be sure to set the $st variable for NC switches._
* Try conditioning the limit switch lines:
 * Use grounded, shielded wire for limit lines. Ground the lines at the TinyG board end.
 * Use grounded, shielded wire for stepper lines. Ground the lines at the TinyG board end.
 * Physically isolate the limit lines from the stepper lines and cross only at 90 degree angles.
 * Provide a pullup resistor on each switch line. Tie the switch side of the line to 3.3v through a 1K resistor. Use the 3.3v available on the limit switch header. DO NOT USE 5v OR HIGHER VOLTAGES! _Note: v7 and later boards have pullup resistors on-board, so this step is not usually necessary._
 * In addition to the pullup resistor, provide a small capacitor to ground such as 0.1 uF capacitor
* If all else fails disable the limit switches using the switch settings

See also: [Crash/Reset on Move](https://github.com/synthetos/TinyG/wiki/Troubleshooting#crashreset-on-move)

## Z Axis Stalls During Gcode File
**PROBLEM**: The Z axis stalls when running a gcode file. We have seen this happen on Shapeokos and other machines that have very different dynamics between the XY and Z axes.

**DIAGNOSIS**: Z axes often have different dynamics than the X and Y axes, and are not capable of the maximum velocities or jerk that the X and Y can sustain. This is especially true for machines that use belts for X and Y and screws for Z (such as the Shapeoko2), or have fast, multi-start screws for X and Y and finer pitched screws for Z. The Probotix Fireball has 5 TPI screws for X and Y and 12 TPI for Z.

The first thing to check is the Z mechanical system. Does the Z axis turn freely by hand? Eliminate mis-alignment or excess friction first.

Next try dropping the Z axis to minimal settings, getting it to work, then ramping settings back up. For a typical screw-driven Z like a Shapeoko you might try:
<pre>
Run G21 first so all settings are in mm mode
$4mi=2     motor4 = 2 microsteps (assuming m3 is mapped to Z axis)
$zvm=600   max velocity 600 mm/min. Shapeoko2 Z should be able to do 1000 - 1200
$zjm=10    10 million. Ramp up to about 50 million once you clear the lower numbers
</pre>

Another possible culprit are the junction deviation settings that set cornering speeds. Here are some values that work well on a Shapeoko2
<pre>
[ja] Junction acceleration = 1 to 2 million (larger is faster)
[xjd] x junction deviation = 0.1 to 0.01 (larger is faster) 
[yjd] y junction deviation = 0.1 to 0.01 (larger is faster) 
[zjd] z junction deviation = 0.1 to 0.01 (may need to be smaller value than X and Y)
</pre>

Some other things you might check:
* Does the Z axis turn freely by hand? Eliminate mis-alignment or excess friction first
* Where is the Z current pot? It should be about 1/2 way, the 12:00 position. Adjust up or down later
* Does the Z chip (motor3, presumably) get hot or much hotter than the X and Y chips?
* Are the 2 large thermal vias under the driver chips soldered on the bottom of the board for all chips?
* Is your motor wiring solid? See [Crash/Reset on Move](https://github.com/synthetos/TinyG/wiki/Troubleshooting#crashreset-on-move)
* It's also possible that the Z axis driver is bad. Try swapping the Z to the A axis and remapping Motor4 to the Z axis 

## Erratic Gcode operation, Z axis plunges, arc specification errors, etc.
**PROBLEM**: The Gcode file you are sending behaves erratically and may have these symptoms:
* Rapid moves to random locations
* Z axis plunging unexpectedly
* Gcode errors returned in message line

**DIAGNOSIS**: This is usually a failure in the program sending Gcode to the board. One of two things is probably happening (1) the serial buffer is being overwritten because the host is not performing adequate flow control, or (2) $ configuration commands are being sent while the tool is in motion or are being sent too rapidly.

Are you using Coolterm to send files? Are you using Xon/Xoff mode? It is enabled in both Coolterm and on TinyG ($ex)?

## Crash/Reset on Move
**PROBLEM**: Two users have reported problems where the board crashed (program execution stopped and boot message was sent over serial) upon a move command on a particular axis. (See [forum thread](https://www.synthetos.com/topic/reset-on-move/).) The specific cause is unknown, but one user reported the problem vanishing after some physical manipulation of the stepper motor wires; there is a hypothesis that a problem with an unreliable motor connection may cause back-EMF discharge into the board, disrupting the CPU. (edited - tdierks)
...and other user the problem stopped when they located and fixed a short in the limit switch wiring. 

**DIAGNOSIS**: We don't think this is a TinyG problem. Solution - check your switch and motor wiring for reliability and shorts.

## Firmware Update Verification Failure
**PROBLEM**:  While attempting to update TinyG's firmware via avrdude, the verification process fails with the error: avrdude:`verification error; content mismatch`

**DIAGNOSIS**:  The simple answer is the XMEGA processor's LOCKBITS are "locked".  Click [here](https://github.com/synthetos/TinyG/wiki/Firmware-Update-Verification-Failure) to read how to fix this.