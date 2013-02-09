See also:
* [**Gcode Commands**](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support)
* [**Configuration Commands**](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)
* [**JSON Formatting**](https://github.com/synthetos/TinyG/wiki/JSON-Operation)

## Status Codes

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
	16-19 | TG_ERROR_16 - TG_ERROR_19 | reserved
	 | **Internal errors** | typically unrecoverable
	20 | TG_INTERNAL_ERROR | unrecoverable internal error
	21 | TG_INTERNAL_RANGE_ERROR | number range error other than by user input
	22 | TG_FLOATING_POINT_ERROR | number conversion error
	23 | TG_DIVIDE_BY_ZERO
	24 | TG_INVALID_ADDRESS
	25 | TG_READ_ONLY_ADDRESS
	26-39 | TG_ERROR_26 - TG_ERROR_39 | reserved
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
	50 | TG_NO_BUFFER_SPACE | Buffer pool is full and cannot perform this operation
	51 - 59 | TG_ERROR_51 - TG_ERROR_59 | reserved
	 | **Gcode and machining errors** | application specific errors for Gcode problems
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
	0x21 | ! | excl point | TinyG feedhold
	0x22 | " | quote | JSON notation
	0x23 | # | number | Gcode parameter prefix
	0x24 | $ | dollar | TinyG / grbl out-of-cycle settings prefix
	0x25 | & | ampersand | universal symbol for logical AND (not used here)
	0x26 | % | percent		
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
	0x3B | ; | semicolon
	0x3C | < | less than | Gcode expressions
	0x3D | = | equals | Gcode expressions
	0x3E | > | greater than | Gcode expressions
	0x3F | ? | question mk | TinyG / grbl query
	0x40 | @ | at symbol
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
	0x7E | ~ | tilde | TinyG cycle start
	0x7F | DEL
	0xFF | DEL | | may be returned to Kinen SPI when no device is plugged in 
