(last updated 12/23/12 - ash) 

**NOTE: This page describes Gcode supported in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

# Gcode Cheat Sheet
This table summarizes Gcode supported

	Gcode | Example | Description
	---------|--------------|-------------
	G0 | g0 x10 y20 | Traverse at maximum velocity to 10,20 
	{"xvm":15000} | {"b":{"xvm":15000},"f":[1,0,14,9253]}<nl>| set X axis maximum velocity to 15000
	{"x":{"vm":""}} | {"b":{"x":{"vm":16000}},"f":[1,0,16,2128]}<nl>| alternate form to get X axis maximum velocity
