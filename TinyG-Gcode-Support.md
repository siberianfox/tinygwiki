_updated 2/24/13 - ash_

**NOTE: This page describes Gcode supported in release 0.95 and later**

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
	G28 | _axes_ | Go to G28.1 position | Optional axes specify an intermediate point
	G28.1 | | Set position for G28 | The current machine position is recorded (No parameters are provided)
	G28.2 | _axes_ | Homing Sequence | Homes all axes present in command. At least one axis must be specified
	G28.3 | _axes_ | Set Zero | Set axis to zero or other value. Use to zero axes that cannot otherwise be homed
	G30 | _axes_ | Go to G30.1 position | Optional axes specify an intermediate point
	G30.1 | | Set position for G30 | The current machine position is recorded (No parameters are provided)
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
	M2 | | Program end |
	M30 | | Program end |
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