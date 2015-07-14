This page contains status codes and other enumerations that may be returned in status reports
See also:
* [**Gcode Commands**](Gcode-Support)
* [**Configuration Commands**](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)
* [**JSON Formatting**](https://github.com/synthetos/TinyG/wiki/JSON-Operation)

## Status Codes 
The following status code are defined. Status codes are returned in the second element of the footer array, e.g.
<pre>
"f":[1,0,255,1234]   -- '0' is the OK code
</pre>

_Earlier revisions (build 380.xx) may be using [these status codes](#legacy-status-codes)_

	Code | Label | Description
	---------|:--------------|:-------------
	 | **Low level codes** | System and comms status
	<a name="0">0</a> | OK | universal OK code
	<a name="1">1</a> | ERROR | generic error return
	<a name="2">2</a> | EAGAIN | function would block here
	<a name="3">3</a> | NOOP | function had no-operation
	<a name="4">4</a> | COMPLETE | operation is complete
	<a name="5">5</a> | TERMINATE | operation terminated (gracefully)
	<a name="6">6</a> | RESET | operation was hard reset (sig kill)
	<a name="7">7</a> | EOL | returned end-of-line
	<a name="8">8</a> | EOF | returned end-of-file
	<a name="9">9</a> | FILE_NOT_OPEN
	<a name="10">10</a> | FILE_SIZE_EXCEEDED
	<a name="11">11</a> | NO_SUCH_DEVICE
	<a name="12">12</a> | BUFFER_EMPTY
	<a name="13">13</a> | BUFFER_FULL
	<a name="14">14</a> | BUFFER_FULL_FATAL
	<a name="15">15</a> | INITIALIZING | initializing - not ready for use
	<a name="16">16</a> | ENTERING_BOOT_LOADER | emitted by boot loader, not TinyG
	<a name="17">17</a> | FUNCTION_IS_STUBBED
	18 - <a name="19">19</a> | Reserved 
	 | **Internal System Errors**
<a name="20">20</a> | INTERNAL_ERROR | unrecoverable internal error
	<a name="21">21</a> | INTERNAL_RANGE_ERROR
	<a name="22">22</a> | FLOATING_POINT_ERROR | number conversion error
	<a name="23">23</a> | DIVIDE_BY_ZERO
	<a name="24">24</a> | INVALID_ADDRESS
	<a name="25">25</a> | READ_ONLY_ADDRESS
	<a name="26">26</a> | INIT_FAIL
	<a name="27">27</a> | ALARMED
	<a name="28">28</a> | FAILED_TO_GET_PLANNER_BUFFER
	<a name="29">29</a> | GENERIC_EXCEPTION_REPORT | used for test
	<a name="30">30</a> | PREP_LINE_MOVE_TIME_IS_INFINITE
	<a name="31">31</a> | PREP_LINE_MOVE_TIME_IS_NAN
	<a name="32">32</a> | FLOAT_IS_INFINITE
	<a name="33">33</a> | FLOAT_IS_NAN
	<a name="34">34</a> | PERSISTENCE_ERROR
	<a name="35">35</a> | BAD_STATUS_REPORT_SETTING
	36 – <a name="89">89</a> | Reserved
	 | **Assertion Failures** | Build down from 99 until they meet system errors
	<a name="90">90</a> | CONFIG_ASSERTION_FAILURE
	<a name="91">91</a> | XIO_ASSERTION_FAILURE
	<a name="92">92</a> | ENCODER_ASSERTION_FAILURE
	<a name="93">93</a> | STEPPER_ASSERTION_FAILURE
	<a name="94">94</a> | PLANNER_ASSERTION_FAILURE
	<a name="95">95</a> | CANONICAL_MACHINE ASSERTION_FAILURE
	<a name="96">96</a> | CONTROLLER_ASSERTION_FAILURE
	<a name="97">97</a> | STACK_OVERFLOW
	<a name="98">98</a> | MEMORY_FAULT | generic memory corruption detected
	<a name="99">99</a> | GENERIC_ASSERTION_FAILURE | unclassified assertion failure
	 | **Application and Data Input Errors**
	 | **Generic Data Input Errors**
	<a name="100">100</a> | UNRECOGNIZED_NAME | parser didn't recognize the command
	<a name="101">101</a> | INVALID_OR_MALFORMED_COMMAND | malformed line to parser
	<a name="102">102</a> | BAD_NUMBER_FORMAT | number format error
	<a name="103">103</a> | BAD_UNSUPPORTED_TYPE | number or JSON type is not supported
	<a name="104">104</a> | PARAMETER_IS_READ_ONLY | this parameter is read-only - cannot be set
	<a name="105">105</a> | PARAMETER_CANNOT_BE_READ | this parameter is not readable
	<a name="106">106</a> | COMMAND_NOT_ACCEPTED | command cannot be accepted at this time
	<a name="107">107</a> | INPUT_EXCEEDS_MAX_LENGTH | input string too long
	<a name="108">108</a> | INPUT_LESS_THAN_MIN_VALUE | value is under minimum
	<a name="109">109</a> | INPUT_EXCEEDS_MAX_VALUE | value is over maximum
	<a name="110">110</a> | INPUT_VALUE_RANGE_ERROR | value is out-of-range
	<a name="111">111</a> | JSON_SYNTAX_ERROR | JSON input string is not well formed
	<a name="112">112</a> | JSON_TOO_MANY_PAIRS | JSON input string has too many pairs
	<a name="113">113</a> | JSON_TOO_LONG | JSON exceeds buffer size
	114 – <a name="129">129</a> | Reserved
	 | **Gcode Errors and Warnings** | Most are from NIST
	<a name="130">130</a> | GCODE_GENERIC_INPUT_ERROR | generic error for gcode input
	<a name="131">131</a> | GCODE_COMMAND_UNSUPPORTED | G command is not supported
	<a name="132">132</a> | MCODE_COMMAND_UNSUPPORTED | M command is not supported
	<a name="133">133</a> | GCODE_MODAL_GROUP_VIOLATION | gcode modal group error
	<a name="134">134</a> | GCODE_AXIS_IS_MISSING | requires at least one axis present
	<a name="135">135</a> | GCODE_AXIS_CANNOT_BE_PRESENT | error if G80 has axis words
	<a name="136">136</a> | GCODE_AXIS_IS_INVALID | axis specified that’s illegal for command
	<a name="137">137</a> | GCODE_AXIS_IS_NOT_CONFIGURED | WARNING: attempt to program an axis that is disabled
	<a name="138">138</a> | GCODE_AXIS_NUMBER_IS_MISSING | axis word is missing its value
	<a name="139">139</a> | GCODE_AXIS_NUMBER_IS_INVALID | axis word value is illegal
	<a name="140">140</a> | GCODE_ACTIVE_PLANE_IS_MISSING | active plane is not programmed
	<a name="141">141</a> | GCODE_ACTIVE_PLANE_IS_INVALID | active plane selected not valid for this command
	<a name="142">142</a> | GCODE_FEEDRATE_NOT_SPECIFIED | move has no feedrate
	<a name="143">143</a> | GCODE_INVERSE_TIME_MODE CANNOT_BE_USED | G38.2 and some canned cycles cannot accept inverse time mode
	<a name="144">144</a> | GCODE_ROTARY_AXIS CANNOT_BE_USED | G38.2 and some other commands cannot have rotary axes
	<a name="145">145</a> | GCODE_G53_WITHOUT_G0_OR_G<a name="1">1</a> | G0 or G1 must be active for G53
	<a name="146">146</a> | REQUESTED_VELOCITY EXCEEDS_LIMITS
	<a name="147">147</a> | CUTTER_COMPENSATION CANNOT_BE_ENABLED
	<a name="148">148</a> | PROGRAMMED_POINT SAME_AS_CURRENT_POINT
	<a name="149">149</a> | SPINDLE_SPEED_BELOW_MINIMUM
	<a name="150">150</a> | SPINDLE_SPEED_MAX_EXCEEDED
	<a name="151">151</a> | S_WORD_IS_MISSING
	<a name="152">152</a> | S_WORD_IS_INVALID
	<a name="153">153</a> | SPINDLE_MUST_BE_OFF
	<a name="154">154</a> | SPINDLE_MUST_BE_TURNING | some canned cycles require spindle to be turning when called
	<a name="155">155</a> | ARC_SPECIFICATION_ERROR | generic arc specification error
	<a name="156">156</a> | ARC_AXIS_MISSING FOR_SELECTED_PLANE | arc missing axis (axes) required by selected plane
	<a name="157">157</a> | ARC_OFFSETS_MISSING FOR_SELECTED_PLANE | one or both offsets are not specified
	<a name="158">158</a> | ARC_RADIUS OUT_OF_TOLERANCE | WARNING - radius arc is too large - accuracy in question
	<a name="159">159</a> | ARC_ENDPOINT IS_STARTING_POINT
	<a name="160">160</a> | P_WORD_IS_MISSING | P must be present for dwells and other functions
	<a name="161">161</a> | P_WORD_IS_INVALID | generic P value error
	<a name="162">162</a> | P_WORD_IS_ZERO
	<a name="163">163</a> | P_WORD_IS_NEGATIVE | dwells require positive P values
	<a name="164">164</a> | P_WORD_IS_NOT_AN_INTEGER | G10s and other commands require integer P numbers
	<a name="165">165</a> | P_WORD_IS_NOT_VALID_TOOL_NUMBER
	<a name="166">166</a> | D_WORD_IS_MISSING
	<a name="167">167</a> | D_WORD_IS_INVALID
	<a name="168">168</a> | E_WORD_IS_MISSING
	<a name="169">169</a> | E_WORD_IS_INVALID
	<a name="170">170</a> | H_WORD_IS_MISSING
	<a name="171">171</a> | H_WORD_IS_INVALID
	<a name="172">172</a> | L_WORD_IS_MISSING
	<a name="173">173</a> | L_WORD_IS_INVALID
	<a name="174">174</a> | Q_WORD_IS_MISSING
	<a name="175">175</a> | Q_WORD_IS_INVALID
	<a name="176">176</a> | R_WORD_IS_MISSING
	<a name="177">177</a> | R_WORD_IS_INVALID
	<a name="178">178</a> | T_WORD_IS_MISSING
	<a name="179">179</a> | T_WORD_IS_INVALID
	180 - 199| Reserved | reserved for Gcode errors
	 | **TinyG Errors and Warnings**
	<a name="200">200</a> | GENERIC_ERROR
	<a name="201">201</a> | MINIMUM_LENGTH_MOVE | move is less than minimum length
	<a name="202">202</a> | MINIMUM_TIME_MOVE | move is less than minimum time
	<a name="203">203</a> | MACHINE_ALARMED | machine is alarmed. Command not processed
	<a name="204">204</a> | LIMIT_SWITCH_HIT | limit switch was hit causing shutdown
	<a name="205">205</a> | PLANNER_FAILED_TO_CONVERGE | planner can throw this exception
	206 - 219| Reserved
	<a name="220">220</a> | SOFT_LIMIT_EXCEEDED | soft limit error - axis unspecified
	<a name="221">221</a> | SOFT_LIMIT_EXCEEDED_XMIN | soft limit error - X minimum
	<a name="222">222</a> | SOFT_LIMIT_EXCEEDED_XMAX | soft limit error - X maximum
	<a name="223">223</a> | SOFT_LIMIT_EXCEEDED_YMIN | soft limit error - Y minimum
	<a name="224">224</a> | SOFT_LIMIT_EXCEEDED_YMAX | soft limit error - Y maximum
	<a name="225">225</a> | SOFT_LIMIT_EXCEEDED_ZMIN | soft limit error - Z minimum
	<a name="226">226</a> | SOFT_LIMIT_EXCEEDED_ZMAX | soft limit error - Z maximum
	<a name="227">227</a> | SOFT_LIMIT_EXCEEDED_AMIN | soft limit error - A minimum
	<a name="228">228</a> | SOFT_LIMIT_EXCEEDED_AMAX | soft limit error - A maximum
	<a name="229">229</a> | SOFT_LIMIT_EXCEEDED_BMIN | soft limit error - B minimum
	<a name="230">230</a> | SOFT_LIMIT_EXCEEDED_BMAX | soft limit error - B maximum
	<a name="231">231</a> | SOFT_LIMIT_EXCEEDED_CMIN | soft limit error - C minimum
	<a name="232">232</a> | SOFT_LIMIT_EXCEEDED_CMAX | soft limit error - C maximum
	233 – <a name="239">239</a> | Reserved
	<a name="240">240</a> | HOMING_CYCLE_FAILED <a name="240">240</a> | homing cycle did not complete
	<a name="241">241</a> | HOMING_ERROR_BAD_OR_NO_AXIS 
	<a name="242">242</a> | HOMING_ERROR_SWITCH_MISCONFIGURATION 
	<a name="243">243</a> | HOMING_ERROR_ZERO_SEARCH_VELOCITY 
	<a name="244">244</a> | HOMING_ERROR_ZERO_LATCH_VELOCITY 
	<a name="245">245</a> | HOMING_ERROR_TRAVEL_MIN_MAX_IDENTICAL 
	<a name="246">246</a> | HOMING_ERROR_NEGATIVE_LATCH_BACKOFF 
	<a name="247">247</a> | HOMING_ERROR_SEARCH_FAILED 
	<a name="248">248</a> | Reserved 
	<a name="249">249</a> | Reserved 
	<a name="250">250</a> | PROBE_CYCLE_FAILED | probing cycle did not complete
	<a name="251">251</a> | PROBE_ENDPOINT IS_STARTING_POINT 
	<a name="252">252</a> | JOGGING_CYCLE_FAILED | jogging cycle did not complete


## Status Report Enumerations
Values commonly reported in status reports are listed below. See canonical_machine.h for the actual code.

	Token | Value | Description
	------|-------|-------------
	"stat" || Machine State
	| 0 | machine is initializing
	| 1 | machine is ready for use
	| 2 | machine is in alarm state (shut down)
	| 3 | program stop or no more blocks (M0, M1, M60)
	| 4 | program end via M2, M30
	| 5 | motion is running
	| 6 | motion is holding
	| 7 | probe cycle active
	| 8 | machine is running (cycling)
	| 9 | machine is homing 
	"momo" | | Gcode Motion Mode
	| 0 | G0 - straight traverse
	| 1 | G1 - straight feed
	| 2 | G3/G4 - arc traverse
	| 3 | G80 - cancel motion mode (no motion mode active)
	"unit" | | Gcode Units
	| 0 | G20 - inches mode
	| 1 | G21 - millimeter mode
	"macs" || Raw Machine State
	| 0 | machine is initializing
	| 1 | machine is ready for use
	| 2 | machine is in alarm state (shut down)
	| 3 | program stop or no more blocks (M0, M1, M60)
	| 4 | program end via M2, M30
	| 5 | machine is in cycle
	"cycs" || Cycle State
	| 0 | cycle off (not in cycle)
	| 1 | normal machine cycle
	| 2 | probe cycle
	| 3 | homing cycle
	| 4 | jog cycle
	"mots" || Motion State
	| 0 | motion off
	| 1 | motion run
	| 2 | motion hold
	"hold" || Feedhold State
	| 0 | feedhold off (not in feedhold)
	| 1 | feedhold sync phase
	| 2 | feedhold planning phase
	| 3 | feedhold deceleration phase
	| 4 | feedhold holding
	| 5 | feedhold end hold
	"coor" || Gcode Coordinate System
	| 0 | G53 - machine coordinate system
	| 1 | G54 - coordinate system 1
	| 2 | G55 - coordinate system 2
	| 3 | G56 - coordinate system 3
	| 4 | G57 - coordinate system 4
	| 5 | G58 - coordinate system 5
	| 6 | G59 - coordinate system 6
	"plan" || Gcode Arc Plane Selected
	| 0 | G17 - XY plane
	| 1 | G18 - XZ plane
	| 2 | G19 - YZ plane
	"path" || Gcode Path Control Mode
	| 0 | G61 - Exact stop mode
	| 1 | G61.1 - Exact path mode
	| 2 | G64 - Continuous mode
	"dist" || Gcode Distance Mode
	| 0 | G90 - Absolute distance mode
	| 1 | G91 - Incremental distance mode
	"frmo" || Gcode Feed Rate Mode
	| 0 | G94 - Normal time mode (cancel inverse time mode)
	| 1 | G93 - Inverse time mode

## ASCII Character Usage

Hex | char | name | used by
	---- | ---- | ---------- | --------------------
	0x00 | NUL | null | everything; may be returned to Kinen SPI when no device is plugged in
	0x01 | SOH | ctrl-A
	0x02 | STX | ctrl-B | Kinen SPI protocol - Request character
	0x03 | ETX | ctrl-C | Kinen SPI protocol - Return no character
	0x04 | EOT | ctrl-D
	0x05 | ENQ | ctrl-E
	0x06 | ACK | ctrl-F
	0x07 | BEL | ctrl-G
	0x08 | BS | ctrl-H
	0x09 | HT | ctrl-I
	0x0A | LF | ctrl-J
	0x0B | VT | ctrl-K
	0x0C | FF | ctrl-L
	0x0D | CR | ctrl-M
	0x0E | SO | ctrl-N
	0x0F | SI | ctrl-O
	0x10 | DLE | ctrl-P
	0x11 | DC1 | ctrl-Q | XOFF
	0x12 | DC2 | ctrl-R		
	0x13 | DC3 | ctrl-S | XON
	0x14 | DC4 | ctrl-T		
	0x15 | NAK | ctrl-U | Kinen SPI protocol - Slave buffer overrun detected
	0x16 | SYN | ctrl-V
	0x17 | ETB | ctrl-W | 
	0x18 | CAN | ctrl-X | TinyG / grbl software reset
	0x19 | EM | ctrl-Y
	0x1A | SUB | ctrl-Z
	0x1B | ESC | ctrl-[ | Used to enter the boot loader - ESC is an AVRdude convention
	0x1C | FS | ctrl-\
	0x1D | GS | ctrl-]
	0x1E | RS | ctrl-^
	0x1F | US | ctrl-_
	0x20 | space | | Gcode blocks, other uses
	0x21 | ! | excl point | TinyG feedhold command character
	0x22 | " | quote | JSON notation
	0x23 | # | number | Gcode parameter prefix; JSON topic prefix
	0x24 | $ | dollar | TinyG / grbl out-of-cycle settings prefix
	0x25 | & | ampersand | universal symbol for logical AND (not used here)
	0x26 | % | percent | TinyG Queue Flush command character
	0x27 | ' | single quote	
	0x28 | ( | open paren | Gcode comments
	0x29 | ) | close paren | Gcode comments
	0x2A | * | asterisk | Gcode expressions
	0x2B | + | plus | Gcode numbers, parameters and expressions
	0x2C | , | comma | JSON notation
	0x2D | - | minus | Gcode numbers, parameters and expressions
	0x2E | . | period | Gcode numbers, parameters and expressions
	0x2F | / | fwd slash | Gcode expressions & block delete char
	0x3A | : | colon | JSON notation
	0x3B | ; | semicolon | Gcode comment - Mach3 and reprap style
	0x3C | < | less than | Gcode expressions
	0x3D | = | equals | Gcode expressions
	0x3E | > | greater than | Gcode expressions
	0x3F | ? | question mk | TinyG / grbl query
	0x40 | @ | at symbol | JSON address prefix
	0x41 – 0x5A | | chars | regular old alphanumeric characters 
	0x5B | [ | open bracket | Gcode expressions
	0x5C | \ | backslash | JSON notation (escape)
	0x5D | ] | close bracket | Gcode expressions
	0x5E | ^ | caret | Reserved for TinyG in-cycle command prefix
	0x5F | _ | underscore
	0x60 | ` | grave accent	
	0x7B | { | open brace | JSON notation (start object)
	0x7C | pipe | pipe | universal symbol for logical OR (not used here)
	0x7D | } | close brace | JSON notation (end object)
	0x7E | ~ | tilde | TinyG cycle start command character
	0x7F | DEL
	0xFF | DEL | | may be returned to Kinen SPI when no device is plugged in 

## Notes
### Status Code 27 - System Alarmed
System alarms occur when the system must halt operation for some reason. You will see a message like this:
<pre>
{“er”:{“fb”:370.08,”st”:27,”msg”:”System alarmed”,”val”:1}}
</pre>

Alarms may occur when:
* A limit switch has been hit. This is normal if you have limit switches enabled. This can also happen sporadically if there is noise on the limit switch line. 
* Memory fault or corruption has been detected. This indicates a program error. Please report this to Synthetos. 

###Legacy Status Codes

	errno | name | Description
	---------|--------------|-------------
	0 | TG_OK | universal OK code (function completed successfully)
	 |  **Low level codes** | typically system and communications status
	1 | TG_ERROR | generic error return (EPERM)
	2 | TG_EAGAIN | function would block here (call again)
	3 | TG_NOOP | function had no-operation
	4 | TG_COMPLETE | operation is complete
	5 | TG_TERMINATE | operation terminated (gracefully)
	6 | TG_RESET | operation was hard reset (sig kill)
	7 | TG_EOL | function returned end-of-line or end-of-message
	8 | TG_EOF | function returned end-of-file 
	9 | TG_FILE_NOT_OPEN 
	10 | TG_FILE_SIZE_EXCEEDED 
	11 | TG_NO_SUCH_DEVICE 
	12 | TG_BUFFER_EMPTY 
	13 | TG_BUFFER_FULL 
	14 | TG_BUFFER_FULL_FATAL 
	15 | TG_INITIALIZING | initializing - not ready for use
	16 | TG_ENTERING_BOOT_LOADER | entering boot loader from application
	17-19 | TG_ERROR_16 - TG_ERROR_19 | reserved
	 | **Internal errors** | typically unrecoverable
	20 | TG_INTERNAL_ERROR | unrecoverable internal error
	21 | TG_INTERNAL_RANGE_ERROR | number range error other than by user input
	22 | TG_FLOATING_POINT_ERROR | number conversion error
	23 | TG_DIVIDE_BY_ZERO
	24 | TG_INVALID_ADDRESS
	25 | TG_READ_ONLY_ADDRESS
	26 | TG_INIT_FAIL | Initialization failure
	[27](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Codes#status-code-27---system-shutdown) | TG_SHUTDOWN | System alarmed and went into shutdown
	28 | TG_MEMORY_FAULT | Memory fault or corruption detected
	29-39 | TG_ERROR_29 - TG_ERROR_39 | reserved
	 | **Input errors** | typically data problems on inputs
	40 | TG_UNRECOGNIZED_COMMAND | parser didn't recognize the command
	41 | TG_EXPECTED_COMMAND_LETTER | malformed line to parser
	42 | TG_BAD_NUMBER_FORMAT | number format error
	43 | TG_INPUT_EXCEEDS_MAX_LENGTH | input string is too long 
	44 | TG_INPUT_VALUE_TOO_SMALL | value is under minimum for this parameter
	45 | TG_INPUT_VALUE_TOO_LARGE | value is over maximum for this parameter
	46 | TG_INPUT_VALUE_RANGE_ERROR | input error: value is out-of-range for this parameter
	47 | TG_INPUT_VALUE_UNSUPPORTED | input error: value is not supported for this parameter
	48 | TG_JSON_SYNTAX_ERROR | JSON string is not well formed
	49 | TG_JSON_TOO_MANY_PAIRS | JSON string or has too many name:value pairs
	50 | TG_JSON_TOO_LONG | JSON output string too long for output buffer
	51 | TG_NO_BUFFER_SPACE | Buffer pool is full and cannot perform this operation
	52 - 59 | TG_ERROR_51 - TG_ERROR_59 | reserved
	 | **Gcode and machining errors** | application specific errors for Gcode problems
	60 | TG_MINIMUM_LENGTH_MOVE_ERROR | move is below minimum length or zero
	61 | TG_MINIMUM_TIME_MOVE_ERROR | move is below minimum time or zero
	62 | TG_GCODE_BLOCK_SKIPPED | block was skipped - usually because it was is too short
	63 | TG_GCODE_INPUT_ERROR | general error for gcode input 
	64 | TG_GCODE_FEEDRATE_ERROR | no feedrate specified
	65 | TG_GCODE_AXIS_WORD_MISSING | command requires at least one axis present
	66 | TG_MODAL_GROUP_VIOLATION | gcode modal group error
	67 | TG_HOMING_CYCLE_FAILED | homing cycle did not complete
	68 | TG_MAX_TRAVEL_EXCEEDED 
	69 | TG_MAX_SPINDLE_SPEED_EXCEEDED 
	70 | TG_ARC_SPECIFICATION_ERROR
	71-79 | TG_ERROR_71 - TG_ERROR_79 | reserved
	80-99 | Expansion | Expansion ranges
	100-119 | Expansion  | 
	etc. | Expansion | 