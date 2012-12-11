= Configuring TinyG  =

[[Projects:TinyG|[Back to TinyG main page]]]

[[Projects:TinyG|TinyG]] comes with a set of defaults pre-programmed to a specific machine profile. Currently this is set for a relatively slow screw machine such as the Zen Toolworks 7x12 by default. Other default profles are settable at compile time by including the right .h file.

But you will want to set up for your particular machine, as follows.<br> Firmware configuration is done using the configuration editor through the command terminal. So connect to the TG terminal and issue "$" &lt;enter&gt; to test. <br> TinyG also supports [[Projects:TinyG-JSON|JSON formatted commands]] for configuration and other operations. Please read that section for JSON operation. 

REVISION NOTICE: The settings on this page are for version 0.93 and beyond. Some of the command definitions have been changed from 0.92 and earlier. These are labeled as '''[$xNN 0.92]''' where this occurs. 

== Background  ==

Configuration is as simple as we could make it given the following features, assumptions, and constraints: 

*Supports 6 gcode axes (XYZABC) 
*Has 4 on-board motors and is designed so that multiple TinyG boards can be networked to drive more than 4 motors 
*Implements controlled jerk motion (and therefore needs to be configurable for it) 
*Treats each axis' contribution to the dynamics independently (i.e. dynamics are not set globaly for the machine) 
*Can be used for cartesian or non-cartesian kinematics - ie. you can't safely assume that motors and axes and axes are motors. 
*Supports 6 coordinate systems + absolute (machine) coordinates, and support G92 offsets.

TinyG configuration is organized into the following groups of related settings: 

*Motor groups: Settings specific to a given motor. There are 4 motor groups, numbered 1,2,3,4 as labeled on the TinyG board. Settings include: 
**Motor mapping (to axis) 
**Step angle 
**Travel per motor revolution 
**Microsteps 
**Polarity 
**Power management mode<br> 
*Axis groups: Settings specific to a given axis. There are 6 axis groups, one for each of X,Y,Z,A,B,C. Settings include: 
**Axis mode 
**Velocity maximum (aka traverse rate or seek) 
**Feed rate maximum 
**Travel maximum
**Jerk maximum 
**Junction deviation (for cornering control) 
**Radius setting (rotational axes only) 
**Switch mode 
**Search velocity (homing speed on initial approach) 
**Latch velocity (homing speed on second approach) 
**Zero offset (offset from switch for zero in absolute coordinate system)<br> 
*System group: The system group contains global machine and communication settings, including: 
**Version control 
***Firmware version (read-only) 
***Firmware build (read-only) 
**Gcode defaults - These are the initial values that machine will power up with or revert to for a Program End (M2 or M30), reset or abort. Setting these does NOT change the current machine mode, only the initial mode. 
***Default units - mm or inches 
***Default plane selection 
***Default coordinate system
***Default path control mode 
***Default distance mode 
**Machine and software global parameters 
***Acceleration planning enabled / disabled 
***Junction acceleration (global cornering value) 
***Minimum line length 
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
