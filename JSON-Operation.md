_updated 2/24/13 - ash_

This page describes JSON operation in TinyG firmware version 0.95, which is the current master build. This page provides a JSON cheat sheet and an overview of JSON operation. See [JSON Details](https://github.com/synthetos/TinyG/wiki/JSON-Details) for the real nuts and bolts.

# JSON Cheat Sheet
This table summarizes using JSON for [configuration and commands](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration). Details are provided in the subsquent sections.

	Request | Response | Description
	---------|--------------|-------------
	{"xvm":""} | {"b":{"xvm":16000},"f":[1,0,11,1301]}<nl>| get X axis maximum velocity
	{"xvm":15000} | {"b":{"xvm":15000},"f":[1,0,14,9253]}<nl>| set X axis maximum velocity to 15000
	{"x":{"vm":""}} | {"b":{"x":{"vm":16000}},"f":[1,0,16,2128]}<nl>| alternate form to get X axis maximum velocity
	{"x":{"vm":15000}} | {"b":{"x":{"vm":15000}},"f":[1,0,19,2131]}<nl>| alternate form to set X axis maximum velocity to 15000
	{"x":""} | {"b":{"x":{"am":1,"vm":16000.000,"fr":16000.000,.... | get entire X axis group (see below for entire response)
	{"gc":"g0x10"} | {"f":[1,0,11,1234]}<nl>| send Gcode with verbosity=1, 2 or 3
	{"gc":"n20g0x20"} | {"r":{"n":20},"f":[1,0,9,5362]} | send Gcode with verbosity=4
	{"gc":"n20g0x20"} | {"r":{"gc":"n20g0x20","n":20},"f":[1,0,9,7209]} | send Gcode with verbosity=5
	{"gc":"g0x10"} | {"r":{"gc":"g0x10"},"f":[1,0,6,8628]}<nl>| send Gcode with verbosity=5
	n20g0x20 | {"r":{"gc":"n20g0x20","n":20},"f":[1,0,9,7209]} | send unwrapped Gcode with verbosity=5
	g0x10 | {"r":{"gc":"g0x10"},"f":[1,0,6,8628]}<nl>| send unwrapped Gcode with verbosity=5

X axis group response:
<pre>
 {"r":{"x":{"am":1,"vm":16000.000,"fr":16000.000,"tm":220.000,"jm":5000000000.000,"jd":0.010,"sn":3,"sx":2,"sv":3000.000,"lv":100.000,"lb":20.000,"zb":3.000}},"f":[1,0,9,9580]} 
</pre>
#JSON Summary
## General

TinyG can accept commands in [JSON](http://json.org/) (JavaScript Object Notation) mode or text mode. Using JSON simplifies writing host GUIs in languages such as Python, Ruby, JavaScript, Java, Processing and other languages that support dictionaries / hashmaps. 

Most commands that are available in JSON are also available in text mode, but there are some differences. ASCII communications overhead is somewhat higher in JSON than text mode but is still quite efficient and manageable.

### Is this RESTful?
The JSON interface is modeled as a RESTful interface, albeit running over USB serial and not HTTP. Using JSON enables exposing system internals as [RESTful resources](http://en.wikipedia.org/wiki/Representational_state_transfer), which makes the embedded system much easier to manipulate using modern programming techniques. Data is treated as resources and state is transfered in and out. Unlike RESTful HTTP, methods are implied by convention as there is no request header in which to declare them. 

## Basic Concepts

	Term | Description
	-----|--------------
	**name** | A name is a JSON name (aka **token**) describing a single data value or a group of data values. Examples of names include "xfr" referring to the X axis maximum feed rate, or "x" referring to all values associated with the X axis (the X axis group). Names are not case sensitive.
	**value** | A value is the number, string, or true/false setting for a name. Values can also be NULL, meaning "get-the-value" in TinyG. A name and a value is a name:value pair or **NV pair**. <br>NULL values can be represented by a pair of quotes `""`, the word `null` (case insensitive, no quotes), or simply `n` for short
	**group** | A group is a collection of one or more NV pairs. Groups are used to specify all parameters for a motor, an axis, a PWM channel, or other logical grouping. A group is similar in concept to a RESTful resource or composite.
	**configs** | Configs are the collection of static configuration settings for the machine. These static parameters are not changed by Gcode execution (but see the G10 exception). Xfr is an example of a config. So is 1po. So is the X group.
	**gcode blocks** | Gcode blocks are lines of Gcode. Blocks can contain one or more Gcode **words**, optional comments and messages, and some other Gcode characters. G1 is an example of a gcode word. So is x23.43. A Gcode **comment** is denoted by parentheses - (this is a Gcode comment). A Gcode **message** is a form of comment that is echoed to the machine operator - (msgThis part is echoed to the user). The gcode file builds and runs the **dynamic model** that controls the machine in operation. It is useful to make the distinction between the static model set by the configs, and the dynamic model used and updated by the Gcode/machine. This makes things easier to understand.

## JSON Overview & TinyG Subset

The concise JSON language definition is [here](http://json.org). A handy validator can be found [here](http://jsonlint.com).

TinyG implements a subset of JSON with the following limitations: 

* Supports 7 bit ASCII characters only 
* Supports decimal numbers only (no hexadecimal numbers or other non-decimals)
* Arrays are returned but are not (yet) accepted as input
* Names (tokens) are case-insensitive and cannot be more than 5 characters
* String values cannot be more than 80 characters in length (Note 1)
* Groups cannot contain more than 24 elements (name/value pairs)
* JSON objects cannot exceed 254 characters in total length 
* Limited object nesting is supported (you won't see more than 2 levels)
* All JSON input and output is on a single text line. There is only one `<LF>`, it's at the end of the line (broken lines are not supported)

_Note 1: The 80 character string length applies to Gcode blocks delivered via JSON or interpreted while the system is in JSON mode. If the Gcode line exceeds 80 characters it will be truncated if a comment makes it too long, or rejected if the 81st character is part of the legitimate Gcode block. If 80 characters is too severe this may be raised._

### Text Mode and JSON Mode

TinyG can operate in either text mode (command line mode) or JSON mode. In text mode TinyG accepts $ config lines and normal Gcode blocks, and returns responses as human-friendly ASCII text. Text mode responses return "ok:" or "error:" to maintain compatibility with grbl parsers.

TinyG starts up in text mode if the $ej setting is set to text mode ($ej=0). TinyG will also enter text mode automatically if it receives a line with a leading $, ? or 'h'. (Note: The first status message returned on bootup will be in JSON format, regardless of the mode set). 

TinyG starts up in JSON mode if the $ej setting is set to JSON mode ($ej=1). TinyG will also enter JSON mode automatically if it receives a line starting with an open curly '{'. While in JSON mode all commands are  expected in JSON format and responses are returned in JSON format....

...the exception being Gcode blocks. Gcode blocks streamed to TinyG while in JSON mode can be sent with or without JSON wrappers - i.e. unwrapped Gcode will not return the system to text mode. Responses will always be returned in JSON format.


In JSON mode TinyG expects well structured JSON (if in doubt use the [JSON validator](http://jsonlint.com)). JSON requests are generally just curly braces and formatting around what would otherwise be a command line request. A JSON line can only contain a single request object. This may be a single value such as {"xvm":""}, or a complete group such as {"x":""} - but only one group. Note that groups with multiple *elements* are accepted. For example, this is OK: 
{"x":{"vm":16000,"fr":10000}}

### Names and Tokens 

JSON names are short mnemonic tokens that can be 1 to 5 characters in length. Axis and motor tokens are typically 3 characters in length including their axis or motor prefix; Non-axis and non-motor (general) tokens are 2 to 5 characters. Tokens are case insensitive and can only contain alphanumeric characters. See [TinyG Configuration](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration) for a complete list of the tokens used for settings. Some examples are provided below: 

	Token | as Text | as JSON | Description
	------|---------|---------|--------------
	xfr | $xfr | {"xfr":""} | X axis maximum feed rate
	cjm | $cjm | {"cjm":""} | C axis maximum jerk 
	1mi | $1mi | {"1mi":""} | Motor 1 microstep setting 
	home | $home | {"home":""} | Homing state
	x | $x | {"x":""} | X axis group. In text mode a group can only be queried (get). In JSON mode a group can be queried and can also be used to set any or all values in the group

##JSON Request and Response Formats

JSON requests are used to perform the following actions {with examples}

* Return the value of a single setting or state variable {"1mi":""}
* Return the values of a group of settings or state variables (aka a Resource) {"1":""}
* Set a single setting or state variable (note that most state variables are read-only) {"1mi":8}
* Set a multiple settings or state variables in a group {"1":{"po":1,"mi":8}}
* Submit a block (line) of Gcode to perform any supported Gcode command {"gc":"n20g1f350 x23.4 y43.2"}
* Special functions and actions;
 * Request a status report {"sr":""} 
 * Set status report contents {"sr":{"line":true,"posx":true,posy":true,   ...}}
 * Run self tests {"test":1}
 * Reset parameters to defaults {"defa":true}

JSON responses to commands are in the following general form.
<pre>
{"xjm":""} returns:
{"r":{"xjm":5000000000.000},"f":[1,0,11,6649]}

{"2":""} returns:
{"r":{"2":{"ma":1,"sa":1.800,"tr":36.540,"mi":8,"po":1,"pm":1}},"f":[1,0,9,2423]}
</pre>

The 'r' is the response envelope. The body of the response is the result returned. In the case of a single name it returns the value. In the case of a group it returns the entire group as a child object. The "f" is the footer which is an array consisting of (1) revision number, (2) status code, (3) the number of bytes pulled from RX buffer for this command (including the terminating LF), and (4) checksum (more details provided later).

**Reports** are generated automatically by the system and are therefore asynchronous. JSON reports are in the following general form:
<pre>
{"sr":{"line":0,"posx":0.000,"posy":0.000,"posz":0.000,"posa":0.000,"vel":0.000,"momo":1,"stat":3}}
</pre>

It's similar to a response except there is no header or footer element. Since it's asynchronous the status code is irrelevant, as is the number of bytes pulled form the queue (0). Since they are informational a checksum is not provided. The exception is a report that is requested, which will return a footer. E.g. the the command {"sr":""} can be used to request the status report in the above example. 

#JSON Details
See [JSON Detail](https://github.com/synthetos/TinyG/wiki/JSON-Details) for more information