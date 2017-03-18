**THIS PAGE IS DEPRECATED. SEE HERE INSTEAD - [TinyG Status Reports](TinyG-Status-Reports)**

## Overview
Status reports are a way to query the internal state of the machine (technically, the "dynamic Gcode model"). Status reports are for keeping a running tally of position and velocity for DRO displays or progress drawing, for knowing when the machine is running or complete, the current units mode or coordinate system, and other things like that.

There are 2 ways to get a status report - to request one from the command line "On-Demand Status Reports", or they can be generated automatically.

### On-Demand Status Reports
Status reports can be requested from the command line:

command line | description
------|------------
? | request a text mode status report
$sr | equivalent to ?
{"sr":""} | JSON status report
{"sr":null} | a different way to request a JSON report

In text mode, '?' or $sr returns a multi-line report like this one:
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
Status reports can also be set up to be run automatically. A status report will be generated:
* every time a move ends (after each Gcode block)
* during movement every NNN milliseconds as set in the $si value
* when motion stops (STOP or END).

Also note that if the gcode blocks are very short and would cause status reports to be generated at a rate faster than the $si interval they will only be generated at the $si interval (i.e. they will never be sent faster than the $si setting).

Use these settings:

Setting | Description | Notes
--------|-------------|-------
$sv | Status report verbosity | 0=off (disabled), 1=filtered (see next section heading), 2=verbose (on all the time)
$si | Status report interval | in milliseconds (50 ms minimum interval)

