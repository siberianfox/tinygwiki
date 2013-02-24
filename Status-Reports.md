## Overview
Status reports are a way to query the internal state of the machine (technically, the "dynamic Gcode model"). Status reports are for keeping a running tally of position and velocity for DRO displays or progress drawing, for knowing when the machine is running or complete, the current units mode or coordinate system, and other things like that.

### On-Demand Status Reports
Status reports can be requested from the command line:

	command line | description
	------|------------
	? | request a text mode status report
	$sr | equivalent to ?
	{"sr":""} | JSON status report
	{"sr":null} | a different way to request a JSON report
	{"sr":n} | shorthand for above

In text mode, '?' returns a multi-line report like this one:
<pre>
Line number:         0
X position:          0.010 mm
Y position:          0.000 mm
Z position:         -7.000 mm
A position:          3.000 deg
Feed rate:           0.000 mm/min
Velocity:            0.000 mm/min
Units:               G21 - millimeter mode
Coordinate system:   G54 - coordinate system 1
Distance mode:       G90 - absolute distance mode
Feed rate mode:      G94 - units-per-minute mode (i.e. feedrate mode)
Motion mode:         G1  - linear feed
Machine state:       End
tinyg [mm] ok> 
</pre>

JSON status reports are parent/child objects with a "sr" parent and one or more child name:value pairs. Here is the JSON form of the above report:
<pre>
{"r":{"sr":{"line":0,"posx":0.010,"posy":0.000,"posz":-7.000,"posa":3.000,"feed":0.000,"vel":0.000,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":1,"stat":3},"f":[1,0,9,588]}}
</pre>

### Automatic Status Reports
Status reports can also be set up to be run automatically on a minimum time interval using these settings:

	Setting | Description | Notes
	--------|-------------|-------
	$sv | Status report verbosity | 0=off, 1=filtered, 2=verbose
	$si | Status report interval | in milliseconds (50 ms minimum interval)

For now let's talk about verbose status reports $sv=2, and $si=100 (interval = 100 milliseconds). Automatic status reports will be generated on the following conditions, but will not be delivered any more frequently than once every 100 ms.
* When a Gcode command is received (e.g. motion start)
* During motion - but no more frequently that the status interval
* When motion stops

Here are some examples of automatically generated status reports in text mode from a g0x20 move: 
<pre>
line:0,posx:0.016,posy:0.000,posz:-7.000,posa:3.000,feed:0.000,vel:61.987,unit:1,coor:1,dist:0,frmo:0,momo:0,stat:5
line:0,posx:3.916,posy:0.000,posz:-7.000,posa:3.000,feed:0.000,vel:6059.247,unit:1,coor:1,dist:0,frmo:0,momo:0,stat:5
line:0,posx:16.094,posy:0.000,posz:-7.000,posa:3.000,feed:0.000,vel:6384.680,unit:1,coor:1,dist:0,frmo:0,momo:0,stat:5
line:0,posx:19.999,posy:0.000,posz:-7.000,posa:3.000,feed:0.000,vel:61.988,unit:1,coor:1,dist:0,frmo:0,momo:0,stat:5
line:0,posx:20.000,posy:0.000,posz:-7.000,posa:3.000,feed:0.000,vel:0.000,unit:1,coor:1,dist:0,frmo:0,momo:0,stat:3
</pre>

Here's the same sequence in JSON mode:
<pre>
{"sr":{"line":0,"posx":0.006,"posy":0.000,"posz":-7.000,"posa":3.000,"feed":0.000,"vel":62.008,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":0,"stat":5}}
{"sr":{"line":0,"posx":4.937,"posy":0.000,"posz":-7.000,"posa":3.000,"feed":0.000,"vel":6681.347,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":0,"stat":5}}
{"sr":{"line":0,"posx":16.570,"posy":0.000,"posz":-7.000,"posa":3.000,"feed":0.000,"vel":6061.269,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":0,"stat":5}}
{"sr":{"line":0,"posx":20.000,"posy":0.000,"posz":-7.000,"posa":3.000,"feed":0.000,"vel":0.000,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":0,"stat":3}}
</pre>

#### Filtered Status Reports
Filtered status reports are a very powerful way of keeping track of the internal machine state (gcode dynamic model) without requiring the UI to parse and interpret the Gcode file. Filtered status reports return only those values that have changed since the last status report. Here are text and JSON mode examples of the g0x20 move.

<pre>
posx:0.006,vel:62.008,stat:5
posx:3.907,vel:6061.269
posx:16.093,vel:6386.810
posx:20.000,vel:15.502
vel:0.000,stat:3
</pre>

<pre>
{"sr":{"posx":0.006,"vel":62.008,"stat":5}}
{"sr":{"posx":4.410,"vel":6386.810}}
{"sr":{"posx":16.093}}
{"sr":{"posx":20.000,"vel":15.502}}
{"sr":{"vel":0.000,"stat":3}}
</pre>

## Enabling Status Reports
Status reports are enabled using the $sv and $si variables:
<pre>
{"sv":0}   disable automatic status reports
{"sv":1}   enable filtered status reports
{"sv":2}   enable verbose status reports

{"si":50}  set minimum status report interval to 50 milliseconds
{"si":250} set minimum status report interval to 250 milliseconds (or whatever you want)

In text mode these are:
$sv=0
$sv=1
$sv=2
$si=50
$si=250
</pre>

## Status Report Operations
The following operations can be performed: 

* GET a status report. Returns a single status report. The following forms are equivalent:
<pre>
{"sr":""}
{"sr":null}    the 'null' value is used instead of "" in this case. Either are accepted.

These return a JSON status report in a response object:
{"r":{"sr":{"line":0,"vel":0.000,"posx":0.000,"posy":0.000,"posz":0.000,"posa":0.000,"mpox":100.000,"mpoy":100.000,"mpoz":0.000,"mpoa":0.000,"ofsx":100.000,"ofsy":100.000,"ofsz":0.000,"ofsa":0.000,"unit":1,"momo":0,"coor":2,"stat":3,"homx":0,"homy":0,"homz":0,"homa":0},"f":[1,0,10,5928]}}

In text mode entering '?' returns a status report in multi-line format:
Line number:         0
Velocity:            0.000 mm/min
X position:          0.000 mm
Y position:          0.000 mm
Z position:          0.000 mm
A position:          0.000 deg
X machine posn:    100.000 mm
Y machine posn:    100.000 mm
Z machine posn:      0.000 mm
A machine posn:      0.000 deg
X work offset:     100.000 mm
Y work offset:     100.000 mm
Z work offset:       0.000 mm
A work offset:       0.000 deg
Units:               G21 - millimeter mode
Motion mode:         G0  - linear traverse (seek)
Coordinate system:   G55 - coordinate system 2
Machine state:       End
X axis homed:        0
Y axis homed:        0
Z axis homed:        0
A axis homed:        0
</pre> 

* SET status report fields.  The elements to be included in a status reports may be specified by setting values to 'true' or 't'. The elements will be returned on subsequent SR requests in the order they were provided in the SET command. (I know, dictionaries are supposed to be unordered, but the firmware will record and return back the attributes in the order listed). There is no incremental setting of elements - all attributes are reset and must be specified in a single SET command. For example, the string below could be used to set up the status report in the example above, and eliminate any previously recorded settings. Note that the 'true' term is not in quotes - it is actually the JSON value for true, not a string that says "true". Examples:
<pre>
{"sr":{"line":true,"posx":true, "posy":true,"posz":true, "vel":true, "unit":true, "stat":true}}
{"sr":{"line":t,"posx":t, "posy":t,"posz":t, "vel":t, "unit":t, "stat":t}}
</pre> 

### Automatic Status Reports
Status reports may also be automatically generated by setting the status report interval to a non-zero value. Reports will be generated:
 * for each command received
 * during movement every N milliseconds
 * and at the end of each Gcode block that causes movement
 * when the machine has stopped (i.e. at the end of the final move in the buffer)

<pre>
{"si":0}      disable automatic status reports
{"si":200}    request status reports every 200 ms during movement
</pre> 

### Status Report Contents
A status report may contain one or more of the following attributes. The [token] name is in braces. Values are returned as numbers. 
<pre>

[vel]  velocity           - actual velocity - may be different than programmed feed rate 
[line] line_number        - either the Gcode line number (N word), or the auto-generated line count if N's are no present 
[feed] feed_rate          - gcode programmed feed rate (F word) 
[stat] machine_state      - 0=reset, 2=stop, 3=end, 4=run, 5=hold, 6=homing 
[unit] units_mode         - 0=inch, 1=mm
[coor] coordinate_system  - 0=g53, 1=g54, 2=g55, 3=g56, 4=g57, 5=g58, 6=g59
[momo] motion_mode        - 0=traverse, 1=straight feed, 2=cw arc, 3=ccw arc
[plan] plane_select       - 0=XY plane, 1=XZ plane, 2=YZ plane
[path] path_control_mode  - 0=exact stop, 1=exact path, 2=continuous
[dist] distance_mode      - 0=absolute distance, 1=incremental distance
[frmo] feed_rate_mode     - 0=units-per-minute-mode, 1=inverse-time-mode
[gc]   gcode_block        - gcode block currently being run

[posx] x work position    - X work position in prevailing units (mm or inch) 
[posy] y work position
[posz] z work position
[posa] a work position
[posb] b work position
[posc] c work position

[mpox] x machine position - X machine position in absolute coordinates in mm units ONLY
[mpoy] y machine position
[mpoz] z machine position
[mpoa] a machine position
[mpob] b machine position
[mpoc] c machine position

[ofsx] x machine offset - X machine offset to work position in absolute coordinates in mm units ONLY
[ofsy] y machine offset
[ofsz] z machine offset
[ofsa] a machine offset
[ofsb] b machine offset
[ofsc] c machine offset

[g92x] g92x - G92 origin offset for X axis
[g92y] g92y
[g92z] g92z
[g29a] g92a
[g92b] g92b
[g92c] g92c
</pre> 
Additionally, any valid token may be listed in a status report. For example, "g54x" will return the X offset in the G54 coordinate system (coordinate system #1). "fv" would return the firmware version. 

### Some Notes
The ofs_ reports are useful if you are using the status reports 