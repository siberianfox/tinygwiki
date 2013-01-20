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

// Internal errors and startup messages
#define	TG_INTERNAL_ERROR 20			// unrecoverable internal error
#define	TG_INTERNAL_RANGE_ERROR 21		// number range other than by user input
#define	TG_FLOATING_POINT_ERROR 22		// number conversion error
#define	TG_DIVIDE_BY_ZERO 23
#define	TG_INVALID_ADDRESS 24
#define	TG_READ_ONLY_ADDRESS 25
#define	TG_ERROR_26 26
#define	TG_ERROR_27 27
#define	TG_ERROR_28 28
#define	TG_ERROR_29 29
#define	TG_ERROR_30 30
#define	TG_ERROR_31 31
#define	TG_ERROR_32 32
#define	TG_ERROR_33 33
#define	TG_ERROR_34 34
#define	TG_ERROR_35 35
#define	TG_ERROR_36 36
#define	TG_ERROR_37 37
#define	TG_ERROR_38 38
#define	TG_ERROR_39 39

// Input errors (400's, if you will)
#define	TG_UNRECOGNIZED_COMMAND 40		// parser didn't recognize the command
#define	TG_EXPECTED_COMMAND_LETTER 41	// malformed line to parser
#define	TG_BAD_NUMBER_FORMAT 42			// number format error
#define	TG_INPUT_EXCEEDS_MAX_LENGTH 43	// input string is too long 
#define	TG_INPUT_VALUE_TOO_SMALL 44		// input error: value is under minimum
#define	TG_INPUT_VALUE_TOO_LARGE 45		// input error: value is over maximum
#define	TG_INPUT_VALUE_RANGE_ERROR 46	// input error: value is out-of-range
#define	TG_INPUT_VALUE_UNSUPPORTED 47	// input error: value is not supported
#define	TG_JSON_SYNTAX_ERROR 48			// JSON string is not well formed
#define	TG_JSON_TOO_MANY_PAIRS 49		// JSON string or has too many JSON pairs
#define	TG_NO_BUFFER_SPACE 50			// Buffer pool is full and cannot perform this operation
#define	TG_ERROR_51 51
#define	TG_ERROR_52 52
#define	TG_ERROR_53 53
#define	TG_ERROR_54 54
#define	TG_ERROR_55 55
#define	TG_ERROR_56 56
#define	TG_ERROR_57 57
#define	TG_ERROR_58 58
#define	TG_ERROR_59 59

// Gcode and machining errors
#define	TG_ZERO_LENGTH_MOVE 60			// move is zero length
#define	TG_GCODE_BLOCK_SKIPPED 61		// block is too short - was skipped
#define	TG_GCODE_INPUT_ERROR 62			// general error for gcode input 
#define	TG_GCODE_FEEDRATE_ERROR 63		// move has no feedrate
#define	TG_GCODE_AXIS_WORD_MISSING 64	// command requires at least one axis present
#define	TG_MODAL_GROUP_VIOLATION 65		// gcode modal group error
#define	TG_HOMING_CYCLE_FAILED 66		// homing cycle did not complete
#define	TG_MAX_TRAVEL_EXCEEDED 67
#define	TG_MAX_SPINDLE_SPEED_EXCEEDED 68
#define	TG_ARC_SPECIFICATION_ERROR 69	// arc specification error
# Gcode

# Commands

