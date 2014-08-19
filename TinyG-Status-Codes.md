This page contains status codeas and other enumerations that may be returned in status reports
See also:
* [**Gcode Commands**](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support)
* [**Configuration Commands**](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)
* [**JSON Formatting**](https://github.com/synthetos/TinyG/wiki/JSON-Operation)


	errno | name | Description
	---------|--------------|-------------
	0 | TG_OK | universal OK code (function completed successfully)
	|  **Low level codes** | typically system and communications status
	0 | STAT_OK 0 | function completed OK
	1 | STAT_ERROR 1 | generic error return (EPERM)
	2 | STAT_EAGAIN 2 | function would block here (call again)
	3 | STAT_NOOP 3 | function had no-operation
	4 | STAT_COMPLETE 4 | operation is complete
	5 | STAT_TERMINATE 5 | operation terminated (gracefully)
	6 | STAT_RESET 6 | operation was hard reset (sig kill)
	7 | STAT_EOL 7 | function returned end-of-line
	8 | STAT_EOF 8 | function returned end-of-file
	9 | STAT_FILE_NOT_OPEN
	10 | STAT_FILE_SIZE_EXCEEDED
	11 | STAT_NO_SUCH_DEVICE
	12 | STAT_BUFFER_EMPTY
	13 | STAT_BUFFER_FULL
	14 | STAT_BUFFER_FULL_FATAL
	15 | STAT_INITIALIZING 15 | initializing - not ready for use
	16 | STAT_ENTERING_BOOT_LOADER 16 | this code actually emitted from boot loader, not TinyG
	17 | STAT_FUNCTION_IS_STUBBED
	18 | STAT_ERROR_18
	19 | STAT_ERROR_19 19 | NOTE: XIO codes align to here
	| **Internal errors and startup messages** |
	20 | STAT_INTERNAL_ERROR 20 | unrecoverable internal error
	21 | STAT_INTERNAL_RANGE_ERROR 21 | number range other than by user input
	22 | STAT_FLOATING_POINT_ERROR 22 | number conversion error
	23 | STAT_DIVIDE_BY_ZERO |
	24 | STAT_INVALID_ADDRESS |
	25 | STAT_READ_ONLY_ADDRESS 25
	26 | STAT_INIT_FAIL 26
	27 | STAT_ALARMED 27
	28 | STAT_FAILED_TO_GET_PLANNER_BUFFER 28
	29 | STAT_GENERIC_EXCEPTION_REPORT 29 | used for test
	30 | STAT_PREP_LINE_MOVE_TIME_IS_INFINITE 30
	31 | STAT_PREP_LINE_MOVE_TIME_IS_NAN 31
	32 | STAT_FLOAT_IS_INFINITE 32
	33 | STAT_FLOAT_IS_NAN 33
	34 | STAT_PERSISTENCE_ERROR 34
	35 | STAT_BAD_STATUS_REPORT_SETTING 35
	36 – 89 | Reserved
	| **Assertion failures** |build down from 99 until they meet the system internal errors
	90 | STAT_CONFIG_ASSERTION_FAILURE 90
	91 | STAT_XIO_ASSERTION_FAILURE 91
	92 | STAT_ENCODER_ASSERTION_FAILURE 92
	93 | STAT_STEPPER_ASSERTION_FAILURE 93
	94 | STAT_PLANNER_ASSERTION_FAILURE 94
	95 | STAT_CANONICAL_MACHINE_ASSERTION_FAILURE 95
	96 | STAT_CONTROLLER_ASSERTION_FAILURE 96
	97 | STAT_STACK_OVERFLOW 97
	98 | STAT_MEMORY_FAULT 98 | generic memory corruption detected by magic numbers
	99 | STAT_GENERIC_ASSERTION_FAILURE 99 | generic assertion failure - unclassified
	| ** Application and data input errors** |
	| **Generic data input errors** |
	100 | STAT_UNRECOGNIZED_NAME 100 | parser didn't recognize the name
	101 | STAT_MALFORMED_COMMAND_INPUT 101 | malformed line to parser
	102 | STAT_BAD_NUMBER_FORMAT 102 | number format error
	103 | STAT_INPUT_EXCEEDS_MAX_LENGTH 103 | input string is too long
	104 | STAT_INPUT_VALUE_TOO_SMALL 104 | input error: value is under minimum
	105 | STAT_INPUT_VALUE_TOO_LARGE 105 | input error: value is over maximum
	106 | STAT_INPUT_VALUE_RANGE_ERROR 106 | input error: value is out-of-range
	107 | STAT_INPUT_VALUE_UNSUPPORTED 107 | input error: value is not supported
	108 | STAT_JSON_SYNTAX_ERROR 108 | JSON input string is not well formed
	109 | STAT_JSON_TOO_MANY_PAIRS 109 | JSON input string has too many JSON pairs
	110 | STAT_JSON_TOO_LONG 110 | JSON output exceeds buffer size
	111 | STAT_CONFIG_NOT_TAKEN 111 | configuration value not taken while in machining cycle
	112 | STAT_COMMAND_NOT_ACCEPTED 112 | command cannot be accepted at this time
	113 – 129 | Reserved |
	| **Gcode errors and warnings** | Most originate from NIST - by concept if not by number
	130 | STAT_GCODE_GENERIC_INPUT_ERROR 130 | generic error for gcode input
	131 | STAT_GCODE_COMMAND_UNSUPPORTED 131 | G command is not supported
	132 | STAT_MCODE_COMMAND_UNSUPPORTED 132 | M command is not supported
	133 | STAT_GCODE_MODAL_GROUP_VIOLATION 133 | gcode modal group error
	134 | STAT_GCODE_AXIS_IS_MISSING 134 | command requires at least one axis present
	135 | STAT_GCODE_AXIS_CANNOT_BE_PRESENT 135 | error if G80 has axis words
	136 | STAT_GCODE_AXIS_IS_INVALID 136 | an axis is specified that is illegal for the command
	137 | STAT_GCODE_AXIS_IS_NOT_CONFIGURED 137 | WARNING: attempt to program an axis that is disabled
	138 | STAT_GCODE_AXIS_NUMBER_IS_MISSING 138 | axis word is missing its value
	139 | STAT_GCODE_AXIS_NUMBER_IS_INVALID | axis word value is illegal
	140 | STAT_GCODE_ACTIVE_PLANE_IS_MISSING 140 | active plane is not programmed
	141 | STAT_GCODE_ACTIVE_PLANE_IS_INVALID 141 | active plane selected is not valid for this command
	142 | STAT_GCODE_FEEDRATE_NOT_SPECIFIED 142 | move has no feedrate
	143 | STAT_GCODE_INVERSE_TIME_MODE_CANNOT_BE_USED 143 | G38.2 and some canned cycles cannot accept inverse time mode
	144 | STAT_GCODE_ROTARY_AXIS_CANNOT_BE_USED 144 | G38.2 and some other commands cannot have rotary axes
	145 | STAT_GCODE_G53_WITHOUT_G0_OR_G1 145 | G0 or G1 must be active for G53
	146 | STAT_REQUESTED_VELOCITY_EXCEEDS_LIMITS 146 |
	147 | STAT_CUTTER_COMPENSATION_CANNOT_BE_ENABLED 147 |
	148 | STAT_PROGRAMMED_POINT_SAME_AS_CURRENT_POINT 148 |
	149 | STAT_SPINDLE_SPEED_BELOW_MINIMUM 149 |
	150 | STAT_SPINDLE_SPEED_MAX_EXCEEDED 150 |
	151 | STAT_S_WORD_IS_MISSING 151 |
	152 | STAT_S_WORD_IS_INVALID 152 |
	153 | STAT_SPINDLE_MUST_BE_OFF 153 |
	154 | STAT_SPINDLE_MUST_BE_TURNING 154 | some canned cycles require spindle to be turning when called
	155 | STAT_ARC_SPECIFICATION_ERROR 155 | generic arc specification error
	156 | STAT_ARC_AXIS_MISSING_FOR_SELECTED_PLANE 156 | arc is missing axis (axes) required by selected plane
	157 | STAT_ARC_OFFSETS_MISSING_FOR_SELECTED_PLANE 157 // one or both offsets are not specified
	158 | STAT_ARC_RADIUS_OUT_OF_TOLERANCE 158 | WARNING - radius arc is too large - accuracy in question
	159 | STAT_ARC_ENDPOINT_IS_STARTING_POINT 159
	160 | STAT_P_WORD_IS_MISSING 160 | P must be present for dwells and other functions
	161 | STAT_P_WORD_IS_INVALID 161 | generic P value error
	162 | STAT_P_WORD_IS_ZERO 162 |
	163 | STAT_P_WORD_IS_NEGATIVE 163 | dwells require positive P values
	164 | STAT_P_WORD_IS_NOT_AN_INTEGER 164 | G10s and other commands require integer P numbers
	165 | STAT_P_WORD_IS_NOT_VALID_TOOL_NUMBER 165 |
	166 | STAT_D_WORD_IS_MISSING 166 |
	167 | STAT_D_WORD_IS_INVALID 167 |
	168 | STAT_E_WORD_IS_MISSING 168 |
	169 | STAT_E_WORD_IS_INVALID 169 |
	170 | STAT_H_WORD_IS_MISSING 170 |
	171 | STAT_H_WORD_IS_INVALID 171 |
	172 | STAT_L_WORD_IS_MISSING 172 |
	173 | STAT_L_WORD_IS_INVALID 173 |
	174 | STAT_Q_WORD_IS_MISSING 174 |
	175 | STAT_Q_WORD_IS_INVALID 175 |
	176 | STAT_R_WORD_IS_MISSING 176 |
	177 | STAT_R_WORD_IS_INVALID 177 |
	178 | STAT_T_WORD_IS_MISSING 178 |
	179 | STAT_T_WORD_IS_INVALID 179 |
	180 - 199| Reserved | reserved for Gcode errors
	| **TinyG errors and warnings |
	200 | STAT_GENERIC_ERROR 200 |
	201 | STAT_MINIMUM_LENGTH_MOVE 201 | move is less than minimum length
	202 | STAT_MINIMUM_TIME_MOVE 202 | move is less than minimum time
	203 | STAT_MACHINE_ALARMED 203 | machine is alarmed. Command not processed
	204 | STAT_LIMIT_SWITCH_HIT 204 | a limit switch was hit causing shutdown
	205 | STAT_PLANNER_FAILED_TO_CONVERGE 205 | trapezoid generator can through this exception
	206 - 219| Reserved
	220 | STAT_SOFT_LIMIT_EXCEEDED 220 | soft limit error - axis unspecified
	221 | STAT_SOFT_LIMIT_EXCEEDED_XMIN 221 | soft limit error - X minimum
	222 | STAT_SOFT_LIMIT_EXCEEDED_XMAX 222 | soft limit error - X maximum
	223 | STAT_SOFT_LIMIT_EXCEEDED_YMIN 223 | soft limit error - Y minimum
	224 | STAT_SOFT_LIMIT_EXCEEDED_YMAX 224 | soft limit error - Y maximum
	225 | STAT_SOFT_LIMIT_EXCEEDED_ZMIN 225 | soft limit error - Z minimum
	226 | STAT_SOFT_LIMIT_EXCEEDED_ZMAX 226 | soft limit error - Z maximum
	227 | STAT_SOFT_LIMIT_EXCEEDED_AMIN 227 | soft limit error - A minimum
	228 | STAT_SOFT_LIMIT_EXCEEDED_AMAX 228 | soft limit error - A maximum
	229 | STAT_SOFT_LIMIT_EXCEEDED_BMIN 229 | soft limit error - B minimum
	230 | STAT_SOFT_LIMIT_EXCEEDED_BMAX 220 | soft limit error - B maximum
	231 | STAT_SOFT_LIMIT_EXCEEDED_CMIN 231 | soft limit error - C minimum
	232| STAT_SOFT_LIMIT_EXCEEDED_CMAX 232 | soft limit error - C maximum
	233 – 239 } Reserved
	240 | STAT_HOMING_CYCLE_FAILED 240 | homing cycle did not complete
	241 | STAT_HOMING_ERROR_BAD_OR_NO_AXIS 241
	242 | STAT_HOMING_ERROR_ZERO_SEARCH_VELOCITY 242
	243 | STAT_HOMING_ERROR_ZERO_LATCH_VELOCITY 243
	244 | STAT_HOMING_ERROR_TRAVEL_MIN_MAX_IDENTICAL 244
	245 | STAT_HOMING_ERROR_NEGATIVE_LATCH_BACKOFF 245
	246 | STAT_HOMING_ERROR_SWITCH_MISCONFIGURATION 246
	247 – 249 | Reserved
	250 | STAT_PROBE_CYCLE_FAILED 250 | probing cycle did not complete
	251 | STAT_PROBE_ENDPOINT_IS_STARTING_POINT 251
	252 | STAT_JOGGING_CYCLE_FAILED 252 | jogging cycle did not complete



## Status Codes
Status codes are returned in the second element of the footer array, e.g.
<pre>
"f":[1,0,255,1234]
</pre>

Codes:

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