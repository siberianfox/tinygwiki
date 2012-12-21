(last updated 12/21/12 - ash)
REVISION NOTICE: The settings on this page are for version 0.95 and later. For other firmware revisions see  [TinyG Configuration for 0.94 and 0.93](https://www.synthetos.com/wiki/index.php?title=TinyG:Configuring)

TinyG comes with a set of defaults pre-programmed to a specific machine profile. The default profile is set for a relatively slow screw machine such as the Zen Toolworks 7x12. Other default profles are settable at compile time by including the right .h file. If you are having trouble with your settings and want to revert to the default settings enter: `$defa=1`  This will revert all settings to defaults. Do a screencap of the $$ dump if you want to refer back to the current settings

## Summary / Cheat Sheet
**Motor Groups** Settings specific to a given motor. There are 4 motor groups, numbered 1,2,3,4 as labeled on the TinyG board. 

	Setting | Description | Notes
	--------|-------------|-------
	$1ma | Motor mapping to axis| Typically: $1ma=0, $2ma=1, $3ma=2, $4ma=3 to map motors 1-4 to X,Y,Z,A, respectively
	$1sa | Step angle | Typical setting is $1s1=1.8 for 1.8 degrees per step (200 steps per revolution)
	$1tr | Travel per revolution | How far does the mapped axis move per motor revolution?
	$1mi | Microsteps | Supported settings are 8,4,2 and 1
	$1po | Polarity | 0=clockwise rotation, 1=counterclockwise - although these are dependent on your motor wiring. 
	$1pm | Power management mode | 0=power shuts off when axis is not moving, 1=axis remains powered when idle

**Axis Groups** Settings specific to a given axis. There are 6 axis groups, one for each of X,Y,Z,A,B,C. Not all axes have all parameters.

	Setting | Description | Notes
	--------|-------------|-------
	$xam | Axis mode | See details for setting. Normally this is =1 "normal" 
	$xvm | Velocity maximum | Max velocity for axis, aka "traverse rate" or "seek" 
	$xfr | Feed rate maximum |
	$xtm | Travel maximum |
	$xjm | Jerk maximum |
	$xjm | Junction deviation | for cornering control
	$ara | Radius setting | Rotational axes only 
	$xsn | Minimum switch mode | 
	$xsx | Maximum switch mode | 
	$xsv | Search velocity | Homing speed during search phase (drive to switch)
	$xlv | Latch velocity | Homing speed during latch phase 
	$xzb | Zero backoff | offset from switch for zero in absolute coordinate system

**System group** 
The system group contains the following global machine and communication settings. The system group can be listed by requesting `$sys`  or {"sys":""} in JSON mode

Global Machine Settings

	Setting | Description | Notes
	--------|-------------|-------
	$fb | Firmware build | Read-only value, e.g. 351.05 
	$fv | Firmware version | Read-only value, e.g. 0.95
	$hv | hardware version | Read-write value, set to 6 for v6 and earlier boards, v7 for version 7 and later boards
	$ja | Junction acceleration | Global cornering acceleration value 
	$ml | Minimum line length | NOTE: This value is accessible only as a single value - it does not appears in SYS group listings
	$ma | Arc segment length |NOTE: This value is accessible only as a single value - it does not appears in SYS group listings
	$mt | Segment timing | Planner interpolation interval - NOTE: This value is accessible only as a single value - it does not appears in SYS group listings
	$st | Switch type | 0=NO, 1=NC

Gcode Initialization Defaults 

Gcode settings loaded on power up, abort/reset and Program End (M2 or M30). Changing these does NOT change the current Gcode mode, only the initialization settings

	Setting | Description | Notes
	--------|-------------|-------
	$gun | Default units mode | 0=inches 1=mm 
	$gpl | Default plane selection | 
	$gco | Default coordinate system |
	$gpa | Default path control mode |
	$gdi | Default distance mode | 

Communications Settings

	Setting | Description | Notes
	--------|-------------|-------
	$ic | Ignore CR / LF on RX | 
	$ee | Enable character echo |
	$ex | Enable XON/XOFF flow control |
	$eq | Enable planner queue reports |
	$ej | Enable JSON mode | 0=text mode, 1=JSON mode
	$tv | Text mode verbosity | 0=silent, 1=prompt only, 2=messages, 3=verbose
	$jv | JSON verbosity | 0=silent, 1=footer only, 2=omit Gcode body, 3=Gcode line numbers only, 4=Gcode line numbers and messages, 5=verbose
	$qr | Queue reports | 0=off, 1=filtered reports, 2=all reports
	$si | Status report interval | In ms, 0=off
	$baud | Baud rate |

Note: Status report parameters is settable in JSON only - see JSON mode for details

# Terms, Concepts and Background
This page describes how configuration works in **text mode**. All configs on this page are also accesible in [**JSON mode**](https://github.com/synthetos/TinyG/wiki/JSON-Operation). Well almost. Those few commands that apply to only one mode or the other are noted.

## Units
Configuration can be performed in either inches or millimeters mode. All values entered and responses provided will be in the current Gcode UNITS setting: G20 for inches or G21 for mm. Most of the examples below are in mm, but could just as easily be input in inches. 

_NOTE 1: In text mode the differences are obvious in the responses. In JSON there is no indication - so best to issue {"gc":"g20"} or {"gc":"g21"} at the start of every config session._

_NOTE 2: internally, everything is converted to mm mode, so if you do a bunch of settings in one units mode then change to the other the settings are still valid. Try it. Change back and forth by issuing in sequence: $x, G20, $x, G21, $x_

## Displaying Settings
To display a setting type $<the-mnemonic-for-the=setting-you-want-to-display>. It will respponse with a text line indicating the value taken. For example

	Request | Response | Notes
	--------|----------|-------
	$xvm | [xvm] x_velocity_maximum      12000.000 mm/min | Show X axis maximum velocity
	$3po | [3po] m3_polarity                 0 [0,1] | Show motor 3 polarity
	$ex | [ex]  enable_xon_xoff             1 [0,1] | Show XON/XOFF setting


The following commands will display settings groups.
<pre>
$x    --- Show all X axis settings ---
[xam] x_axis_mode                 1 [standard]
[xvm] x_velocity_maximum      12000.000 mm/min
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
</pre>

<pre>
$x    --- Show all X axis settings in inches mode (issue g20 beforehand) ---
[xam] x_axis_mode                 1 [standard]
[xvm] x_velocity_maximum        472.441 in/min
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


$1    Show all motor 1 settings (or whatever motor you want 1,2,3,4)
$x    Show all X axis settings (or whatever axis you want x,y,z,a,b,c)
--- the following are not available in JSON mode:
$m    Show all motor settings for motors 1,2,3 and 4
$n    Show all axis settings for axes x,y,z,a,b,c
$$    Show all settings (everything)
$h    Show system help screen
</pre> 
The $ must be the first character of the line, and input is case insensitive. 

Configuration is non-moded; that is, configuration lines and Gcode blocks can be freely intermixed without changing modes. However, it is not good practive to intermingle configs with Gcode blocks, and this operation may be prevented in the future - i.e. gonfig commands that arrive during a gcode cycle will be rejected.

CAVEAT: At the current time because of various limitations of the Xmega errata we recommend pausing transmission for at least 30 milliseconds after each line containing a $ command. This gives the system enough time to persist the data to EEPROM, during which time the system cannot receive new serial input. 

## Updating Settings 
To update a setting enter $token = value. Most tokens are a 2 to 4 letter mnemonic plus a motor number or axis prefix. System settings have no prefix and may be 2 to 5 letters. The following are examples of valid inputs. The setting is taken and the value is echoed on the next line. No spaces or commas are allowed.

<pre>
tinyg[mm] ok> $yfr=800                                         (Set Y axis feed rate maximum to 800 mm/min)
tinyg[mm] ok> [yfr] y_feedrate_maximum        800.000 mm/min
tinyg[mm] ok> $2po=1                                           (Set polarity for motor 2 to inverted)
tinyg[mm] ok> [2po] m2_polarity                 1 [0,1]
</pre> 

If there is an error there will be no echo or a line like one of these: 
<pre>
tinyg[mm] ok> $ted=1                                           (Set "Ted" to 1)
tinyg[mm] error: Unrecognized command $ted
</pre> 

# Settings Details
Settings are case insensitive - they are shown in upper case for emphasis only. The leading '1' can be any motor, 1-4, and the leading 'x' can be any axis (with some restrictions as noted).

## Motor Settings

### $1MA - MAp motor to axis
Axes must be input as numbers, with X=0, Y=1, Z=2, A=3, B=4 and C=5. As you might expect, mapping motor 1 to X will cause X movement to drive motor 1. The example below is a way to run a dual-Y gantry such as the LumenLabs micRo v3 or a 4 motor Shapeoko setup. Movement in Y will drive both motor2 and motor3. The mapping illustrated causes movement in X and Z to drive motors 1 and 4, respectively. 

<pre>
 $1ma=0	    Maps motor 1 to the X axis
 $2ma=1	    Maps motor 2 to the Y axis
 $3ma=2	    Maps motor 3 to the Z axis
 $4ma=1	    Maps motor 4 to the Y axis
</pre> 

### $1SA - Step Angle for the motor
This is a decimal number which is often 1.8 degrees per step, but should reflect the motor in use. You might also find 0.9, 3.6, 7.5 or other values. You can usually read this off the motor label, or divide 360 by the steps per rotation.

<pre>
 $1sa=1.8	This is a typical value for most motors. 
</pre> 

### $1TR - Travel per Revolution
This is the amount of travel of the mapped axis per motor revolution. It is in mm or inches for X, Y or Z axes, or in degrees for A, B and C axes. The XYZ value will be interpreted and echoed in the prevailing units; G20 sets inches, G21 sets mm. ABC values are always in degrees. 

For XYZ this value is usually the result of the lead screw pitch or pulley circumference. A 10 TPI leadscrew moves 0.100" / revolution. A 0.500" diameter pulley will travel 3.14159" per revolution, absent any other gearing. A typical value for a Shapeoko or Reprap belt driven machine is on the order of 35 mm per revolution. Don't take this as exact - you will need to calibrate your machine to get this setting.

For ABC the travel is entered in degrees. This value will be 360 degrees for an axis that is not geared down. The value for a geared rotary axis is 360 divided by the gear ratio. For example, a motor-driven rotary table with 4 degrees of table movement per handle rotation has a gear ratio of 90:1. The Travel per Revolution value should be set to 4. 

Note that Travel per Revolution is a motor parameter, not an axis parameter as one might think. Consider the case of a dual Y gantry with lead screws of different pitch (how weird). The travel per revolution would be different for each motor. 

<pre>
 $1tr=2.54<span class="Apple-tab-span" style="white-space:pre">	</span>Sets motor 1 to a 10 TPI travel from millimeters (2.54 mm per revolution)
</pre>

### $1MI - MIcrosteps
The following microstep values are supported: 

* 1 = no microsteps (whole steps)
* 2 = half stepping
* 4 = quarter stepping
* 8 = eighth stepping

Despite common wisdom, higher microstep values are not always better (It's like watts in the 80's - the more the better, right?). In a typical setup the total power delivered to the motor (and hence torque) will go down as you increase the microsteps, especially at higher speeds. Also, using microsteps to set the finest machine resolution is source of error as the shaft angle isn't necessarily going to be at the theoretical point. Don't just assume that 1/8 microstepping is the right setting for your application. Try out different settings to balance smoothness and power. 

<pre>
 $3mi=8	<span class="Apple-tab-span" style="white-space:pre">	</span>Set 1/8 microsteps for motor 3 
</pre>

Note: Values other than 1,2,4 and 8 are accepted. This is to support some people that have crazily wired TinyG to other drivers [like these crazy 1.3 Kw servos Saci's wired up](http://youtu.be/Nrsyejv-vwE) and like some of the common commercial stepper driver running 10x or 16x steps. If you are using the drivers on TinyG this will cause them to malfunction, so please don't do this unless you are one of those hacker types that soldered up your TinyG.

### $1PO - POlarity
Set to one of the following: 

* 0 = Normal motor polarity
* 1 = Invert motor polarity

Polarity sets which direction the motor will turn when presented with positive and negative Gcode coordinates. It's affected by how you wired the motors and by mechanical factors. Set polarity so the indicated axis travels in the correct orientation for your machine. 

Travel in X and Y is dependent on the conventions for your particular machine and CAD setup. Typically X is left/right movement, and Y is towards and away from you, but people often set up the machine to agree with the visualization their CAD program provides, and can depend on where you stand when operating the machine. Typically X+ moves to the right, X- to the left, Y+ away from you, and Y- towards you. Z is by convention the cutting axis, which is the vertical axis on a typical milling machine. Z+ should move up, and Z- should move down, into the work.

<pre>
 $3po=0<span class="Apple-tab-span" style="white-space:pre">		</span>Set polarity to normal
</pre>

### $1PM - Power Management mode
Set to one of the following: 

* 0 = Leave motor powered on when stopped
* 1 = Turn motor power off when stopped

Stepper motors actually consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Some machine configurations are OK if you shut off the power on idle (like most leadscrew machines), others are not (some belt/pulley configs and some non-cartesian robots)

<pre>
 $4pm=1         Set low-power idle for motor 4
</pre>

## Axis Settings

### $xAM - Axis Mode
Sets the function of the axis. The following modes are supported for all axes.

* 0 = Disable. All input to that axis will be ignored and the axis will not move. 
* 1 = Standard. Linear axes move in length units. Rotary axes move in degrees. 
* 2 = Inhibited. Axis values are taken into account when planning moves, but the axis will not move. Use this to perform a Z kill.

<pre>
 $zmo=2 	will inhibit the Z axis; $zmo1 will restore standard operation
</pre>

Rotary axes can have these additional modes. 

* 3 = Radius mode. In radius mode gcode values are interpreted as linear units; either inches or mm depending on the prevailing G20/G21 setting. The conversion of linear units to degrees is accomplished using the radius setting for that axis. See $aRA for details. 

### $xVM - Velocity Maximum
(Also known as traverse rate or seek rate). Sets the maximum velocity the axis will move during a traverse (G0). This is set in length units per minute for linear axes, degrees per minute for rotary axes. The max velocity will be used for all G0's from the time of reset onward. Note that the max velocity is *per-axis*.

Diagonal / multi-axis traverses will actually occur at the fastest speed the combined set of axes and the geometry will allow, and may be faster than the individual axis max velocities. For example, max velocity for X and Y are set to 1000 mm/min. For a 45 degree traverse in X and Y the toolhead would travel at 1414.21 mm/min. 

<pre>
$xvm=1200 sets X maximum velocity (G0) to 1200 mm/min - assuming G21 is active (i.e. the machine is in MM mode)
$zvm=30.0 sets Z to 30 inches per minute - assuming G20 is active
$avm=3600 sets A to 10 revolutions per minute (360 * 10)
</pre>
 
### $xFR - Feed Rate maximum
Sets the maximum velocity the axis will move during a feed in a G1, G2, or G3 move. This works similarly to maximum velocity, but instead of actually setting the speed, it only serves to establish a "do not exceed" for Gcode F words. Put another way, the maximum feed rate setting is NOT used to set the Gcode's F value; it is only a maximum.

Axis feed rates should be equal to or less than the maximum velocity. See Setting Feed Rate and Maximum Velocity for more details. 

<pre>
$xfr=1000 sets X max feed rate to 1000 mm/min - assuming G21 is active (i.e. the machine is in MM mode)
</pre> 

### $xTM - Travel Maximum
Defines the maximum extent of travel in that axis. This is used during homing.

### $xJM - Jerk Maximum
Sets the maximum jerk value for that axis. Jerk is settable independently for each axis to support machines with different dynamics per axis - such as Shapeoko with belts for X and Y, screws for Z, or Probotix with 5 pitch X and Y screws and 12 pitch Z screws. 

Jerk is in units per minutes^3, so the numbers are quite large. Some common values are shown in *millimeters* in the examples below 

<pre>
$xjm=50000000 Set X jerk to 50 million MM per min^3. This is a good value for a moderate speed machine
$zjm=25000000 A reasonable setting for a slower Z axis
$xjm=5000000000 X jerk for Shapeoko. Yes, that's 5 billion
</pre> 

The jerk term in mm is measured in mm/min^3. In inches mode it's units are inches/min^3. So the conversion from mm to inches is 1/(25.4). The same values as above are shown in inches are: 
<pre>
50,000,000 mm/min^3 is 1,968,504 in/min^3 2,000,000 would suffice
25,000,000 mm/min^3 is 984,251 in/min^3 1,000,000 would suffice
5,000,000,000 mm/min^3 is 196,850,400 in/min^3 200,000,000 would suffice
</pre> 

### $xJD - Junction Deviation
This one is somewhat complicated. Junction deviation - in combination with Junction Acceleration ($JA) from the system group - sets the velocity reduction used during cornering through the junction of two lines. The reduction is based on controlling the centripetal acceleration through the junction to the value set in JA with the junction deviation being the "tightness" of the controlling cornering circle. An explanation of what's happening here can be found on [Sonny Jeon's blog: Improving grbl cornering algorithm] (http://onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/ onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/). 

It's important to realize that the tool head does not actually follow the controlling circle - the circle is just used to set the speed of the tool through the defined path. In other words, the tool does go through the sharp corner, just not as fast. This is a Gcode G61 - Exact Path Mode operation, not a Gcode G64 - Continuous Path Mode (aka corner rounding, or splining) operation. 

JA is set globally and applies to all axes. JD is set per axis and can vary depending on the characteristics of the axis. An axis that moves more slowly should have a JD that is less than an axis that can move more quickly, as the larger the JD the faster the machine will move through the junction (i.e. a bigger controlling circle). The following example has some representative values for a Probotix Fireball V90 machine. The V90 has 5 TPI X and Y screws, and 12 TPI Z. All values in MM. 

<pre>
 $xJD 0.05<span class="Apple-tab-span" style="white-space:pre">	</span>Units are mm
 $yJD 0.05
 $zJD 0.02<span class="Apple-tab-span" style="white-space:pre">	</span>Setting Z to a smaller value means that moves with a change in the Z component will move proportionately slower depending on the contribution in Z. 
 $JA 200000<span class="Apple-tab-span" style="white-space:pre">	</span>Units are mm/min^2. As before, commas are ingored and are provided only for clarity
</pre>

### $aRA - Radius value
The radius value is used by rotational axes (A, B and C, only) to convert linear units to degrees when in radius mode or in a slaved mode. 

For example; if the A radius is set to 10 mm it means that a value of 6.28318531 mm will make the A axis travel one full revolution - as 62.383... is the circumference of the circle of radius R ( 2*PI*R, or 10 * 2 * 3.14159...) (Assuming $nTR = 360 -- see note below). Receiving the gcode block "G0 A62.83" will turn the A axis one full revolution (360 degrees) from a starting position of 0. All internal computations and settings are still in degrees - it's just that gcode units received for the axis are converted to degrees using the specified radius. 

Note that the Travel per Revolution value ($1TR) is used but unaffected in radius mode. The degrees per revolution still applies, it's just that the degrees were computed based on the radius and the Gcode axis values. See Travel per Revolution (See $1TR) in the motor group. 

### Homing Settings
Please see [TinyG Homing](https://github.com/synthetos/TinyG/wiki/TinyG-Homing) for the following homing settings

* $xSN - Minimum switch mode
* $xSX - Maximum switch mode
* $xSV - Homing Search Velocity
* $xLV - Homing Latch Velocity
* $xLB - Homing Latch Backoff
* $xZB - Homing Zero Backoff

## Coordinate System and Origin Offsets 
### $g54x - $g59c
Coordinate system offsets are the values used by G54, G55, G56, G57, G58 and G59 to define the offsets from the machine (absolute) coordinate system for X,Y,Z,A,B and C. G54-G59 correspond to coordinate systems 1-6, respectively. These can be set from Gcode using the G10 command (e.g. G10 P2 L2 X20.000 - the P word is the coordinate system, the L word is according to standard, but is ignored). 

In addition to using G10, the G54-G59 offsets can be set from the config system using the following conventions:
<pre>
$g54x=20.000
$g54y=20.000     etc, all the way through...
...
$g59b=1800
$g59c=1800
</pre> 

Or they can be set in a single command using using JSON mode. Only those axes specified are set. 
<pre>
{"g55"":{"x":20.000,"y":20.000,"z":"0.000","a":0.000}}
</pre> 

They can be displayed individually

`$g54x` - returns a single value 

...or as a group: 

`$g54` - returns all 6 values in the G54 group 

`$g92` - returns all 6 values of the origin offset group 

...or all together: 

`$o` - returns all offsets in the system (not available in JSON)

Note: the G54-G59 settings are persistent settings that are preserved between resets (i.e. in EEPROM), unlike the G92 origin offset settings which are just in the volatile Gcode model and are thus not preserved. <br>

## System Group Settings
These are general system-wide parameters and are part of the "sys" group.

### $FB - Firmware Build number
Read-only value. Can be queried.

### $FV - Firmware Version
Read-only value. Can be queried.

### $HV - Hardware Version
Read-write value. Set to 6 for version 6 or earlier board, Set to 7 for version 7 board. Used to configure switch and output ports which are somewhat different between revs. 

### $SI - Status Interval 
Interval in milliseconds between status reports when in motion. Set to 0 to disable automatic status reports. Minimum is about 200 ms.

### $SR - Status Report
Returns a status report in line form (use ? to return one in multi-line report firm)

### $JA - Junction Acceleration 
<pre>
$ja=50000   - 50,000 mm/min^2 - a reasonable value for a modest performance machine
$ja=200000  - 200,000 mm/min^2 - a reasonable value for a higher performance machine
</pre> 

### $ML- Minimum Line Segment 
Don't change this unless you are seriously tweaking TinyG for your application. It can cause many things to break.
<pre>
$ml=0.08    - Do not change this value
</pre> 

### $MA - Minimum Arc Segment 
Don't change this unless you are seriously tweaking TinyG for your application. It can cause many things to break.
<pre>$ma=0.10    - Do not change this value
</pre> 

### $MS - Minimum Segment time in microseconds - Refers to S-curve interpolation segments
Don't change this unless you are seriously tweaking TinyG for your application. It can cause many things to break.
<pre>
$ms=5000  - Do not change this value
</pre> 
<br> 

##Gcode Default Parameters
These parameters set the values for the Gcode model on power-up or reset. They do not affect the current gcode model state. For example, entering $gun=0 will not change the system to inches, but it will cause it to come up in inches during reset.

These are also part of the "sys" group.

### $GPL - Gcode Default Plane Selection
<pre>
$gpl=0      - G17 (XY plane)
$gpl=1      - G18 (XZ plane)
$gpl=2      - G19 (YZ plane)
</pre> 

###$GUN - Gcode Default Units
<pre>
$gun=0      - G20 (inches)
$gun=1      - G21 (millimeters)
</pre> 

###$GCO - Gcode Default Coordinate System
<pre>
$gco=0      - (absolute coordinate system)
$gco=1      - G54 (coordinate system 1)
$gco=2      - G55 (coordinate system 2)
$gco=3      - G56 (coordinate system 3)
$gco=4      - G57 (coordinate system 4)
$gco=5      - G58 (coordinate system 5)
$gco=6      - G59 (coordinate system 6)
</pre> 

###$GPA - Gcode Default Path Control
<pre>
$gpa=0      - G61 (exact stop mode)
$gpa=1      - G61.1 (exact path mode)
$gpa=2      - G64 (continuous mode)
</pre> 

### $GDI - Gcode Distance Mode
<pre>
$gdi=0      - G90 (absolute mode)
$gdi=1      - G91 (incremental mode)
</pre> 

## Communications Parameters
Set communications. These are also part of the "sys" group.

### $IC Ignore CR or LF on RX 
<pre>
$ic=0      - Don't ignore CR or LF in received data
$ic=1      - Ignore CR in received data
$ic=2      - Ignore LF in received data
</pre> 

### $EE - Enable Character Echo 
This should be disabled for JSON mode. In text mode it's optional either way.
<pre>
$ee=0      - Disable character echo
$ee=1      - Enable character echo
</pre> 

### $EX - Enable XON/XOFF protocol 
<pre>
$ex=0      - Disable XON/XOFF protocol 
$ex=1      - Enable XON/XOFF protocol 
</pre>

### $EJ - Enable JSON Mode on Power Up
This sets the startup mode. JSON mode can be invoked at any time by sending a line starting with an open curly '{'. JSON mode is exited any time by sending a line starting with '$', '?' or 'h'
<pre>
$ej=0      - Disable JSON mode on power-up and reset (e - Set Baud Ratenables text mode)
$ej=1      - Enable JSON mode on power-up and reset
</pre>

### $TV - Set Test mode verbosity
<pre>
$tv=0      - Silent - no response is provided
$tv=1      - Prompt - returns prompt only and exception messages
$tv=2      - Messages - returns prompt and all messages
$tv=3      - Verbose
</pre>

### $JV - Set JSON Echo verbosity
If you are using JSON mode with high-speed files (many short lines at high feed rates) you probably want setting 2 or 3. You may also want to change the baud rate to 230400.
<pre>
$jv=0      - Silent - no responses given to JSON commands
$jv=1      - Footer only - response contains no body - footer only
$jv=2      - Omit Gcode body - Body returned for configs; omitted for Gcode commands
$jv=3      - Gcode linenum only - Body returned for configs; Gcode returns line number as 'n', otherwise body is omitted
$jv=4      - Messages - body returned for configs; Gcode returns line numbers and messages only
$jv=5      - Full echo - Body returned for configs and Gcode - Gcode comments removed
</pre>

### $BAUD - Set USB Baud Rate
The default baud rate for the USB port is 115,200 baud. The following additional baud rates may be set. The sequence for changing the baud rate is: (1) Issue the $baud command, (2) wait for a response verifying the command, (3) change to the new baud rate.
<pre>
$baud=0     - Illegal baud rate setting. Returns an error
$baud=1     - 9600
$baud=2     - 19200
$baud=3     - 38400
$baud=4     - 57600
$baud=5     - 115200
$baud=6     - 230400
</pre>