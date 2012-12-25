(last updated 12/23/12 - ash) 

**NOTE: This page describes Gcode supported in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

# Gcode Cheat Sheet
This table summarizes Gcode supported. _axes_ means one or more of X,Y,Z,A,B,C. 

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G0 | _axes_ | Straight traverse | Traverse at maximum velocity. At least one axis must be present
	G1 | _axes_, F | Straight feed | Feed at feed rate F. At least one axis must be present
	G2 | _axes_, F, I,J,K or R | Clockwise arc feed | Arc at feed rate F. Offset mode IJK or radius mode R. At least one axis must be present
	G3 | _axes_, F, I,J,K or R | Counter clockwise arc feed | Arc at feed rate F. Offset mode IJK or radius mode R. At least one axis must be present
	G4 | P | Dwell | Pause for P seconds
	G10 L2 | _axes_, P | Set offset parameters | P selects coordinate system 1-6
	G17 | | Select XY plane |
	G18 | | Select XZ plane |
	G19 | | Select YZ plane |
	G20 | | Select inches units mode |
	G21 | | Select mm units mode |
	G28 | _axes_ | Go to G28.1 position | Optional axes specify an intermediate point
	G28.1 | | Set position for G28 | Axis words are not provided for this command
	G28.2 | _axes_ | Homing cycle | Homes all axes present in command. At least one axis must be present. Axis value is ignored.
	G28.3 | _axes_ | Set machine origin | Set axes specified. Useful for zeroing and setting origins in an case axis cannot be homed
	G30 | _axes_ | Go to G30.1 position | Optional axes specify an intermediate point
	G30.1 | | Set position for G30 | Axis words are not provided for this command
	G53 | | Select absolute coordinate system | Applies only to current block
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

 	Mcode | Parameter |Command | Description
	------|-----------|--------|-------------
	M0 | | Program stop |
	M1 | | Program stop |
	M2 | | Program end |
	M30 | | Program end |
	M60 | Program end |
	M3 | S | Spindle on - CW | S is speed in RPM
	M4 | S | Spindle on - CCW | S is speed in RPM
	M5 | | Spindle off |
	M6 | | Change tool | No operation at this time
	M7 | | Mist coolant on |
	M8 | | Flood coolant on |
	M9 | | All coolant off |


