This page is a design page for a major refactoring of the way switches (or more generally, sensors) are handled. This has become necessary to properly support probing cycles, and should be useful in the future for adding encoders and other feedback mechanisms.

##Problem Statement
Currently switches are defined by their homing / limit function. They are bound to an axis, and min or max on that axis. With the introduction of probing cycles this no longer makes sense. What axis is the tool bound to for touch offs and center finding? Further, in the current design all switches are of the same type (NO or NC), and have the same (fixed) deglitch and debounce characteristics. This makes is difficult to configure a system that has both homing/limit switches on XYZ axes and uses the tool as a touch-off probe.

##Switch Handling