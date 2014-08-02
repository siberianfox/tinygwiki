This page discusses G20/G21 units mode selection and related topics. It explains how things work and what to expect.

###G20 / G21 - Set Units Mode
Basically:
* G20 will cause all gcode from this point onwards to be interpreted in inches
* G21 will cause all gcode from this point onwards to be interpreted in millimeters

The current units mode setting is referred to as the 'prevailing units mode'. The prevailing units mode will remain in effect until:
* It is changed by another G20 or G21 command
* An M2 or M30 Program END is hit. At this point it will revert to the Default units ($gun). Note that a Program STOP (M0, M1) does not change the units mode.

The units mode affects all the following:
* Gcode execution, including:
  * XYZ axis positions in gcode programs (ABC are in degrees regardless of unit mode).
  * Feed rate in gcode programs (F words)
  * Offsets and coordinates - whether enterd via G10 or $g54 or similar)
* Status reports returned by gcode execution
* Configuration settings including:
  * Almost all axis settings
  * Travel per revolution motor setting {1tr:...} 
  * Junction acceleration {ja:...}
  * Chordal tolerance {ct:...}

###$GUN, {gun:_} - Set Default Units Mode
The GUN setting sets the default (starting) units mode on power-on or reset. Gun does not change the units mode on the board if it is changed. It only affects the mode during power on or reset. 

###Command Input and Echo
Commands in text mode and JSON mode accept values in and respond back in the prevailing units mode. 

###Status Reports
Status reports return all values in the prevailing units mode with the following exception.

The machine positions values (mpox, mpoy ...mpoc) are always in millimeter units and always in the machine absolute coordinate system (G53). This is so that hosts and UI's can always have a stable position reference for display generation regardless of the G2/G21 setting or the coordinate system and offsets selected (G54-G59, G92). 

In order to interpret the Mpos values back to work coordinates (say for DRO display), the status reports should also return the units (unit) and offsets (ofsx, ofsy ... ofsc) for the axes being monitored.
