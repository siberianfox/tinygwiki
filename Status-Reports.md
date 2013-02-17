## Status Reports
Status reports are parent/child objects with a "sr" parent and one or more child NV pairs. In the examples below the status report has been requested from the command line:
<pre>
Sending: {"sr":""}  returns:  
{"r":{"sr":{"line":0,"vel":0.000,"posx":-3.937,"posy":-3.937,"posz":0.000,"posa":0.000,"mpox":0.000,"mpoy":0.000,"mpoz":0.000,"mpoa":0.000,"ofsx":100.000,"ofsy":100.000,"ofsz":0.000,"ofsa":0.000,"unit":0,"momo":4,"coor":2,"stat":1,"homx":0,"homy":0,"homz":0,"homa":0},"f":[1,0,10,1755]}}

In text mode, '?' returns:
Line number:         0
Velocity:            0.000 in/min
X position:         -3.937 in
Y position:         -3.937 in
Z position:          0.000 in
A position:          0.000 deg
X machine posn:      0.000 mm
Y machine posn:      0.000 mm
Z machine posn:      0.000 mm
A machine posn:      0.000 deg
X work offset:     100.000 mm
Y work offset:     100.000 mm
Z work offset:       0.000 mm
A work offset:       0.000 deg
Units:               G20 - inches mode
Motion mode:         G80 - cancel motion mode (none active)
Coordinate system:   G55 - coordinate system 2
Machine state:       Reset
X axis homed:        0
Y axis homed:        0
Z axis homed:        0
A axis homed:        0
</pre>

Here are some examples of automatically generated status reports in JSON mode and text mode: 
<pre>
{"sr":{"posx":0.182,"posy":2.750,"posz":0.026,"mpox":104.620,"mpoy":169.839,"mpoz":0.663}}
{"sr":{"posx":0.147,"posy":2.225,"posz":0.021,"mpox":103.737,"mpoy":156.507,"mpoz":0.535}}
{"sr":{"posx":0.112,"posy":1.700,"posz":0.016,"mpox":102.854,"mpoy":143.176,"mpoz":0.407}}
{"sr":{"posx":0.078,"posy":1.175,"posz":0.011,"mpox":101.971,"mpoy":129.845,"mpoz":0.279}}
{"sr":{"vel":589.589,"posx":0.048,"posy":0.676,"posz":0.006,"mpox":101.132,"mpoy":117.173,"mpoz":0.158}}
{"sr":{"vel":395.886,"posx":0.018,"posy":0.279,"posz":0.002,"mpox":100.464,"mpoy":107.088,"mpoz":0.061}}
{"sr":{"vel":146.745,"posx":0.005,"posy":0.074,"posz":0.000,"mpox":100.119,"mpoy":101.874,"mpoz":0.011}}
{"sr":{"vel":16.305,"posx":0.001,"posy":0.022,"posz":-0.000,"mpox":100.031,"mpoy":100.549,"mpoz":-0.002}}
{"sr":{"vel":0.000,"posx":0.000,"posy":0.000,"posz":0.000,"mpox":100.000,"mpoy":100.000,"mpoz":0.000,"stat":3}}

line:0,vel:631.875,posx:19.886,posy:300.510,posz:2.886,posa:0.000,mpox:605.094,mpoy:7732.955,mpoz:73.307,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
line:0,vel:629.264,posx:19.922,posy:300.980,posz:2.891,posa:0.000,mpox:606.027,mpoy:7744.886,mpoz:73.420,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
line:0,vel:537.877,posx:19.960,posy:301.459,posz:2.895,posa:0.000,mpox:606.979,mpoy:7758.124,mpoz:73.545,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
line:0,vel:315.937,posx:19.987,posy:301.805,posz:2.899,posa:0.000,mpox:607.714,mpoy:7766.438,mpoz:73.624,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
line:0,vel:93.998,posx:19.999,posy:301.960,posz:2.900,posa:0.000,mpox:607.976,mpoy:7769.783,mpoz:73.656,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
line:0,vel:0.653,posx:20.001,posy:301.980,posz:2.900,posa:0.000,mpox:608.016,mpoy:7770.301,mpoz:73.661,mpoa:0.000,ofsx:100.000,ofsy:100.000,ofsz:0.000,ofsa:0.000,unit:0,momo:0,coor:2,stat:5,homx:0,homy:0,homz:0,homa:0
</pre>

## Enabling Status Reports
Status reports are enabled using the $sv and $si variables:
<pre>
{"sv":0} - 

Interval is set using $si

## Status Report Operations
The following operations can be performed: 

* GET an on-demand status report - this will return a single status report. The following forms are equivalent:
<pre>
{"sr":""}
{"sr":null}    the 'null' value is used instead of "" in this case. Either are accepted.
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