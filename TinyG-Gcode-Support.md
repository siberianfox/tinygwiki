_updated 6/24/13 - ash_

**NOTE: This page describes Gcode supported in release 0.95 and later**

See also:
* [Feedhold and Cycle Start (Pause and Resume)](https://github.com/synthetos/TinyG/wiki/TinyG-Feedhold-and-Resume)
* [Homing and Limits](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Description-and-Operation)

TinyG implements the NIST RS274v3/ngc dialect of Gcode. We try to adhere as closely as possible to the NIST Gcode and LinuxCNC Gcode specifications:
* [Kramer's NIST RS274NGCv3 Gcode Specification](http://technisoftdirect.com/catalog/download/RS274NGC_3.pdf)
* [LinuxCNC Gcode Specification](http://www.linuxcnc.org/docs/2.4/html/gcode_main.html)<br>

##Cheat Sheet
This table summarizes Gcode supported. _axes_ means one or more of X,Y,Z,A,B,C. 

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G0 | _axes_ | Straight traverse | Traverse at maximum velocity. At least one axis must be present
	G1 | _axes_, F | Straight feed | Feed at feed rate F. At least one axis must be present
	G2 | _axes_, F, I,J,K or R | Clockwise arc feed | Arc at feed rate F. Offset mode IJK or radius mode R
	G3 | _axes_, F, I,J,K or R | Counter clockwise arc feed | Arc at feed rate F. Offset mode IJK or radius mode R
	G4 | P | Dwell | Pause for P seconds
	G10 L2 | _axes_, P | Set offset parameters | P selects coordinate system 1-6
	G17 | | Select XY plane | G17, G18 and G19 set the plan in which the G2/G3 arcs are drawn
	G18 | | Select XZ plane |
	G19 | | Select YZ plane |
	G20 | | Select inches units mode |
	G21 | | Select mm units mode |
	[G28](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#g28-g281-g30-g301-go-to-predefined-position) | _axes_ | Go to G28.1 position | Optional axes specify an intermediate point
	[G28.1](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#g28-g281-g30-g301-go-to-predefined-position) | | Set position for G28 | The current machine position is recorded (No parameters are provided)
	G28.2 | _axes_ | Homing Sequence | Homes all axes present in command. At least one axis must be specified
	G28.3 | _axes_ | Set Zero | Set axis to zero or other value. Use to zero axes that cannot otherwise be homed
	[G30](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#g28-g281-g30-g301-go-to-predefined-position) | _axes_ | Go to G30.1 position | Optional axes specify an intermediate point
	[G30.1](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#g28-g281-g30-g301-go-to-predefined-position) | | Set position for G30 | The current machine position is recorded (No parameters are provided)
	G53 | | Select absolute coordinates | Non-Modal: Applies only to current block
	G54 | | Select coord system 1 | G54 is typically used as the "normal" coordinate system and reflects the machine position
	G55 | | Select coord system 2 |
	G56 | | Select coord system 3 |
	G57 | | Select coord system 4 |
	G58 | | Select coord system 5 |
	G59 | | Select coord system 6 |
	G61 | | Exact stop mode | Motion will stop between each Gcode block
	G61.1 | | Exact path mode | Motion will be blended between Gcode blocks - exact path will be traced
	G64 | | Continuous path mode | Motion will be blended between Gcode blocks - may deviate from exact path
	G80 | | Cancel motion mode |
	G90 | | Set absolute mode |
	G91 | | Set incremental mode |
	G92 | _axes_ | Set origin offsets |
	G92.1 | | Reset origin offsets |
	G92.2 | | Suspend origin offsets |
	G92.3 | | Resume origin offsets |
	G93 | | Set inverse feedrate mode |
	G94 | | Cancel inverse feedrate mode |

 	Mcode | Parameter |Command | Description
	------|-----------|--------|-------------
	M0 | | Program stop |
	M1 | | Program stop | Optional program stop switch is not implemented so M1 is equivalent to M0
	[M2](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#m2-m30-program-end) | | Program end |
	[M30](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support#m2-m30-program-end) | | Program end |
	M60 | | Program stop |
	M3 | S | Spindle on - CW | S is speed in RPM
	M4 | S | Spindle on - CCW | S is speed in RPM
	M5 | | Spindle off |
	M6 | | Change tool | No operation at this time
	M7 | | Mist coolant on | Note that mist and flood share the same Coolant ON/OFF pin
	M8 | | Flood coolant on | Note that mist and flood share the same Coolant ON/OFF pin
	M9 | | All coolant off | Note that mist and flood share the same Coolant ON/OFF pin

 	Other | Parameter |Command | Description
	------|-----------|--------|-------------
	N | line number | label gcode block | Line numbers are allowed, handled, and may be reported back in status reports. Don't underestimate how useful this is for debugging Gcode files.
	() | comment | gcode comment | Gcode comments are allowed. They are stripped and ignored, except for messages (below)
	(msg....) | message | gcode message | Gcode messages are comments that begin with the characters `msg` (case insensitive). These will be echoed to the operator 


#Gcode Reference
Some of the more complicated commands are described here. Much of this is shamelessly cribbed from the [LinuxCNC Gcode pages](http://www.linuxcnc.org/docs/2.4/html/gcode_main.html)<br>

##G28, G28.1, G30, G30.1 Go To Predefined Position
G28 (and G30) will move the machine to the absolute coordinates set in G28.1 (G30.1), optionally through an intermediate point.  Movement will occur at the traverse rate (G0 rate). Axes that are not specified are ignored (not moved). The axis value is the intermediate point for that axis. 

Format is:
<pre>G28 X0 Y0 Z0 A0 B0 C0</pre> 

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G28 | _axes_ | Go to G28.1 position | Goes through intermediate point if _axes_ are present
	G28.1 | | Set position for G28 | Axes are not used and are ignored if present
	G30 | _axes_ | Go to G30.1 position | Goes through intermediate point if _axes_ are present
	G30.1 | | Set position for G30 | Axes are not used and are ignored if present

Example of use: 
* Go to an arbitrary position, e.g. G0 x100 y100 
* Send G28.1  - This will "remember" the absolute position. This position remains constant regardless of what coordinate system is in effect. 
* Then go to a gifferent place, e.g. G0 x50 y50
* Send G28  - The machine will return to x100 y100

Example:
* Go back to X0Y0
* Send G91 G28 Z10 - this will move to x100 y100. The tool will initially lift z by 10 mm (or inches); G91 is used to set relative mode for this command. 


### Homing Operation
## G28.2 - Homing Sequence (Homing Cycle)
G28.2 is used to home to physical homing switches. G28.2 will find the home switch for an axis then set machine zero for that axis at an offset from the switch location. Format is: 
<pre>G28.2 X0 Y0 Z0 A0 B0 C0</pre>
Axes not present are ignored. The zero values are not meaningful but each axis present must have a number value.

For example. G28.2 X0 Y0 will home the X and Y axes only. The values provided for X and Y don't matter, but something must be present.

* G28.2 homes all axes present in the command 
 * The homing sequence progresses through each axis provided in the G28.2 block in turn - i.e. it does not home on multiple axes simultaneously. 
 * Axes are always executed in order of ZXYABC. The order the axis words occur in the G28.2 block has no effect. 
* Homing begins by checking the pre-conditions for homing
 * $xSV homing-search-velocity must be non-zero for the axis to be processed
 * Switches must be configured correctly - one and only one homing switch per axis
* The following is performed for each specified axis:
 * Homing begins by testing homing and limit switches for the currently homing axis. If a switch is tripped the axis will back off the switch by the Latch Backoff ($xLB) distance.
 * Once the switches are cleared a search move is executed. The search will travel at the Search Velocity ($xSV) for Travel Maximum ($xTM) distance towards the homing switch. The search runs until the homing switch is hit or the total travel is performed.
 * Once the switch is hit a Latch Backoff move is performed. This backs off the switch until the switch opens again. 
 * Once the switch is cleared the axis moves further off the sthe switch by the the Zero Backoff amount and sets zero for that axis.
* Once all axes are processed the affected axes are moved to the absolute home location (machine zero). At this point the homing state will indicate that the machine has been homed. Homing state can also be read using the homing group: $hom. This returns 0 or 1 for each axis to indicate homing state for each axis

## G28.3 - Set Zero 
G28.3 allows you to set a zero (or other value) for any axes. Some axes cannot be homed. They either don't have switches, are infinite axes like rollers on the Othercutter, or some other reason. Do the following to set a zero - for example:
<pre>
g28.3 y0
</pre> 

G28.3 also supports setting to non-zero values, if that's useful. G28.3 affects the $hom group - any axis set by g28.3 is considered set for $hom


##M2, M30 Program End
program END (M2, M30) performs the following actions:

* Axis offsets are set to G92.1 CANCEL offsets
* Set default coordinate system (uses $gco)
* Selected plane is set to default plane ($gpl)
* Distance mode is set to MODE_ABSOLUTE (like G90)
* Feed rate mode is set to UNITS_PER_MINUTE (like G94)
* The spindle is stopped (like M5)
* Motion mode is canceled like G80 (not set to G1)
* Coolant is turned off (like M9)
* Default INCHES or MM units mode is restored ($gun)