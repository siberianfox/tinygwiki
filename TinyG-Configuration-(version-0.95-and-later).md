REVISION NOTICE: The settings on this page are for version 0.95 and later.

TinyG comes with a set of defaults pre-programmed to a specific machine profile. The default profile is set for a relatively slow screw machine such as the Zen Toolworks 7x12. Other default profles are settable at compile time by including the right .h file.

But you will want to set up for your particular machine, as follows.

Firmware configuration is done by sending configuration commands while in text mode (USB command line), or JSON equivalents if in JSON mode. Connect to the TG terminal and issue "$" to test. 

## Background
Configuration is as simple as we could make it given the following features, assumptions, and constraints: 

* Supports 6 gcode axes (XYZABC) 
* Has 4 on-board motors and is designed so that multiple TinyG boards can be networked to drive more than 4 motors 
* Implements controlled jerk motion (and therefore needs to be configurable for it) 
* Treats each axis' contribution to the dynamics independently (i.e. dynamics are not set globaly for the machine) 
* Can be used for cartesian or non-cartesian kinematics - ie. you can't safely assume that motors and axes and axes are motors. 
* Supports 6 coordinate systems + absolute (machine) coordinates, and support G92 offsets.


## Summary
TinyG configuration is organized into the following groups of related settings: 

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
The system group contains global machine and communication settings> These are not prefixed by and axis or motor prefix

	Setting | Description | Notes
	--------|-------------|-------
	$fv | Firmware version | Read-only value, e.g. 0.95
	$fb | Firmware build | Read-only value, e.g. 351.05 
	| Gcode defaults | These are the initial values that machine will power up with or revert to for a Program End (M2 or M30), reset or abort. Setting these does NOT change the current machine mode, only the initial mode
	$gun | Default units mode | 0=inches 1=mm 
	$gpl | Default plane selection | 
	$gco | Default coordinate system |
	$gpa | Default path control mode |
	$gdi | Default distance mode | 
	| Global parameters | 
	$ja | Junction acceleration | Global cornering acceleration value 
	$ml | Minimum line length | 
***Arc segment length 
***Segment timing (interpolation interval)<br> 
**Communications settings configure the serial port behaviors<br>
***Ignore CR for RX chars 
***Ignore LF for RX chars 
***Append CR to TX chars
***Enable / disable command line echo
***Enable / disable XON/XOFF flow control protocol<br>
**Status Report Settings 
***Status report interval (disable = 0)
***Status report parameters (Settable in JSON only - see JSON mode for details)

== Displaying Settings  ==

The following commands will display settings groups. 
<pre>$s    Show system settings
$1    Show motor 1 settings (or whatever motor you want 1,2,3,4)
$x    Show X axis settings (or whatever axis you want x,y,z,a,b,c)
$m    Show all motor settings
$n    Show all axis settings
$$    Show all settings
$h    Show this help screen
</pre> 
The $ must be the first character of the line, and input is case insensitive. Configuration is non-moded; that is, configuration lines and Gcode blocks can be freely intermixed without changing modes <br><br> ''CAVEAT: At the current time because of various limitations of the Xmega errata we recommend pausing transmission for at least 30 milliseconds after each line containing a $ command. This gives the system enough time to persist the data to EEPROM, during which time the system cannot receive new serial input. We are working on a workaround to this issue.''

== Updating Settings  ==

To update a setting enter a token and a value. Most tokens are a 2 or 3 letter mnemonic plus a motor number or axis prefix. System settings have no prefix and may be 2 to 4 letters. The following are examples of valid inputs. The setting is taken and the value is echoed on the next line 

 tinyg[mm]ok&gt; $yfr=800 <span class="Apple-tab-span" style="white-space:pre">						</span>(Set feed rate for Y axix to 800 mm/min)
 Y axis - Feed rate            800 mm/min       $YFR800
 
 tinyg[mm]ok&gt; $ y fr = 1,600 					(Set feed rate for Y axix to 1600 mm/min)
 Y axis - Feed rate           1600 mm/min       $YFR1600
 
 tinyg[mm] ok&gt; $2po=1<span class="Apple-tab-span" style="white-space:pre">						</span>(Set polarity for motor 2 to inverted)
 Motor 2 - Motor polarity        1 [0,1]        $2PO1
 
 tinyg[mm] ok&gt; $ja=100000<span class="Apple-tab-span" style="white-space:pre">					</span>(Set junction acceleration global value to 100,000)
 Junction corner accel      100000 mm/min^2     $JA100000
 

If there is an error there will be no echo or a line like one of these: 

 #### Unknown config string: YGR800<span class="Apple-tab-span" style="white-space:pre">		</span>It didn't recognize the mnemonic
 {21} Bad number format: XFR1200.2.3<span class="Apple-tab-span" style="white-space:pre">		</span>The value had 2 decimal points

Input is somewhat forgiving of caps, extra characters and spaces. The following are all valid ways to set the step angle for motor 2 to 0.9 degrees per step 

 tinyg[mm]ok&gt;  $2sa 0.9
 tinyg[mm]ok&gt;  $2 sa 0.900
 tinyg[mm]ok&gt;  $2SA=0.9
 tinyg[mm]ok&gt;  $2sa=+0.9
 tinyg[mm]ok&gt;  $2SA=.9

<br>

== Units  ==
TinyG operates in either inches or millimeters mode depending on the prevailing setting of G20 (inches) or G21 (mm). All values will be input and displayed in the units system selected. Most of the examples below are in mm, but could just as easily be input in inches.<br><br>
