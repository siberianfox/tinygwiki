**This page discusses some of the parts of Gcode that we have seen cause confusion. Please feel free to add topics if you are confused.**

##Coordinate Systems and Offsets
TinyG supports the following coordinate systems, offsets and positioning commands that are all related. More detail later, but here they are:

* G53 - Absolute coordinate system (aka machine coordinate system)
* G54 - Coordinate system 1 - set offsets using G10 L2 P1 X...
* G55 - Coordinate system 2 - set offsets using G10 L2 P2 X...
* G56 - Coordinate system 3 - set offsets using G10 L2 P3 X...
* G57 - Coordinate system 4 - set offsets using G10 L2 P4 X...
* G58 - Coordinate system 5 - set offsets using G10 L2 P5 X...
* G59 - Coordinate system 6 - set offsets using G10 L2 P6 X...
* G92 - Temporary offsets (G92.1, G92.2, G92.3, G92.4)
* G28/G28.1 - G28.1 saves the absolute position and G28 returns to the saved position
* G30/G30.1 - G30.1 saves the absolute position and G30 returns to the saved position
* G28.2 - Set absolute coordinates using Homing cycle
* G28.3 - Set absolute coordinates using command
* G38.2 - Probe position

###Absolute Coordinate System
The coordinate systems and their offsets can be viewed in layers. The bottom layer is the **absolute** coordinate system aka **machine** coordinate system. This is always the G53 coordinate system, and can be selected by including G53 on the Gcode line. G53 is non-modal, meaning that it only applies to the line it's present on.

The absolute coordinate system can be set a few different ways. The homing operation (G28.2) will run a homing cycle for the axes specified, at the end of which the absolute coordinate system will be (or is supposed to be) referenced to the homing switches. 

<br>
<br>
At all other times the machine will be in one of the 6 coordinate systems, 1-6, which correspond to G54-G59. If the offsets are zero this will be the same as the 



https://github.com/synthetos/TinyG/wiki/Gcode-Support#g28-g281-g30-g301-go-to-predefined-position
