**This page discusses some of the parts of Gcode that we have seen cause confusion. Please feel free to add topics if you are confused.**

##Coordinate Systems and Offsets
TinyG supports the following coordinate systems, offsets and positioning commands that are all related. More detail later, but here they are:

* G10 L2 - Set coordinate offsets 
* G53 - Absolute coordinate system (aka machine coordinate system)
* G54 - Coordinate system 1 - set offsets using G10 L2 P1 X...
* G55 - Coordinate system 2 - set offsets using G10 L2 P2 X...
* G56 - Coordinate system 3 - set offsets using G10 L2 P3 X...
* G57 - Coordinate system 4 - set offsets using G10 L2 P4 X...
* G58 - Coordinate system 5 - set offsets using G10 L2 P5 X...
* G59 - Coordinate system 6 - set offsets using G10 L2 P6 X...
* G92 - Temporary offsets (G92.1, G92.2, G92.3, G92.4)
* G28.1 - Saves the absolute position and G28 returns to the saved position
* G30.1 - Saves the absolute position and G30 returns to the saved position
* G28.2 - Set absolute coordinates using homing cycle
* G28.3 - Set absolute coordinates using axis words in the gcode command
* G38.2 - Probe for a position

###Absolute Coordinate System
The coordinate systems and their offsets can be viewed in layers. The bottom layer is the **absolute** coordinate system aka **machine** coordinate system. This is always the G53 coordinate system, and can be selected by including G53 on the Gcode line. G53 is non-modal, meaning that it only applies to the line where it is present.

The absolute coordinate system can be set a few different ways. The G28.2 homing operation runs a homing cycle for the axes specified, at the end of which the absolute coordinate system will be (or is supposed to be) referenced to the homing switches. G28.2 is not an official Gcode - it's just something we implemented in TinyG.

G28.3 also sets absolute coordinates, but without running a homing cycle. It sets the axis or axes specified to the axis words provided. This is useful for infinite axes and other special cases. For example, a particular cardboard cutter has a pair of pinch rollers to feed cardboard in and out on the Y axis. Asking the user to insert the cardboard then press a button can send G28.3 Y0 to set the Y axis to zero at the edge of the cardboard. G28.3 is another TinyG-only code.

G28 and G30 also use absolute coordinates, as explained later.

###Coordinate Systems and Offsets
At all times other than when a G53 is active the machine will be in a coordinate system 1-6, which correspond to G54-G59. These support setting offsets on any or all axes using the G10 L2 command. If the offsets are zero this will be the same as the G53 system, but it's persistent, so you don't need to invoke it every time. It's common practice to leave the G54 offsets at zero so you have a persistent machine coordinate system available.

Another common practice os to set one of the coordinate systems to the middle of the work area, et;s say G55. If the table has dimensions of 200mm x 200m (XY) then issuing `G10 L2 P2 X100 Y100` will set coordinate system 2 (G55) to the middle of the XY table.



https://github.com/synthetos/TinyG/wiki/Gcode-Support#g28-g281-g30-g301-go-to-predefined-position
