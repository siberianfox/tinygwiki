This page describes what you can do from the command line in TinyG. Basically, it's entering configuration values and commands, and sending lines of Gcode.


# Terms, Concepts and Background
This page describes how configuration works in **text mode**. All configs on this page are also accessible in [**JSON mode**](https://github.com/synthetos/TinyG/wiki/JSON-Operation). Well almost. Those few commands that apply to only one mode or the other are noted.

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

### Groups 
A group is a collection of related tokens. Groups are used to specify all parameters for a motor, an axis, a PWM channel, or other logical grouping. A group is similar in concept to a RESTful resource or composite. The list of groups is:


When typing in configs a '$' must be the first character of the line. Input is case insensitive.

Configuration is non-moded; that is, configuration lines and Gcode blocks can be used without changing modes. However, it is not recommended to intermingle configs with Gcode blocks, as the EEPROM writes can interfere with step generation and serial transmission (interrupts). Please note: in some future release config commands that arrive during a gcode cycle may be rejected.

_CAVEAT: At the current time because of various limitations of the Xmega we recommend waiting for each config command to send a response before sending the next command. This gives allows the system to persist the data to EEPROM, because during that interval the board cannot reliably receive serial input._

## Units
Configuration can be performed in either inches or millimeters mode. All values entered and responses provided will be in the current Gcode UNITS setting: G20 for inches or G21 for mm. Most of the examples below are in mm, but could just as easily be input in inches. 

_NOTE 1: In text mode the differences are obvious in the responses. In JSON there is no indication - so best to issue {"gc":"g20"} or {"gc":"g21"} at the start of every config session._

_NOTE 2: internally, everything is converted to mm mode, so if you do a bunch of settings in one units mode then change to the other the settings are still valid. Try it. Change back and forth by issuing in sequence: $x, G20, $x, G21, $x_

## Groups
Groups simplify configuration management and reporting by collecting related values together. The following groups are defined. All parameters within the groups are persistent (stored in EEPROM) unless noted.

	Group | Tokens | Notes
	--------|----------|-------
	axis | x y x a b c | Contain all settings for that axis
	motor | 1 2 3 4 | Contain all settings for that motor
	pwm | p1 | Contain all settings for pulse width modulation channel
	offsets | g54 g55 g56 g57 g58 g59 | Contain offset settings xyzabc in named coordinate system. 
	temp offset | g92 | Contain g92 offset settings. These are not persistent
	return to home values | g28, g30 | Contain return values in machine coordinates. These are not persistent
	pos | x y x a b c | current work position, with offsets
	mpo | x y x a b c | current machine position, no offsets, and always reports in millimeters
	ofs | x y x a b c | current offsets,  always reports in millimeters
	hom | x y x a b c e | axis homing stateus. 'e' reports homing status for Entire machine
	system | sys | Contain system parameters

To list a group type in the group prefix; for example:
* type $x to list the X group
* type $sys to list the system group (or simply $, which is shorthand)
* type $g55 to list the xyzabc offsets in the G55 coordinate system

### Uber-Groups
In text mode (not JSON mode) the following groups of groups are also available for display:

	Uber-Group | Token | Notes
	--------|----------|-------
	axis | q | All axis settings for all axes
	motor | m | All settings for all motors
	offsets | o | All offset settings including g92's
	all | $ | All settings. Invoked as $$

For example type $q to list all axis groups. 
The list of uber-groups can be seen by asking for the system help screen using $h.

## Displaying Settings and Groups
To display a setting type $<the-mnemonic-for-the=setting-you-want-to-display>. It will respond with the value taken. For example:

	Request | Response | Notes
	--------|----------|-------
	$xvm | [xvm] x_velocity_maximum      16000.000 mm/min | Show X axis maximum velocity
	$3po | [3po] m3_polarity                 0 [0,1] | Show motor 3 polarity
	$ex | [ex]  enable_xon_xoff             1 [0,1] | Show XON/XOFF setting

The following commands will display groups.
<pre>
$x    --- Show all X axis settings ---
[xam] x_axis_mode                 1 [standard]
[xvm] x_velocity_maximum      16000.000 mm/min
[xfr] x_feedrate_maximum      16000.000 mm/min
[xtm] x_travel_maximum          220.000 mm
[xjm] x_jerk_maximum     5000000000 mm/min^3
[xjd] x_junction_deviation        0.0100 mm (larger is faster)
[xsn] x_switch_min                3 [0-4]
[xsx] x_switch_max                2 [0-4]
[xsv] x_search_velocity        3000.000 mm/min
[xlv] x_latch_velocity          100.000 mm/min
[xlb] x_latch_backoff            20.000 mm
[xzb] x_zero_backoff              3.000 mm
tinyg [mm] ok> 
</pre>

<pre>
$x    --- Show all X axis settings in inches mode (issue g20 beforehand) ---
[xam] x_axis_mode                 1 [standard]
[xvm] x_velocity_maximum        629.921 in/min
[xfr] x_feedrate_maximum        629.921 in/min
[xtm] x_travel_maximum            8.661 in
[xjm] x_jerk_maximum      196850400 in/min^3
[xjd] x_junction_deviation        0.0004 in (larger is faster)
[xsn] x_switch_min                3 [0-4]
[xsx] x_switch_max                2 [0-4]
[xsv] x_search_velocity         118.110 in/min
[xlv] x_latch_velocity            3.937 in/min
[xlb] x_latch_backoff             0.787 in
[xzb] x_zero_backoff              0.118 in
</pre>

<pre>
$3    --- Show all motor 3 settings ---
[3ma] m3_map_to_axis              2 [0=X, 1=Y...]
[3sa] m3_step_angle               1.800 deg
[3tr] m3_travel_per_revolution    1.250 mm
[3mi] m3_microsteps               8 [1,2,4,8]
[3po] m3_polarity                 0 [0,1]
[3pm] m3_power_management         1 [0,1]
</pre>

<pre>
$sys  --- Show all system settings ---
[fb]  firmware_build            355.04
[fv]  firmware_version            0.95
[hv]  hardware_version            7.00
[gpl] gcode_select_plane          0 [0,1,2]
[gun] gcode_units_mode            1 [0,1]
[gco] gcode_coord_system          1 [1-6]
[gpa] gcode_path_control          2 [0,1,2]
[gdi] gcode_distance_mode         0 [0,1]
[ja]  junction_acceleration  200000 mm
[st]  switch_type                 1 [0,1]
[ic]  ignore CR or LF on RX       0 [0,1=CR,2=LF]
[ee]  enable_echo                 0 [0,1]
[ex]  enable_xon_xoff             1 [0,1]
[eq]  enable_queue_reports        1 [0,1]
[ej]  enable_json_mode            0 [0,1]
[jv]  json_verbosity              4 [0-5]
[tv]  text_verbosity              3 [0-3]
[si]  status_interval           200 ms [0=off]
[baud] USB baud rate              0 [0-6] | Show all system settings
</pre>

## Updating Settings 
To update a setting enter $token=value

Tokens are a mnemonic plus a group prefix (system settings have no prefix). The setting is taken and the value is echoed in a descriptive text line. No spaces are allowed. Numeric values can contain embedded commas. The following are examples of valid and invalid inputs. 

	Request | Response | Notes
	--------|----------|-------
	$yfr=800 | [yfr] y_feedrate_maximum        800.000 mm/min | Set Y axis feed rate maximum to 800 mm/min
	$yfr=16,000 | [yfr] y_feedrate_maximum      16000.000 mm/min |  Set Y axis feed rate maximum to 16000 mm/min
	$2po=1 | [2po] m2_polarity                 1 [0,1] | Set motor 2 polarity
	$ex=1 | [ex]  enable_xon_xoff             1 [0,1] | Enable XON/XOFF protocol
	$ted=1 | error: Unrecognized command $ted | Example of an error

