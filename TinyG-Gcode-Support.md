(last updated 12/23/12 - ash) 

**NOTE: This page describes Gcode supported in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

# Gcode Cheat Sheet
This table summarizes Gcode supported

	Gcode | Command | Description
	---------|--------------|-------------
	G0 | Straight traverse | Traverse at maximum velocity to 10,20 
	G1 | Straight feed | 
	G2 | Clockwise arc feed | 
	G3 | Counter clockwise arc feed | 
	G4 | Dwell | 
	G10 L2 | Set offset parameters | 
	G17 | Select XY plane |
	G18 | Select XZ plane |
	G19 | Select YZ plane |
	G20 | Select inches units mode |
	G21 | Select mm units mode |
	G28 | Go to predefined position as set via G28.1 | Option axes will specify an intermediate point
	G28.1 | Set to predefined position for G28 |
	G28.2 | Initiate machine homing cycle |
	G28.3 | Set machine origin | Useful for zeroing and setting one or more origins in case axis cannot be homed
 