NOTE: $si won't accept any values less than its minimum interval, which is 50 ms.<br>
NOTE: The $sv setting does not apply to on-demand requested status reports. $sr (or {"sr:""} ) will always return a full status report.

Here are some examples:
<pre>
{"sv":0}      disable automatic status reports
{"sv":1}      enable filtered status reports
{"sv":2}      enable verbose status reports
{"si":100}    set minimum status report interval to 100 milliseconds

In text mode these are:
$sv=0
$sv=1
$sv=2
$si=10
</pre>

Automatic status reports will be generated on the following conditions, but will not be delivered any more frequently than the minimum interval.
* When a Gcode command is received (e.g. motion start)
* During motion - but no more frequently that the status interval
* When motion stops

Here are some examples of automatically generated status reports in text mode from a g0x20 move with sv=verbose and si=100ms.
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

## Status Report Operations
The following operations can be performed:

### GET a status report
Returns a single status report. See discussion above for description.

### SET status report contents
You can customize the fields returned status reports by using the SET command. This must be done in JSON mode. JSON mode is detected by the leading curlie, so you could just enter either of these commands:
<pre>
{"sr":{"line":true,"posx":true, "posy":true,"posz":true, "vel":true, "unit":true, "stat":true}}
{"sr":{"line":t,"posx":t, "posy":t,"posz":t, "vel":t, "unit":t, "stat":t}}    (shorter version of above)
</pre>

The items to be included in a status reports are specified by setting their values to 'true' or 't'. The items will be returned in reports in the order they were provided in the SET command. There is no incremental setting of elements - all attributes are reset and must be specified in a single SET command. Limits are:
* maximum number of items is 24
* maximum length of input string is 254 characters

By the way, to return to text mode just enter a non-JSON, non-Gcode command, such as ? or $

## Status Report Contents
A status report may contain one or more of the following attributes. The [token] name is in braces. Values are returned as numbers.
<pre>
[vel]  velocity           - actual velocity - may be different than programmed feed rate
[line] line_number        - either the Gcode line number (N word), or the auto-generated line count if N's are no present
[feed] feed_rate          - gcode programmed feed rate (F word)
[stat] machine_state      - 0=initializing, 1=ready, 2=shutdown, 3=stop, 4=end, 5=run, 6=hold, 9=homing
[unit] units_mode         - 0=inch, 1=mm
[coor] coordinate_system  - 0=g53, 1=g54, 2=g55, 3=g56, 4=g57, 5=g58, 6=g59
[momo] motion_mode        - 0=traverse, 1=straight feed, 2=cw arc, 3=ccw arc
[plan] plane_select       - 0=XY plane, 1=XZ plane, 2=YZ plane
[path] path_control_mode  - 0=exact stop, 1=exact path, 2=continuous
[dist] distance_mode      - 0=absolute distance, 1=incremental distance
[frmo] feed_rate_mode     - 0=units-per-minute-mode, 1=inverse-time-mode
[hold] hold_state         - 0=off, 1=sync, 2=plan, 3=decel, 4=holding, 5=end-hold

[posx] x work position    - X work position in prevailing units (mm or inch)
[posy] y work position        Reports position in current coordinate system
[posz] z work position        and with any G92 offsets in effect
[posa] a work position
[posb] b work position
[posc] c work position

[mpox] x machine position - X machine position in absolute coordinates in mm units ONLY
[mpoy] y machine position     Reports the internal canonical position which is
[mpoz] z machine position     always in mm and absolute machine coordinates (G53)
[mpoa] a machine position     Note that no coordinate systems or g92 offsets are applied
[mpob] b machine position
[mpoc] c machine position

[ofsx] x machine offset   - X machine offset to work position in absolute coordinates in mm units ONLY
[ofsy] y machine offset       Reports offsets in mm and absolute machine coordinates
[ofsz] z machine offset
[ofsa] a machine offset
[ofsb] b machine offset
[ofsc] c machine offset

[g54x] g54x               - G54 coordinate system offset for X axis
[g54y] g54y                   Coordinate system offsets may be reported
[g54z] g54z
...
[g59a] g59a
[g59b] g59b
[g59c] g59c

[g92x] g92x               - G92 origin offset for X axis
[g92y] g92y                   The number returned can be a bit brain bending as you have to back out
[g92z] g92z                   the position from which the G92 was set, but this is the actual offset
[g29a] g92a                   value; and may be different from the value entered in the G92 command.
[g92b] g92b
[g92c] g92c
</pre>
Additionally, any valid token may be listed in a status report. For example, "g54x" will return the X offset in the G54 coordinate system (coordinate system #1). "fv" would return the firmware version.

### Status Report Values
Values commonly reported in status reports. See also canonical_machine.h for the actual code.

Token | Value | Description
------|-------|-------------
stat || Machine State
| 0 | machine is initializing
| 1 | machine is ready for use
| 2 | machine is in alarm state (shut down)
| 3 | program stop or no more blocks
| 4 | program end via M2, M30, M60
| 5 | motion is running
| 6 | motion is holding
| 7 | probe cycle active
| 8 | machine is running (cycling)
| 9 | machine is homing
momo | | Motion Mode - from Gcode
| 0 | G0 - straight traverse
| 1 | G1 - straight feed
| 2 | G3/G4 - arc traverse
| 3 | G80 - cancel motion mode (no motion mode active)

## Random Notes
* The reason the machine position (mpo) is in internal coordinates is to return a clean representation of the internal position model. This allows the status report output to be used directly to chart tool path on a graphics window. This way the drawing model ignores things like units changes, coordinate systems and g92 offsets.
* You can get the actual work position for display in a DRO by subtracting the offset from the machine position. So a convenient way to set up a status report is to use filtered reports to return mpox, mpoy, mpoz, ofsx, ofsy, ofsz. You will get back the xyz positions that are actually moving. The offsets will be reported once at the start of the status reports, and then only if they are changed by a gcode command. The offsets can be queried at any time by issuing a {"sr":""}, which will always return a complete SR regardless of the $sv setting

## Status Reports for Diagnostics
Status reports can be a good way to report back diagnostics when developing and debugging. In reason is that embedding printf() statements in code (especially in interrupts) can choke the system and cause it to lock up unless you are really careful. Status reports have a mechanism to manage bandwidth that you can take advantage of.

The convention for adding diagnostic parameters to status reports is to lead with an underscore. This makes mnemonics somewhat tortured, but at least it keeps them out of the main parameter namespace. Here are a few that have been defined as diagnostics for mp_exec_line().

group | example | description
------|-------|-------------
_te | _tex | Target endpoint value for axis: mr.target[axis]
_tr | _trx | Target runtime value for axis: mr.gm.target[axis]
_ts | _ts1 | Target motor steps: mr.target_steps[motor]
_ps | _ps1 | Position motor steps: mr.position_steps[motor]
_ns | _ns1 | Encoder steps: mr.encoder_steps[motor]
_es | _es1 | Encoder error in steps: &mr.encoder_error[motor]

These will show up as JSON in status reports or can be invoked from the command line individually or in groups. Examples: `$_te` or `$_tex`
