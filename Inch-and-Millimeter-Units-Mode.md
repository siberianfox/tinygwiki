This page provides a discussion of Gcode G20/G21 units mode selection and related topics. It explains how things work and what to expect.

##G20 / G21 - Set Units Mode
Basically:
* G20 will cause all gcode from this point onwards to be interpreted in inches
* G21 will cause all gcode from this point onwards to be interpreted in millimeters

The units mode will remain in effect until it is:
* Set by another G20 or G21 command
* An M2 or M30 Program END is hit. At this point it will revert to the Default units ($gun)

##$GUN, {gun:_} - Set Default Units Mode
The GUN setting sets the default (starting) units more on power-on or reset. Gun does not change the units mode on the board if it is changed. It only affects the mode during power on or reset. 


