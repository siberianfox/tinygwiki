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
* G28/G28.1 - Save absolute position (G28.1) and return to saved position (G28)
* G30/G30.1 - Save absolute position (G30.1) and return to saved position (G30)
* G28.2 - Set absolute coordinates using Homing cycle
* G28.3 - Set absolute coordinates using command
* G38.2 - Probe position
The following are used to 

The coordinate systems and their offsets can be viewed in layers. The bottom layer is the **absolute** coordinate system aka **machine** coordinate system. This is always the G53 coor

https://github.com/synthetos/TinyG/wiki/Gcode-Support#g28-g281-g30-g301-go-to-predefined-position