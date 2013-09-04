This page is a design page for a major refactoring of the way switches (or more generally, sensors) are handled. This has become necessary to properly support probing cycles, and should be useful in the future for adding encoders and other feedback mechanisms.

##Problem Statement
Currently switches are defined by their homing / limit function. They are bound to an axis, and min or max on that axis. With the introduction of probing cycles this no longer makes sense. What axis is a probe bound to for touch offs and center finding? Further, in the current design all switches are of the same type (NO or NC), and have the same (fixed) deglitch and debounce characteristics. This makes is difficult to configure a system that has both homing/limit switches on XYZ axes and uses the tool as a touch-off probe.

##Design Options for Discussion
###General Requirements

* Support various types of switches including (but not limited to)
 * "microswitch" style button and leaf actuated switches
 * probe "switches" where electrical closure is made by contact between the probe (or tool) and the work or a conductive surface
 * Opto switches
 * Hall effect sensors
* have a structure that's also capaple of supporting other types of positional and state input including:
 * rotary / linear encoders
 * image recognition systems (e.g. edge or centroid reporting via data inputs)
 * "buttons" and other user-activated inputs

###Switch Uses Cases

###Switch Resource Model