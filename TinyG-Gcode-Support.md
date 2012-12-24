(last updated 12/23/12 - ash) 

**NOTE: This page describes Gcode supported in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

# Gcode Cheat Sheet
This table summarizes Gcode supported. _axes_ means one or more of X,Y,Z,A,B,C. At least one axis must be present or it's an error

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G0 | _axes_ | Straight traverse | Traverse at maximum velocity to 10,20 
	G1 | _axes_ F | Straight feed | 
	G2 | _axes_ I,J,K or R,P | Clockwise arc feed |
	G3 | _axes_ I,J,K or R,P | Counter clockwise arc feed |
	G4 | P | Dwell | P is time in seconds
	G10 L2 | P, _axes_| Set offset parameters | P selects coordinate system 1-6
	G17 | | Select XY plane |
	G18 | | Select XZ plane |
	G19 | | Select YZ plane |
	G20 | | Select inches units mode |
	G21 | | Select mm units mode |
	G28 | _axes_ | Go to predefined position as set via G28.1 | Optional axes will specify an intermediate point
	G28.1 | _axes_ | Set to predefined position for G28 |
	G28.2 | _axes_ | Initiate machine homing cycle | Home all axes present in command
	G28.3 | _axes_ | Set machine origin | Set any axes specified. Useful for zeroing and setting origins in an case axis cannot be homed
	G30 | _axes_ | Go to predefined position as set via G30.1 | Optional axes will specify an intermediate point
	G30.1 _axes_ | | Set to predefined position for G30 |
	G53 | | Select absolute coordinate system | Non-model - only applies to current block
	G54 | | Select coordinate system 1 |
	G55 | | Select coordinate system 2 |
	G56 | | Select coordinate system 3 |
	G57 | | Select coordinate system 4 |
	G58 | | Select coordinate system 5 |
	G59 | | Select coordinate system 6 |
	G61 | | Exact path mode |
	G61.1 | | Exact stop mode |
	G64 | | Continuous path mode |
	G80 | | Cancel motion mode |
	G90 | | Set absolute mode |
	G91 | | Set incremental mode |
	G92 | _axes_ | Set origin offsets |
	G92.1 | | Reset origin offsets |
	G92.2 | | Suspend origin offsets |
	G92.3 | | Resume origin offsets |
	G93 | | Set inverse feedrate mode |
	G94 | | Cancel inverse feedrate mode |

 	Mcode | Command | Description
	---------|--------------|-------------
	M0 | Program stop |
	M1 | Program stop |
	M2 | Program end |
	M30 | Program end |
	M60 | Program end |
	M3 | Spindle on - CW |
	M4 | Spindle on - CCW |
	M5 | Spindle off |
	M6 | Change tool | No operation at this time
	M7 | Mist coolant on |
	M8 | Flood coolant on |
	M9 | All coolant off |



