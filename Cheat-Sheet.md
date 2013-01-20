# Response Codes

	# | name | Description
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
	16 | TG_ERROR_16 | reserved
	17 | TG_ERROR_17 | reserved
	18 | TG_ERROR_18 | reserved
	19 | TG_ERROR_19 | reserved
	 | Internal errors (like HTTP 500's)
	20 | TG_INTERNAL_ERROR | unrecoverable internal error
	21 | TG_INTERNAL_RANGE_ERROR | number range error other than by user input
	22 | TG_FLOATING_POINT_ERROR | number conversion error
	23 | TG_DIVIDE_BY_ZERO
	24 | TG_INVALID_ADDRESS
	25 | TG_READ_ONLY_ADDRESS
	26-39 | TG_ERROR_26 - TG_ERROR_26 | reserved
	27 | TG_ERROR_27 27
	28 | TG_ERROR_28 28
	29 | TG_ERROR_29 29
	20 | TG_ERROR_30 30
	20 | TG_ERROR_31 31
	20 | TG_ERROR_32 32
	20 | TG_ERROR_33 33
	20 | TG_ERROR_34 34
	20 | TG_ERROR_35 35
	20 | TG_ERROR_36 36
	20 | TG_ERROR_37 37
	20 | TG_ERROR_38 38
	20 | TG_ERROR_39 39
	 | Input errors (like HTTP 400's)
	40 | TG_UNRECOGNIZED_COMMAND 40		// parser didn't recognize the command
	40 | TG_EXPECTED_COMMAND_LETTER 41	// malformed line to parser
	40 | TG_BAD_NUMBER_FORMAT 42			// number format error
	40 | TG_INPUT_EXCEEDS_MAX_LENGTH 43	// input string is too long 
	40 | TG_INPUT_VALUE_TOO_SMALL 44		// input error: value is under minimum
	40 | TG_INPUT_VALUE_TOO_LARGE 45		// input error: value is over maximum
	40 | TG_INPUT_VALUE_RANGE_ERROR 46	// input error: value is out-of-range
	40 | TG_INPUT_VALUE_UNSUPPORTED 47	// input error: value is not supported
	40 | TG_JSON_SYNTAX_ERROR 48			// JSON string is not well formed
	40 | TG_JSON_TOO_MANY_PAIRS 49		// JSON string or has too many JSON pairs
	50 | TG_NO_BUFFER_SPACE 50			// Buffer pool is full and cannot perform this operation
	50 | TG_ERROR_51 51
	50 | TG_ERROR_52 52
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	50 | TG_ERROR_53 53
	 | Gcode and machining errors | Application specific
	60 | TG_ZERO_LENGTH_MOVE 60			// move is zero length
	60 | TG_GCODE_BLOCK_SKIPPED 61		// block is too short - was skipped
	60 | TG_GCODE_INPUT_ERROR 62			// general error for gcode input 
	60 | TG_GCODE_FEEDRATE_ERROR 63		// move has no feedrate
	60 | TG_GCODE_AXIS_WORD_MISSING 64	// command requires at least one axis present
	60 | TG_MODAL_GROUP_VIOLATION 65		// gcode modal group error
	60 | TG_HOMING_CYCLE_FAILED 66		// homing cycle did not complete
	60 | TG_MAX_TRAVEL_EXCEEDED 67
	60 | TG_MAX_SPINDLE_SPEED_EXCEEDED 68
	60 | TG_ARC_SPECIFICATION_ERROR 69	// arc specification error
	 | Expansion - 80-99 | Expansion ranges
	 | Expansion - 100-119 | 
	 | Expansion - etc. | 


# Gcode

# Commands