This page is a design page for a major refactoring of the way switches (or more generally, sensors) are handled. This has become necessary to properly support probing cycles, and should be useful in the future for adding encoders and other feedback mechanisms.

##Problem Statement
Currently switches are defined by their homing / limit function. They are bound to an axis, and min or max on that axis. With the introduction of probing cycles this no longer makes sense. What axis is a probe bound to for touch offs and center finding? Further, in the current design all switches are of the same type (NO or NC), and have the same (fixed) deglitch and debounce characteristics. This makes is difficult to configure a system that has both homing/limit switches on XYZ axes and uses the tool as a touch-off probe.

##Discussion of Requirements and Design Options
###General Requirements

* Support various types of switches including (but not limited to)
 * "microswitch" style button and leaf actuated switches
 * probe "switches" where electrical closure is made by contact between the probe (or tool) and the work or a conductive surface
 * Opto switches
 * Hall effect sensors
* have a structure that's also capable of supporting other types of positional and state input including:
 * rotary / linear encoders
 * image recognition systems (e.g. edge or centroid reporting via data inputs)
 * "buttons" and other user-activated inputs
 * firmware generated edge conditions such as soft limits
* Be as resistant to switch mis-configuration as possible without sacrificing flexibility

###Use Cases
We can envision:
* UC1: Limit switch for an axis / system resets on limit switch closure
* UC2: Homing switch for an axes / homing cycles
###Switch Resource Model

## Use Case Detail
###UC1: Limit Switches
Currently when a limit switch is hit all bets are off. This is how my machinist friends tell me it's supposed to work. Hitting a limit is a catastrophic event that immediately shuts down the machine and stops all work. 

In grbl, however, position is preserved when a limit switch is hit; something we've not worried about in TinyG. I see this (position preserved) being used more as compensation for not having a more general probe function. ALso, introducing soft limits should remove many of the cases where a limit will actually get hit. 

Lastly, there are some users who don't use switches at all and instead use probing to zero the machine