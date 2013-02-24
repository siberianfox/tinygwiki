This page describes what you can do from the command line in TinyG.

## Text Mode and JSON Mode
TinyG can operate in either text mode (command line mode) or JSON mode. In text mode TinyG accepts $ config lines and normal Gcode blocks, and returns responses as human-friendly ASCII text. Text mode responses return "ok>" or "err" to maintain compatibility with grbl parsers.

TinyG starts up in text mode if the $ej setting is set to text mode ($ej=0). TinyG will also enter text mode automatically if it receives a line with a leading $, ? or 'h'. (Note: The initial status messages returned on bootup will be in JSON format, regardless of the mode set). 

TinyG starts up in JSON mode if the $ej setting is set to JSON mode ($ej=1). TinyG will also enter JSON mode automatically if it receives a line starting with an open curly '{'. While in JSON mode all commands are  expected in JSON format and responses are returned in JSON format....

...the exception being Gcode blocks. Gcode blocks streamed to TinyG while in JSON mode can be sent with or without JSON wrappers - i.e. unwrapped Gcode will not return the system to text mode. Responses will always be returned in JSON format.

The rest of this page covers things that are common to both text and JSON operation. 
[See here for specifics about JSON mode](https://github.com/synthetos/TinyG/wiki/JSON-Operation)

## Names and Tokens 

Names are short mnemonic tokens that can be 1 to 5 characters in length. Axis and motor tokens are typically 3 characters in length including their axis or motor prefix; Non-axis and non-motor (general) tokens are 2 to 5 characters. Tokens are case insensitive and can only contain alphanumeric characters. See [TinyG Configuration](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration) for a complete list of the tokens used for settings. Some examples are provided below: 

	Token | as Text | as JSON | Description
	------|---------|---------|--------------
	xfr | $xfr | {"xfr":""} | X axis maximum feed rate
	cjm | $cjm | {"cjm":""} | C axis maximum jerk 
	1mi | $1mi | {"1mi":""} | Motor 1 microstep setting 
	home | $home | {"home":""} | Homing state
	x | $x | {"x":""} | X axis group. In text mode a group can only be queried (get). In JSON mode a group can be queried and can also be used to set any or all values in the group