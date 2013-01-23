(last updated 1/23/13 - ash) 

**This page is really a continuation of [this page](https://github.com/synthetos/TinyG/wiki/JSON-Operation)**

**NOTE: This page describes JSON operations in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

#JSON Details
##JSON Mode Protocol
### Commands and Responses
Commands in JSON mode are sent as JSON objects. Some request and response examples:

    {"xfr":""}        {"r":{"xfr":12000.000},"f":[1,0,14,3009]}   get the X axis max feed rate
    {"xfr":12000}     {"r":{"xfr":12000.000},"f":[1,0,14,3009]}   set the X maximum feed rate to 12000 mm/min (assuming the system is in G21 mode)
    {"x":""}          {"r":{"x":{"am":1,"vm":16000.000,....       get all configuration settings for the X axis
    {"gc":"g0 x100"}  {"r":{"gc":"g0x100"},"f":[1,0,17,9360]}     execute the Gcode block "G0 X100"

    where  "f":[1,0,255,1234]  
       is  "f":[<protocol_version>, <status_code>, <input_available>, <checksum>]

Responses are wrapped in a response body and include a footer. Requests and responses are a single line of text terminated by CR, LF or CR/LF depending on the board configuration settings ($ec, $el). Requests and responses can be up to 256 characters long but are generally much shorter. <br>

The footer contains a 4 element array:

	Element  | Notes
	-------|-------------------------
	array_ID/version | Initially set to 1. This number identifies the array and version for the parser. It will be incremented if protocol changes are made that would affect hosts/clients
	status_code | 0 means the command executed `OK`. All others are exceptions. See [Status Codes](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Codes) for details.
	RX_received | Indicates how many characters were removed from the serial RX buffer to process this command. This allows the host to keep a running total of the bytes available in the TinyG RX buffer. The RX buffer starts with 254 bytes free. The terminating `<LF>` or `<CR>` counts as a byte.
	checksum | A hashcode checksum for the line; typically 4 digits but may be anything from 1 to 4 digits. The checksum is generated for the JSON line up to but not including the comma preceding the checksum itself. I.e, the comma is where the nul termination would exist. The checksum is computed as a [Java hashcode](http://en.wikipedia.org/wiki/Java_hashCode(\)) from which a modulo 9999 is taken to limit the length to no more than 4 characters. See compute_checksum() in util.c for C code.

### Response Verbosity
Character echo ($ee) is always an option; it's just not a good one for JSON. In JSON mode it should be turned off (  $ee=0, or {"ee":0}  ). It's a matter of preference for text mode.

JSON verbosity ($jv) sets the level of verbosity in JSON responses. It is set by {"jv":N} where N is one of:

* 0 = SILENT - no response
* 1 = FOOTER_ONLY - no body data is returned, only footer data
* 2 = OMIT_GCODE_BODY - body returned for configs; omitted for Gcode commands
* 3 = GCODE_LINENUM_ONLY - body returned for configs; Gcode returns line number as 'n', otherwise body is omitted
* 4 = GCODE_MESSAGES - body returned for configs; Gcode returns line numbers and messages only
* 5 = VERBOSE - body returned for configs and Gcode - Gcode comments removed

Generally levels 2, 3 or 4 are recommended. Levels 4 and 5 will return Gcode comment messages of the form (msgSome text you want to show to the operator)

###Reading Configuration Parameters (GET)
To get a parameter pass an object with a null value. The value is returned in the response. Some examples of valid requests and responses are provided below. 

	Request | Response | Description
	---------|--------------|-------------
	{"xvm":""} | {"r":{"xvm":16000},"f":[1,0,11,1301]}<nl>| get X axis maximum velocity
	{"x":{"vm":""}} | {"r":{"x":{"vm":16000}},"f":[1,0,16,2128]}<nl>| alternate form to get X axis maximum velocity
	{"x":""} | {"r":{"x":{"am":1,"vm":16000.000,"fr":16000.000,.... | get entire X axis group

###Setting Configuration Parameters (PUT)
To set a parameter pass an object with the value to be set. The value applied is returned in the response. The response value may be different than the requested valued in some cases. For example, an attempt to set a status report interval less than the minimum will return the minimum interval. Trying to set a read-only value will return that value; for example, firmware version. In some other cases a value of 'false' will be returned. The following are examples of valid set commands. (Nevermind the multi-line responses, these are just an artifact of the table. All requests and responses are on a single line of text)

	Request | Response | Description
	---------|--------------|-------------
	{"xvm":15000} | {"r":{"xvm":15000},"f":[1,0,14,9253]} | set X axis maximum velocity to 15000
	{"x":{"vm":15000}} | {"r":{"x":{"vm":15000},"f":[1,0,19,2131]}} | alternate form to set X axis maximum velocity to 15000
	{"si":250} | {"r":{"si":250.000},"f":[1,0,19,2131]} | Set status minimum interval to 250 ms. 
	{"si":10} | {"r":{"si":50.000},"f":[1,0,19,2131]} | Attempt to set status interval to 10 ms, but minimum was 50, so that's what stuck
	{"fv":2.0} | {"r":{"fv":0.950},"f":[1,0,19,2131]} | Whilst you may want a version 2.0 to magically appear the firmware remains at version 0.95 :(

### Status messages
Status messages are end-user text associated with each status code, similar to those on the [Status Codes](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Codes) page. JSON does not return these whereas text mode does. JSON parsers must therefore work to the status codes, and provide their own end-user messages.

**HOWEVER**<br>
There are 2 cases where messages are returned in responses

* System generated advisory messages may be returned as "msg" tags for some advisory cases. For example, TinyG will accept a microstep value that the chips don't support so that people who wire up TinyG to run external steppers can still set non-standard microstep values.

* Gcode provided messages. A Gcode block can have a comment that starts with the string "msg". For example, (msgChange tool and hit cycle start) will return "Change tool and hit cycle start" as data for a "msg" tag. Presumably the UI would want to display this message.

### Gcode line numbers 
Gcode line numbers are returned as `n` objects if they are provided in a Gcode file - i.e. the line starts with Nxxxx where xxxxx is a number. An example is:

    N20 G1 F240 X2.01 Y2.99       returns:
    "n":20 in the response

## Gcode in JSON Mode
In JSON mode Gcode blocks may be provided either as native gcode text or wrapped in JSON as a "gc" object. In either case responses will be returned in JSON format. A JSON wrapper may only contain a single gcode block.<br> 
<pre>Gcode requests
     {"gc":"g0 x100"}                                               wrapped input
     g0 x100                                                        unwrapped input
     {"gc":"g0 x100 (Initial move)"}                                wrapped input with a Gcode comment (see note)
     {"gc":"m0 (MSGChange tool and hit Cycle Start when done)"}     stop with a message

Gcode responses
     {"r":{"gc":"G0X100"},"f":[1,0,19,2131]}
     {"r":{"gc":"G0X100 (Initial move)"},"f":[1,0,19,2131]}
     {"r":{"gc":"M0","msg":"Change tool and hit Cycle Start when done"},"f":[1,0,19,2131]}

The first response is pretty normal. The second has a comment in it. The third is what would happen if a MSG were communicated in the Gcode comment. 

## Status Reports in JSON
Status reports are parent/child objects with a "sr" parent and one or more child NV pairs, as in the example below.<br> 

    {"sr": {"line":1245, "xpos":23.4352, "ypos":-9.4386, "zpos":0.125, "vel":600, "unit":"1", "stat":"5"}"f":[1,0,19,2131]}}

The following use-cases are supported: 

*SET status report fields.&nbsp;The elements to be included in a status reports may be specified by setting values to 'true'. The elements will be returned on subsequent SR requests in the order they were provided in the SET command. (I know, dictionaries are supposed to be unordered, but the firmware will record and return back the attributes in the order listed). There is no incremental setting of elements - all attributes are reset and must be specified in a single SET command. For example, the string below could be used to set up the status report in the example above, and eliminate any previously recorded settings. Note that the 'true' term is not in quotes - it is actually the JSON value for true, not a string that says "true". Examples:
<pre>{"sr":{"line":true,"xpos":true, "ypos":true,"zpos":true, "vel":true, "unit":true, "stat":true}}
{"status_report":{"line":true,"xpos":true, "ypos":true,"zpos":true, "vel":true, "unit":true, "stat":true}}  - this is the same as the above but using the friendly name
</pre> 
*GET an on-demand status report - this will return a single status report. All forms below are equivalent. The first two use friendly names, the last two use mnemonic tokens. &nbsp;
<pre>{"status_report":""}
{"status_report":null}    the 'null' value is used instead of "" in this case. Either are accepted.
{"sr":""}
{"sr":null}
</pre> 
*Request an ad-hoc report. An ad-hoc query for one or more name/value pairs can be performed. This is technically not a "status report", but it's useful. This is really just multiple GET requests in a single JSON object.<br>
<pre>{"line":"","xpos":"","ypos":"","zpos":"","vel":"","unit":"","stat":""}                               request GETS for the listed values
{"line":1245, "xpos":23.4352, "ypos":-9.4386, "zpos":0.125, "vel":600, "unit":"mm", "stat":"run"}    returns the NV pairs. Note the absence of the SR parent object.
</pre> 
*Automatic status reports. Status reports may also be automatically generated by setting the status report interval to a non-zero value. Reports will be generated during movement every N milliseconds, and at the end of each Gcode bloack that causes a movement, and when the machine has stopped (i.e. at the end of the final move in the buffer).&nbsp;
<pre>{"status_interval":0}      disable automatic status reports
{"status_interval":200}    request status reports every 200 ms during movement
{"si":200}                 same as above
</pre> 
A status report may contain one or more of the following attributes. The [token] and the friendly name are listed. Values are returned as numbers. (note: A CSV string is returned when in text mode) 
<pre>[vel]  velocity           - actual velocity - may be different than programmed feed rate 
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

[mpox] mpox (x absolute position) - X machine position in absolute coordinate system in prevailing units (mm or inch) 
[mpoy] mpoy (y absolute position)
[mpoz] mpoz (z_absolute position)
[mpoa] mpoa (a absolute position)
[mpob] mpob (b absolute position)
[mpoc] mpoc (c absolute position)

[posx] posx (x position)          - X work position in prevailing units (mm or inch) 
[posy] posy (y position)
[posz] posz (z position)
[posa] posa (a position)
[posb] posb (b position)
[posc] posc (c position)

[g92x] g92x - G92 origin offset for X axis
[g92y] g92y
[g92z] g92z
[g29a] g92a
[g92b] g92b
[g92c] g92c

Additionally, any valid token may be listed in a status report. For example, "g54x" will return the X offset in the G54 coordiante system (coordinate system #1). "fv" would return the firmware version. 
</pre> 
<br>

== Group Resources ==

"Groups" are related parameters or settings. The following groups are defined: 

*Axis groups [x,y,z,a,b,c] - contains all per-axis configuration settings such as maximum velocity and axis travel 
*Motor groups [1,2,3,4] - contains all per-motor configuration settings such as steps per revolution or microsteps 
*Coordinate system offset groups [g54,g55,g56,g57,g58,g59] - contains offsets for xyzabc axes. 
*System group - special group with system and global parameters&nbsp;

See [[TinyG:Configuring|[Configuration]]]&nbsp;for details about the settings available in groups. 

Groups are treated specially in JSON. The group prefix is "pulled out" and used as the parent object ID for the child NV pairs. For example, here is a standard (non-group) JSON line with all motor parameters for motor #2: 
<pre>{"2ma":1,"2sa":1.8,"2tr":1.275,"2mi":2,"2po":0,"2pm":1}

where:
[2ma] m2_map_to_axis
[2sa] m2_step_angle
[2tr] m2_travel_per_revolution
[2mi] m2_microsteps
[2po] m2_polarity
[2pm] m2_power_management
</pre> 
&nbsp;Here is the same set of parameters represented as a group resource: 
<pre>{"2":{"ma":1,"sa":1.8,"tr":1.275,"mi":2,"po":0,"pm":1}}
</pre> 
These are handled identically by the JSON parser. Either line above would have set all the values in the list. If null's had been provided all values would have been returned. 

A group resource can also be retrieved by its parent name alone: 
<pre>{"2":""}   returns:
{"2":{"ma":1,"sa":1.8,"tr":1.275,"mi":2,"po":0,"pm":1}}
</pre> 
Another advantage of the group form is that all motors will serialize / deserialize in host code to well-defined objects, making for convenient host coding. Essentially a resource can be instantiated in the host program, serialized to JSON, then sent to the the firmware to set all values in the resource. The same is true in reverse. You can think of r attributes as being in an implicit dotted notation: e.g. x.fr, 1.sa, or g55.x<br> 

'''''Here is an short Python Script that illustrates how easy it is to get data back from TinyG in a programatic way.''''' 
<pre>#!/usr/bin/python
import json
motor_settings = '{"2":{"ma":1,"sa":1.8,"tr":1.275,"mi":2,"po":0,"pm":1}}'
#motor_settings is an example response you would get from TG if you issued a {"2":""}\n command
motors=json.loads(motor_settings)  #load the response into a python dict.

#Break out individual values into vars.
microSteps, travelRevs, stepAngle, polarity, powerManagement = (motors["2"]["ma"], p["2"]["tr"], p["2"]["sa"], p["2"]["po"], p["2"]["pm"])
</pre> 
Some other examples of group resoruces: 
<pre>{"x":{"am":1,"fr":1000,"vm":1000,"tm":100,"jm":100000000,"jd":0.05,"sm":1,"sv":1000,"lv":100,"zo":4,"abs":"","pos":""}}
{"y":{"am":1,"fr":1000,"vm":1000,"tm":100,"jm":100000000,"jd":0.05,"sm":1,"sv":1000,"lv":100,"zo":4,"abs":"","pos":""}}
{"z":{"am":1,"fr":1000,"vm":1000,"tm":100,"jm":100000000,"jd":0.05,"sm":1,"sv":1000,"lv":100,"zo":4,"abs":"","pos":""}}
{"g54":{"x":0.000,""y":0.000,"z":0.000,"a",0.000,"b":0.000,"c":0.000}}
{"g55":{"x":50.000,""y":50.000,"z":-10.000,"a",0.000,"b":0.000,"c":0.000}}
</pre> 
=== System Group  ===

The System Group is a special case that handles system and global values that don't have have a common prefix. The system group is identified by "sys". Here are the members of the system group listed as a plain list and as a group resource. 
<pre>{"fv":"","fb":"","si":"","gpl":"","gun":"","gco":"","gpa":"","gdi":"",ea":"","ja":"","ml":"","ma":"","mt":"","ic":"","il":"","ec":"","ee":"","ex":""}          - as a plain list
{"sys":{"fv":"","fb":"","si":"","gpl":"","gun":"","gco":"","gpa":"","gdi":"",ea":"","ja":"","ml":"","ma":"","mt":"","ic":"","il":"","ec":"","ee":"","ex":""}}  - as a system group
{"sys":""}  - returns all values in the system group

[fv]  firmware_version
[fb]  firmware_build
[si]  status_interval

[gpl] gcode_select_plane   - power-on and reset default, not equivalent to G17, G18, G19
[gun] gcode_units_mode     - power-on and reset default, not equivalent to G20, G21
[gco] gcode_coord_system   - power-on and reset default, not equivalent to G54 - G59
[gpa] gcode_path_control   - power-on and reset default, not equivalent to G61, G61.1, G64
[gdi] gcode_distance_mode  - power-on and reset default, not equivalent to G90, G91

[ea]  enable_acceleration
[ja]  junction_acceleration
[ml]  min_line_segment
[ma]  min_arc_segment
[mt]  min_segment_time

[ic]  ignore_CR/LF (on RX)
[ec]  enable_CR (on TX)
[ee]  enable_echo
[ex]  enable_xon_xoff
[ej]  enable_json_mode on startup
</pre> 
<br>