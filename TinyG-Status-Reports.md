# Status Reports in JSON
Status reports return the dynamic gcode model state. In plain English, they tell you where you are. And other stuff as well. This is useful for the user or the UI to know what's happening in the machine. For example, hitting '?' from the command line will return the current XYZ position, the inches/mm units mode, and some other relevant data.

Status reports can also be automatically generated during movement so you can see move progress. 

The following variables can be reported in a status report

	Request | Response | Description
	---------|--------------|-------------
	n | line_number | Gcode line number (N word)
	vel | velocity | actual velocity - may be different than programmed feed rate 
	feed | feed_rate          | gcode programmed feed rate (F word) 
	stat | machine_state      | 1=reset, 2=shutdown, 3=stop, 4=end, 5=run, 6=hold, 9=homing 
	unit | units_mode         | 0=inch, 1=mm
	coor | coordinate_system  | 0=g53, 1=g54, 2=g55, 3=g56, 4=g57, 5=g58, 6=g59
	momo | motion_mode        | 0=traverse, 1=straight feed, 2=cw arc, 3=ccw arc
	plan | plane_select       | 0=XY plane, 1=XZ plane, 2=YZ plane
	path | path_control_mode  | 0=exact stop, 1=exact path, 2=continuous
	dist | distance_mode      | 0=absolute distance, 1=incremental distance
	frmo | feed_rate_mode     | 0=units-per-minute-mode, 1=inverse-time-mode
	gc | gcode_block        | gcode block currently being run

The following are available for all axes, XYZABC. Only `pos` is expanded for illustration

	Request | Response | Description
	---------|--------------|-------------
	posx | x work position | X work position in prevailing units (mm or inch) 
	posy | y work position
	posz | z work position
	posa | a work position
	posb | b work position
	posc | c work position
	mpox | x absolute position | X machine position in absolute coordinate system (mm or inch).
	g92x | offset | G92 origin offset for X axis
	g54x | coord system 1 offset | X axis G54 coordinate system offset
	g55x | coord system 2 offset | X axis G55 coordinate system offset
	g56x | coord system 3 offset | X axis G56 coordinate system offset
	g57x | coord system 4 offset | X axis G57 coordinate system offset
	g58x | coord system 5 offset | X axis G58 coordinate system offset
	g59x | coord system 6 offset | X axis G59 coordinate system offset
	g28x | G28 home position | X axis G28 home position
	g30x | G30 home position | X axis G30 home position

It's worth noting that any gettable variable can be put in a status report - the above variables are listed as they represent the Gcode model state. For example `fv` would return the firmware version.

It's also worth noting that any variable can be independently queried as an individual variable, and axis variables (e.g. pos, G55) can be queried as a group, e.g. `pos`, or `g55`

## Text Mode Status Reports
### On-Demand Text Mode Status Report
Enter a '?' to get a status report in text mode.

### Automatic Text Mode Status Reports
Automatically generated status reports may enabled with `$sv=1` or `$sv=2`. Reports will be generated for each new command entered, during movement every N milliseconds, and when the machine has stopped (i.e. at the end of the final move in the buffer).

Filtered status reports (`$sv=1`) return only variables that have changed since the previous report. This saves serial bandwidth and host processing time.
<pre>
$sv=0      turn off automatic status reports
$sv=1      turn on filtered automatic status reports
$sv=2      turn on unfiltered automatic status reports
$si=200    request status reports every 200 ms during movement
</pre> 

## JSON Mode Status Reports
JSON mode status reports are parent/child objects with a "sr" parent and one or more child NV pairs, as in the example below.<br> 

    {"sr": {"line":1245, "posx":23.4352, "posx":-9.4386, "posx":0.125, "vel":600, "unit":"1", "stat":"5"}"f":[1,0,19,2131]}}

### Set Status Report Fields
This sets the variables reported in a status report. This command is only supported in JSON mode, but the fields set in JSON apply to both JSON and text mode reports.

Variables to be included in a status reports are selected by setting values to 'true'. These variables will be returned on subsequent SR requests in the order they were provided in the SET command. There is no incremental setting of variables - all variables are reset and must be specified in a single SET command. 

For example, the string below could be used to set up the status report in the example above, and eliminate any previously recorded settings. Note that the 'true' term is not in quotes - it is actually the JSON value for `true`, not a string that says "true". Example:
<pre>
{"sr":{"line":true,"posx":true, "posy":true,"posz":true, "vel":true, "unit":true, "stat":true}}
</pre> 

### JSON On-Demand Status Report
This will return a single status report. The two forms below are equivalent.
<pre>
{"sr":""}
{"sr":null}         the `null` value is used instead of "" in this case. Either are accepted.
</pre> 

### JSON Automatic Status Reports
Automatically generated status reports may enabled with {"sv":1} or {"sv":2}. Reports will be generated for each new command entered, during movement every N milliseconds, and when the machine has stopped (i.e. at the end of the final move in the buffer).

Filtered status reports `{"sv":1}` return only variables that have changed since the previous report. This saves serial bandwidth and host processing time.
<pre>
{"sv":0}      turn off automatic status reports
{"sv":1}      turn on filtered automatic status reports
{"sv":2}      turn on unfiltered automatic status reports
{"si":200}    request status reports every 200 ms during movement
</pre> 