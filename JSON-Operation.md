(last updated 12/21/12 - ash) 

**NOTE: This page describes JSON operations in release 0.95 and later. Currently this code is only in the edge and dev branches. If you want to try it we recommend using the edge branch as it's more stable than dev.**

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

TinyG can accept commands in JSON (JavaScript Object Notation) mode or text mode. Using JSON simplifies writing host GUIs in languages such as Python, Ruby, JavaScript, Java, Processing and other languages that support dictionaries / hashmaps. 

Most commands that are available in JSON are also available in text mode, but there are some differences. ASCII communications overhead is somewhat higher in JSON than text mode but is still quite efficient and manageable.

### Is this RESTful?
The JSON interface is modeled as a RESTful interface, albeit running over USB serial and not HTTP. Using JSON enables exposing system internals as [RESTful resources](http://en.wikipedia.org/wiki/Representational_state_transfer), which makes the embedded system much easier to manipulate using modern programming techniques. Data is treated as resources and state is transfered in and out. Methods are implied by convention as there is no request header in which to declare them. 

## Basic Concepts

	Term | Description
	-----|--------------
	**name** | A name is a JSON name (aka **token**) describing a single data value or a group of data values. Examples of names include "xfr" referring to the X axis maximum feed rate, or "x" referring to all values associated with the X axis (the X axis group). Names are not case sensitive.
	**value** | A value is the number, string, or true/false setting for a name. Values can also be NULL, meaning "get-the-value" in TinyG. A name and a value is a name:value pair or **NV pair**. NULL values can be represented by the word 'null' (case insensitive, no quotes), or quote quote --> "", which is more efficient
	**group** | A group is a collection of one or more NV pairs. Groups are used to specify all parameters for a motor, an axis, a PWM channel, or other logical grouping. A group is similar in concept to a RESTful resource or composite.
	**configs** | Configs are the collection of static configuration settings for the machine. These static parameters are not changed by Gcode execution (but see the G10 exception). Xfr is an example of a config. So is 1po. So is the X group.
	**gcode blocks** | Gcode blocks are lines of Gcode. Blocks can contain one or more Gcode **words**, optional comments and messages, and some other Gcode characters. G1 is an example of a gcode word. So is x23.43. A Gcode **comment** is denoted by parentheses - (this is a Gcode comment). A Gcode **message** is a form of comment that is echoed to the machine operator - (msgThis part is echoed to the user). Gcode words are part of the **dynamic model** that holds the values used by the Gcode file itself. There are a few cases where these cross over (G10's) but this is discussed later.

## JSON Overview & TinyG Subset

The full JSON language definition is [here](http://json.org). A handy validator can be found [here](http://jsonlint.com).

TinyG implements a subset of JSON with the following limitations: 

* Supports 7 bit ASCII characters only 
* Does not support hexadecimal numbers or other non-decimals
* Arrays are returned but are not accepted as input (currently)
* Names are case-insensitive
* Names cannot be more than 5 characters
* String values cannot be more than 64 characters in length (_Note 1_)
* Multi-valued objects (resources) cannot contain more than 24 name/value pairs (compile-time setting)
* Objects cannot exceed 255 characters in length total 
* Only 2 levels of object nesting is supported
* All JSON input and output is on a single text line. There is only one `<CR>`, it's at the end of the line (broken lines are not supported)

_Note 1: The 64 character string length applies to Gcode blocks delivered via JSON or interpreted while the system is in JSON mode. If the Gcode line exceeds 64 characters it will be truncated if a comment makes it too long, or rejected if the 65th character is part of the legitimate Gcode block. If 64 characters is too severe this may be raised._

### Text Mode and JSON Mode

TinyG can operate in either text mode (command line mode) or JSON mode. In text mode TinyG accepts $ config lines and normal Gcode blocks, and returns responses as human-friendly ASCII text. Text mode responses return "ok:" or "error:" to maintain compatibility with grbl parsers.

TinyG starts up in text mode if the $ej setting is set to text mode ($ej=0). TinyG will also enter text mode automatically if it receives a line with a leading $, ? or 'h'. (Note: The first status message returned on bootup will be in JSON format, regardless of the mode set). 

TinyG starts up in JSON mode if the $ej setting is set to JSON mode ($ej=1). TinyG will also enter JSON mode automatically if it receives a line starting with an open curlie '{'. While in JSON mode all commands are  expected in JSON format and responses are returned in JSON format....

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

The 'r' is the response envelope. The body of the response is the result returned. In the case of a single name it returns the value. In the case of a group it returns the entire group as a child object. The "f" is the footer which is an array consisting of (1) revision number, (2) status code, (3) bytes pulled from RX buffer for this command, and (4) checksum (more details provided later).

JSON reports are in the following general form.
<pre>
{"r":{"sr":{"line":0,"posx":0.000,"posy":0.000,"posz":0.000,"posa":0.000,"vel":0.000,"momo":1,"stat":3}}}
</pre>

It's the same except there is no footer element. Reports are typically generated by the system and are therefore asynchronous. Therefore the status code is irrelevant, as is the number of bytes pulled form the queue (0). Since they are informational a checksum does nto need to be provided. The exception is a report that is requested, which will return a footer. E.g. the the command {"sr":""} can be used to request the status report in the above example. (NOTE: Footers are not yet returned for requested reports - to do)

#JSON Details
##JSON Mode Protocol
### Commands
Commands in JSON mode are sent as JSON messages Examples:

    {"x":""}<lf>          get the X resource
    {"xvm":12000}<lf>     set the X maximum velocity to 12000 mm/min (assuming the system is in G21 mode)
    {"gc":"g0 x100"}<lf>  execute the Gcode block "G0 X100"

Responses are wrapped in a response body and include a footer. Responses to the above examples are:

    {"r":{"x":{"am":1,"vm":16000.000,"fr":16000.000,"tm":220.000,"jm":5000000000.000,"jd":0.010,"sn":3,"sx":2,"sv":3000.000,"lv":100.000,"lb":20.000,"zb":3.000}},"f":[1,0,9,9580]}
    {"r":{"xvm":12000.000},"f":[1,0,14,3009]}
    {"r":{"gc":"g0x100"},"f":[1,0,17,9360]}
    
    where  "f":[1,0,255,1234]  
       is  "f":[<protocol_version>, <status_code>, <input_available>, <checksum>]
 
The footer contains a 4 element array of packet flow control information as per the following:

	Element  | Notes
	-------|-------------------------
	array_ID/version | Initially set to 1. This number identified the array and version for the parser. It will be incremented if protocol changes are made that would affect UI clients
	status_code | 0 is OK. All others are exceptions. See tinyg.h for details
	RX_received | Indicates how many characters were removed from the serial RX buffer to process this command. THis allows the host to keep a running total of the bytes available in the TinyG RX buffer. The RX buffer typically starts with 3254 bytes free. The <lf> counts as a byte. 
	checksum | A 4 digit checksum for the line. The checksum is generated for the JSON line up to but not including the comma preceding the checksum itself. I.e, the comma is where the nul termination would exist. The checksum is computed as a [Java hashcode](http://en.wikipedia.org/wiki/Java_hashCode(\)) from which a modulo 9999 is taken to fix the length to 4 characters. See compute_checksum() in util.c for C code.

### Response Verbosity
Character echo ($ee) is always an option; it's just not a good one for JSON. In JSON mode it should be turned off (  $ee=0, or {"ee":0}  ). It's a matter of preference for text mode.

JSON verbosity ($jv) sets the level of verbosity in JSON responses. It is set by {"jv":N} where N is one of:

* 0 = SILENT - no response
* 1 = FOOTER_OMIT - no body data is returned, only footer data
* 2 = OMIT_GCODE_BODY - body returned for configs; omitted for Gcode commands
* 3 = GCODE_LINENUM_ONLY - body returned for configs; Gcode returns line number as 'n', otherwise body is omitted
* 4 = GCODE_MESSAGES - body returned for configs; Gcode returns line numbers and messages only
* 5 = VERBOSE - body returned for configs and Gcode - Gcode comments removed

Generally levels 2, 3 or 4 are recommended. Levels 4 and 5 will return Gcode comment messages of the form (msgSome text you want to show to the operator)

----- OK Needs work from here on down ------


###Reading Configuration Parameters (GET)

To get a parameter pass an object with a null value. The value is returned in the response. Some examples of valid requests and responses are provided below. 

	Request | Response | Description
	------|---------|---------|--------------
	xfr | $xfr | {"xfr":""} | X axis maximum feed rate
	cjm | $cjm | {"cjm":""} | C axis maximum jerk 
	1mi | $1mi | {"1mi":""} | Motor 1 microstep setting 
	home | $home | {"home":""} | Homing state


| {"xfr":""} 
| {"r":{"bd":{"xfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| return x max feed rate
|-
| {"x_feedrate":""} 
| {"r":{"bd":{"xfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Same as above using friendly-name
|-
| {"xfr":null} 
| {"r":{"bd":{"xfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Same as above
|-
| {"xfr":null, "yfr":null, "zfr":null} 
| {"r":{"bd":{"xfr":1200.000,"yfr":1200.000,"zfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Multiple gets may be presented in a single object
|}

=== Setting Configuration Parameters (PUT)  ===

To set a parameter pass an object with the value to be set. The value taken is returned in the response. The response value may be different than the requested valued in some cases. For example, an attempt to set&nbsp;a status interval less than the minimum will return the minimum interval. Trying to set a read-only value will return that value; for example, firmware version. In some other cases a value of 'false' will be returned. The following are examples of valid set commands.<br> 

{| width="1200" border="1" cellpadding="1" cellspacing="1"
|-
| Request 
| Response 
| Comments
|-
| {"xfr":1200} 
| {"r":{"bd":{"xfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Set x max feed rate to 1200 mm/min (assumes mm units set)
|-
| {"x_feedrate":1200} 
| {"r":{"bd":{"xfr":1200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Same as above using friendly-name
|-
| {"si":250} 
| {"r":{"bd":{"si":250.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Set status interval to 250 milliseconds
|-
| {"si":10} 
| {"r":{"bd":{"si":200.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Attempt to set status interval to 10 ms, but minimum was 200, so that's what stuck
|-
| {"fv":2.0} 
| {"r":{"bd":{"fv":0.930},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Whilst you may want a version 2.0 to magically appear the firmware remains at version 0.93 &nbsp;:(
|-
| {"xvm":2000, "yvm":2000, "zvm":2000, "avm":36000} 
| {"r":{"bd":{"xvm":2000.000,"yvm":2000.000,"zvm":2000.000,"avm":36000.000},"sc":0,"sm":"OK","cks":"2593896578"}} 
| Setting max velocity for 4 axes
|}

== JSON Response Header Format  ==

All responses to JSON request are returned in a response header. The response format provides an outer wrapper (header) for response details and an inner "body" which is the object being returned. The response wrapper plays a similar role to an HTTP response header. This simplifies parsing and makes the whole thing one step closer to an actual RESTful HTTP implementation.&nbsp; 

The response wrapper has the following format: 
<pre>{                                        Comments:
    "r": {                               response object
&nbsp; &nbsp; &nbsp; &nbsp; "bd": { &nbsp;   &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;body of response
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;  &lt;returned object&gt; &nbsp;  &nbsp; &nbsp;returned object, e.g. {"xvm":600.00"}
&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; },
&nbsp; &nbsp; &nbsp; &nbsp; "sc": 0, &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; status code
&nbsp; &nbsp; &nbsp; &nbsp; "sm": "OK", &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;status message (for end-user display)
        "buf": 255,                      bytes available in input buffer
        "ln": 0,                         gcode line number N just received, or auto-increment if no N word provided
&nbsp; &nbsp; &nbsp; &nbsp; "cks": 123456789 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; checksum
&nbsp; &nbsp; &nbsp; &nbsp; }
}
</pre> 
Note: The actual output is on one line with a trailing &lt;CR&gt;. The format above is just for clarity 

Note: The letter 'q' is rereserved for reQuest, should we decide to wrap that, too.<br> 

'''"r" &nbsp;Response Header''' 

Responses can be up to 256 characters long but we try to keep them shorter in the interest of communications overhead.<br> 

The entire JSON object is terminated by CR, LF or CR/LF depending on the board configuration settings ($ec, $el)<br> 

'bd'&nbsp;'''{returned body object}''' This is a JSON object that is one of: 

*a configuration parameter response, e.g.&nbsp;{"xvm":600.000} 
*a group response, e.g.&nbsp;{"x":{"am":1,"vm":600.000,"fr":600.000,"tm":475.000,"jm":20000000.000,"jd":0.050,"sm":1,"sv":-500.000,"lv":100.000,"lb":2.000,"zb":1.000}} 
*a Gcode response, e.g.{"gc":"G0X90"} &nbsp; (Note: this is a change from 0.93/0.94 as the gcode object is now just the command line, no status or message) 
*a status report, e.g.&nbsp;{"sr":{"line":3,"posx":90.000,"posy":0.000,"posz":50.000,"posa":0.000,"feed":0.000,"vel":0.000,"unit":1,"coor":1,"dist":0,"frmo":0,"momo":0,"stat":2}}<br> 
*a message, e.g. {"msg":"Some text that should be displayed to the user"} &nbsp;(note, a mesage field may also follow any of the above in the response body)

'''"sc" &nbsp;Status Code''' 

A status code of '0' is OK. All others are errors. Check tinyg.h for the most up-to-date list, and see controller.c for corresponding end-user status messages.<br> 
<pre>// OS, communications and low-level status (must align with XIO_xxxx codes in xio.h)
#define	TG_OK 0				// function completed OK
#define	TG_ERROR 1			// generic error return (EPERM)
#define	TG_EAGAIN 2			// function would block here (call again)
#define	TG_NOOP 3			// function had no-operation
#define	TG_COMPLETE 4			// operation is complete
#define TG_TERMINATE 5			// operation terminated (gracefully)
#define TG_ABORT 6			// operaation aborted
#define	TG_EOL 7			// function returned end-of-line
#define	TG_EOF 8			// function returned end-of-file 
#define	TG_FILE_NOT_OPEN 9
#define	TG_FILE_SIZE_EXCEEDED 10
#define	TG_NO_SUCH_DEVICE 11
#define	TG_BUFFER_EMPTY 12
#define	TG_BUFFER_FULL_FATAL 13 
#define	TG_BUFFER_FULL_NON_FATAL 14	// NOTE: XIO codes align to here

// Internal errors and startup messages
#define	TG_INTERNAL_ERROR 20		// unrecoverable internal error
#define	TG_INTERNAL_RANGE_ERROR 21	// number range other than by user input
#define	TG_FLOATING_POINT_ERROR 22	// number conversion error
#define	TG_DIVIDE_BY_ZERO 23

// Input errors (400's, if you will)
#define	TG_UNRECOGNIZED_COMMAND 40	// parser didn't recognize the command
#define	TG_EXPECTED_COMMAND_LETTER 41	// malformed line to parser
#define	TG_BAD_NUMBER_FORMAT 42		// number format error
#define	TG_INPUT_EXCEEDS_MAX_LENGTH 43	// input string is too long 
#define	TG_INPUT_VALUE_TOO_SMALL 44	// input error: value is under minimum
#define	TG_INPUT_VALUE_TOO_LARGE 45	// input error: value is over maximum
#define	TG_INPUT_VALUE_RANGE_ERROR 46	// input error: value is out-of-range
#define	TG_INPUT_VALUE_UNSUPPORTED 47	// input error: value is not supported
#define	TG_JSON_SYNTAX_ERROR 48		// JSON string is not well formed
#define	TG_JSON_TOO_MANY_PAIRS 49	// JSON string or has too many JSON pairs

// Gcode and machining errors
#define	TG_ZERO_LENGTH_MOVE 60		// move is zero length
#define	TG_GCODE_BLOCK_SKIPPED 61	// block is too short - was skipped
#define	TG_GCODE_INPUT_ERROR 62		// general error for gcode input 
#define	TG_GCODE_FEEDRATE_ERROR 63	// move has no feedrate
#define	TG_GCODE_AXIS_WORD_MISSING 64	// command requires at least one axis present
#define	TG_MODAL_GROUP_VIOLATION 65	// gcode modal group error
#define	TG_HOMING_CYCLE_FAILED 66	// homing cycle did not complete
#define	TG_MAX_TRAVEL_EXCEEDED 67
#define	TG_MAX_SPINDLE_SPEED_EXCEEDED 68
#define	TG_ARC_SPECIFICATION_ERROR 69	// arc specification error

</pre> 
At some point we may want to distinguish between fatal and non fatal errors.<br> 

'''"sm"&nbsp; Status Message''' 

Status messages are end-user text associated with each error code (see controller.c). Not all commands are required to return messages (for example, status reports may not). Parsers should work to the status codes, NOT the messages, as messages may change or be omitted.&nbsp; 

Note: these are distinct from application messages that may be returned from Gcode MSG fields, or from system startup operations.<br> 
'''"buf"'''&nbsp;RX buffer bytes available

This value is how many bytes are available in the serial input buffer (RX buffer). An empty buffer has 254 bytes. As the RX buffer fill up this number goes down until a command is read out of the buffer. Then it goes back up by the length of the line read. This value is used for synchronization with host drivers.

'''"ln"''' Last line number processed

This is the line number of the line that was most recently pulled out of the buffer, or the "gcode model" line number. This number will be populate with the N word sent in the Gcode block - e.g. N300. If no N word is populated the line number will advance by 1 for each new line sent in. Since Gcode does not require every line to have a number (or any!), this can lead to some odd behaviors. Say you set N300, then followed with 20 lines without N words. Then sent N310. The line number will advance to 320, then reset to 310. You are your own master. Note that the line number reported in "ln" (the *model* line number) is different than the *runtime* line number reported as "line" in a status report. "Line" is the line currently being executed by the steppers. The model line number may lag the runtime line number by as many as 24 moves, depending on how deep the move planner is.<br><br>
Be aware that BX buffer space is being under-reported in most cases. The controller module is continuously pulling characters off the RX buffer into a separate input buffer (string). When the input string has a full command ready for processing it's another 100 microseconds or so before the model line number is updated and available for return (e.g. on a status report or some other asynchronous response). Then typically it will be another 3000 microseconds (+ or -) before the command is actually processed and the response to the command is returned with the new model line number. This under-reporting should not cause problems for command synchronization with the host, but it's good to know it's happening. 

'''"cks"''' &nbsp;Checksum 

The checksum is always the last item on the line. The checksum is the decimal Java hashCode of the string starting with the opening curly (in front the "r") to the "cks" name. For example: 

for this string ---------&gt; {"r.:{"bd":{"gc":"g0x100"},"sc":0,"sm":"OK","cks":"3756753382"}} 

take checksum of --&gt;&nbsp;{"r.:{"bd":{"gc":"g0x100"},"sc":0,"sm":"OK","cks" 

Do not include a CR or line terminator in the checksum. 

See [http://en.wikipedia.org/wiki/Java_hashCode() en.wikipedia.org/wiki/Java_hashCode()]&nbsp;or the algorithm<br>

== Get and Set Configuration Parameters  ==

== Gcode in JSON  ==

In JSON mode Gcode blocks may be provided either as native gcode text or wrapped in JSON as a "gc" object. In either case responses will be returned in JSON format.&nbsp;A JSON wrapper may only contain a single gcode block.<br> 
<pre>Gcode requests
{"gc":"g0 x100"}                                               wrapped input
g0 x100                                                        unwrapped input
{"gc":"g0 x100 (Initial move)"}                                wrapped input with a Gcode comment (see note)
{"gc":"m0 (MSGChange tool and hit Cycle Start when done)"}     stop with a message

Gcode responses
{"r":{bd:{"gc":"G0X100"},"st":0,"sm":"OK","cks":123456789}}
{"r":{bd:{"gc":"G0X100 (Initial move)"},"st":0,"sm":"OK","cks":123456789}}
{"r":{bd:{"gc":"M0","msg":"Change tool and hit Cycle Start when done"},"st":0,"sm":"OK","cks":123456789}}
</pre> 
The first response is pretty normal. The second has a comment in it. The third is what would happen if a MSG were communicated in the Gcode comment. 

Note: We are still undecided on how to handle comments in the Gcode. These could be returned in the gcode response as illustrataed, but currently they are stripped. 

Note: We are still debating if line numbers should be returned as a separate JSON element, e.g. "gc":"G0X100","line":2245. See issue #23 on the TinyG github 

<br>

== Status Reports in JSON  ==

Status reports are parent/child objects with a "sr" parent and one or more child NV pairs, as in the example below.<br> 
<pre>{"sr": {"line":1245, "xpos":23.4352, "ypos":-9.4386, "zpos":0.125, "vel":600, "unit":"mm", "stat":"run"}}
</pre> 
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