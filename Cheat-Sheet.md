# Response Codes

	errno | name | Description
	---------|--------------|-------------
	0 | TG_OK 0 | function completed OK
	 |  **Low level status and communications**
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
	16-19 | TG_ERROR_16 - TG_ERROR_19 | reserved
	 | Internal errors (like HTTP 500's)
	20 | TG_INTERNAL_ERROR | unrecoverable internal error
	21 | TG_INTERNAL_RANGE_ERROR | number range error other than by user input
	22 | TG_FLOATING_POINT_ERROR | number conversion error
	23 | TG_DIVIDE_BY_ZERO
	24 | TG_INVALID_ADDRESS
	25 | TG_READ_ONLY_ADDRESS
	26-39 | TG_ERROR_26 - TG_ERROR_39 | reserved
	 | Input errors (like HTTP 400's)
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
	50 | TG_NO_BUFFER_SPACE | Buffer pool is full and cannot perform this operation
	51 - 59 | TG_ERROR_51 - TG_ERROR_59 | reserved
	 | Gcode and machining errors | Application specific
	60 | TG_ZERO_LENGTH_MOVE | move is zero length
	61 | TG_GCODE_BLOCK_SKIPPED | block was skipped - usually because it was is too short
	62 | TG_GCODE_INPUT_ERROR | general error for gcode input 
	63 | TG_GCODE_FEEDRATE_ERROR | no feedrate specified
	64 | TG_GCODE_AXIS_WORD_MISSING | command requires at least one axis present
	65 | TG_MODAL_GROUP_VIOLATION | gcode modal group error
	66 | TG_HOMING_CYCLE_FAILED | homing cycle did not complete
	67 | TG_MAX_TRAVEL_EXCEEDED 
	68 | TG_MAX_SPINDLE_SPEED_EXCEEDED 
	69 | TG_ARC_SPECIFICATION_ERROR | arc specification error
	70-79 | TG_ERROR_70 - TG_ERROR_79 | reserved
	80-99 | Expansion | Expansion ranges
	100-119 | Expansion  | 
	etc. | Expansion | 


# Gcode

# Commands