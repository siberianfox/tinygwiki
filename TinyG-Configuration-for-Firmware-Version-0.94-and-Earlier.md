**The settings on this page are for firmware version 0.94 and later. See [TinyG Configuration for 0.95 and later](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration) if your firmware is 0.95 (or later) You can check your firmware version by looking for the "fv":0.950 or similar string in the startup message after a reset**

This page describes how configuration works in text mode from the [Command Line]. The settings on this page are for versions 0.94 and 0.93. Some of the command definitions have been changed from 0.92 and earlier. These are labeled as **[$xNN 0.92]** where this occurs. 

# Configuring 0.94 and Earlier
TinyG comes with a set of defaults pre-programmed to a specific machine profile. Currently this is set for a relatively slow screw machine such as the Zen Toolworks 7x12 by default. Other default profles are settable at compile time by including the right .h file.

But you will want to set up for your particular machine, as follows. Firmware configuration is done using the configuration editor through the text-mode command line. So connect to the USB port and issue "$" &lt;enter&gt; to test.<br>

## Background
Configuration is as simple as we could make it given the following features, assumptions, and constraints: 

* Supports 6 gcode axes (XYZABC) 
* Has 4 on-board motors and is designed so that multiple TinyG boards can be networked to drive more than 4 motors 
* Implements controlled jerk motion (and therefore needs to be configurable for it) 
* Treats each axis' contribution to the dynamics independently (i.e. dynamics are not set globaly for the machine) 
* Can be used for cartesian or non-cartesian kinematics - ie. you can't safely assume that motors and axes and axes are motors. 
* Supports 6 coordinate systems + absolute (machine) coordinates, and support G92 offsets.

TinyG configuration is organized into the following groups of related settings: 

* Motor groups: Settings specific to a given motor. There are 4 motor groups, numbered 1,2,3,4 as labeled on the TinyG board. Settings include: 
 * Motor mapping (to axis) 
 * Step angle 
 * Travel per motor revolution 
 * Microsteps 
 * Polarity 
 * Power management mode<br> 
* Axis groups: Settings specific to a given axis. There are 6 axis groups, one for each of X,Y,Z,A,B,C. Settings include: 
 * Axis mode 
 * Velocity maximum (aka traverse rate or seek) 
 * Feed rate maximum 
 * Travel maximum
 * Jerk maximum 
 * Junction deviation (for cornering control) 
 * Radius setting (rotational axes only) 
 * Switch mode 
 * Search velocity (homing speed on initial approach) 
 * Latch velocity (homing speed on second approach) 
 * Zero offset (offset from switch for zero in absolute coordinate system)<br> 

System group: The system group contains global machine and communication settings, including: 
* Versions
 * Firmware version (read-only) 
 * Firmware build (read-only) 
* Gcode defaults - These are the initial values that machine will power up with or revert to for a Program End (M2 or M30), reset or abort. Setting these does NOT change the current machine mode, only the initial mode. 
 * Default units - mm or inches 
 * Default plane selection 
 * Default coordinate system
 * Default path control mode 
 * Default distance mode 
* Machine and software global parameters 
 * Acceleration planning enabled / disabled 
 * Junction acceleration (global cornering value) 
 * Minimum line length 
 * Arc segment length 
 * Segment timing (interpolation interval)<br> 
* Communications settings configure the serial port behaviors<br>
 * Ignore CR for RX chars 
 * Ignore LF for RX chars 
 * Append CR to TX chars
 * Enable / disable command line echo
 * Enable / disable XON/XOFF flow control protocol<br>
* Status Report Settings 
 * Status report interval (disable = 0)
 * Status report parameters (Settable in JSON only - see JSON mode for details)

## Displaying Settings
The following commands will display settings groups. 
<pre>
$s    Show system settings
$1    Show motor 1 settings (or whatever motor you want 1,2,3,4)
$x    Show X axis settings (or whatever axis you want x,y,z,a,b,c)
$m    Show all motor settings
$n    Show all axis settings
$$    Show all settings
$h    Show this help screen
</pre> 
The $ must be the first character of the line, and input is case insensitive. Configuration is non-moded; that is, configuration lines and Gcode blocks can be freely intermixed without changing modes <br><br> **CAVEAT: At the current time because of various limitations of the Xmega errata we recommend pausing transmission for at least 30 milliseconds after each line containing a $ command. This gives the system enough time to persist the data to EEPROM, during which time the system cannot receive new serial input. We are working on a workaround to this issue.**

## Updating Settings
To update a setting enter a token and a value. Most tokens are a 2 or 3 letter mnemonic plus a motor number or axis prefix. System settings have no prefix and may be 2 to 4 letters. The following are examples of valid inputs. The setting is taken and the value is echoed on the next line 

<pre>
tinyg[mm]ok> $yfr=800       Set feed rate for Y axis to 800 mm/min
 Y axis - Feed rate           800 mm/min       $YFR800
</pre>
<pre>
tinyg[mm]ok> $yfr = 1,600    Set feed rate for Y axix to 1600 mm/min
 Y axis - Feed rate           1600 mm/min       $YFR1600
</pre>
<pre>
tinyg[mm]ok> $2po=1          Set polarity for motor 2 to inverted
 Motor 2 - Motor polarity        1 [0,1]        $2PO1
</pre>
<pre>
tinyg[mm]ok> $ja=100000      Set junction acceleration global value to 100,000
 Junction corner accel      100000 mm/min^2     $JA100000
</pre>
<pre>
If there is an error there will be no echo or a line like one of these: 
 #### Unknown config string: YGR800                             It didn't recognize the mnemonic
 {21} Bad number format: XFR1200.2.3                            The value had 2 decimal points
</pre>

## Units
TinyG operates in either inches or millimeters mode depending on the prevailing setting of G20 (inches) or G21 (mm). All values will be input and displayed in the units system selected. Most of the examples below are in mm, but could just as easily be input in inches.

# Settings Details
Note: the settings are case insensitive - they are shown in upper case for emphasis only.

## Motor Settings
**$1MA** MAp motor to axis. Axes must be input as numbers, with X=0, Y=1, Z=2, A=3, B=4 and C=5. As you might expect, mapping motor 1 to X will cause X movement to drive motor 1.The example below is a way to run a dual-Y gantry such as the LumenLabs micRo v3. Movement in Y will drive both motor2 and motor3. The mapping illustrated causes movement in X and Z to drive motors 1 and 4, respectively. 

<pre>
 $1ma=0	<span class="Apple-tab-span" style="white-space:pre">	</span>Maps motor 1 to the X axis
 $2ma=1	<span class="Apple-tab-span" style="white-space:pre">	</span>Maps motor 2 to the Y axis
 $3ma=1	<span class="Apple-tab-span" style="white-space:pre">	</span>Maps motor 3 to the Y axis
 $4ma=2	<span class="Apple-tab-span" style="white-space:pre">	</span>Maps motor 4 to the Z axis
</pre>

**$1SA** Step Angle for the motor. This is often 1.8 degrees per step, but should reflect the motors in use.&nbsp;You might also find 0.9, 3.6, 7.5 or other values. 
<pre>
 $1sa=1.8	This is a typical value for most motors. 
</pre>

**$1TR** This is the amount of travel per motor revolution in mm or inches for X, Y or Z axes, or in degrees for A, B and C axes. The XYZ value will be interpreted and echoed in the prevailing units; G20 sets inches, G21 sets mm. ABC values are always in degrees. 

For XYZ this value is usually the result of the lead screw pitch or pulley circumference. A 10 TPI leadscrew moves 0.100" / revolution. A 0.500" pulley will travel 3.14159" per revolution, absent any other gearing. A typical value for a Shapeoko of Reprap belt driven machine is on the order of 35 mm per revolution. Don't take this as exact - you will need to calibrate your machine to get this setting.

For ABC the travel is entered in degrees. This value will be 360 degrees for an axis that is not geared down. The value for a geared rotary axis is 360 divided by the gear ratio. For example, a motor-driven rotary table with a 4 degree table movement per handle rotation has a gear ratio of 90:1. The Travel per Revolution value should be set to 4. 

Note that Travel per Revolution is a motor parameter, not an axis parameter as one might think. Consider the case of a dual Y gantry with lead screws of different pitch (how weird). The travel per revolution would be different for each motor. 

<pre>
 $1tr=2.54      Sets motor 1 to a 10 TPI travel from millimeters (2.54 mm per revolution)
</pre>

**$1MI** MIcrosteps. The following values are supported: 
* 1 = no microsteps (whole steps)
* 2 = half stepping
* 4 = quarter stepping
* 8 = eighth stepping

Despite common wisdom, higher microstep values are not always better (It's like watts in the 80's - the more the better, right?). In a typical setup the total power delivered to the motor (and hence torque) will go down as you increase the microsteps. Also, using microsteps to set the finest machine resolution is source of error as the shaft angle isn't necessarily going to be at the theoretical point. Don't just assume that 1/8 microstepping is the right setting for your application. Try out different settings to balance smoothness and power.
 
<pre>
 $3mi=8	<span class="Apple-tab-span" style="white-space:pre">	</span>Set 1/8 microsteps for motor 3 
</pre>

_Note: Values other than 1,2,4 and 8 are accepted. This is to support some people that have crazily wired TInyG to other drivers (you know who you are) like those running 10x or 16x steps. If you are using the drivers on TInyG this will cause them to malfucntion, so please don't do this unless you are one of those hacker types that soldered up your TinyG._ 

**$1PO** Set to one of the following: 0 = Normal motor polarity, 1 = Invert motor polarity. Polarity sets which direction the motor will turn when presented with positive and negative Gcode coordinates. It's affected by how you wired the motors and by mechanical factors. Set polarity so the indicated axis travels in the correct orientation for your machine. Travel in X and Y is dependent on the conventions for your particular machine and CAD setup. Typically X is left/right movement, and Y is towards and away from you, but people often set up the machine to agree with the visualization their CAD program provides, and can depend on where you stand when operating the machine. Typically X+ moves to the right, X- to the left, Y+ away from you, and Y- towards you.&nbsp;Z is by convention the cutting axis, which is the vertical axis on a typical milling machine. Z+ should move up, and Z- should move down, into the work.<br> 

<pre>
 $3po=0<span class="Apple-tab-span" style="white-space:pre">		</span>Set polarity to normal
</pre>

**$1PM** Power Management mode **[$1PW 0.92]**. Set to one of the following: 0 = Leave motor powered on when stopped, 1 = Turn motor power off when stopped.&nbsp;Stepper motors actually consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Some machine configurations are OK if you shut off the power on idle (like most leadscrew machines), others are not (some belt/pulley configs and some non-cartesian robots)<br> 
<pre>
 $4pm=1         Set low-power idle for motor 4
</pre>

## Axis Settings
**$xAM** Axis Mode. **[was $xMO in version 0.92]** Sets the function of the axis. The following modes are supported for all axes.<br> 

* 0 = Disable. All input to that axis will be ignored and the axis will not move. 
* 1 = Standard. Linear axes move in length units. Rotary axes move in degrees. 
* 2 = Inhibited. Axis values are taken into account when planning moves, but the axis will not move. Use this to perform a Z kill.

<pre>
 $zmo=2 	will inhibit the Z axis; $zmo1 will restore standard operation
</pre>

Rotary axes can have these additional modes. 

* 3 = Radius mode. In radius mode gcode values are interpreted as linear units; either inches or mm depending on the prevailing G20/G21 setting. The conversion of linear units to degrees is accomplished using the radius setting for that axis. See $aRA for details. 
* 4 = Slave X mode - rotary axis slaved to movement in X dimension 
* 5 = Slave Y mode - rotary axis slaved to movement in Y dimension 
* 6 = Slave Z mode - rotary axis slaved to movement in Z dimension 
* 7 = Slave XY mode - rotary axis slaved to movement in XY plane 
* 8 = Slave XZ mode - rotary axis slaved to movement in XZ plane 
* 9 = Slave YZ mode - rotary axis slaved to movement in YZ plane 
* 10 = Slave XYZ mode - rotary axis slaved to movement in XYZ space

Slave modes use the distance traveled in one or more linear dimensions as the linear input to the rotational axis. The linear travel is converted to degrees using the radius value set by $aRA. Any value that is provided in the Gcode block for the axis is ignored.<br> 

**$xVM** Velocity Maximum (also known as traverse rate or seek rate) **[$xSR 0.92]**. Sets the maximum velocity the axis will move during a traverse (G0). This is set in length units per minute for linear axes, degrees per minute for rotary axes. The max velocity will be used for all G0's from the time of reset onward. Note that the max velocity is *per-axis*. Diagonal / multi-axis traverses will actually occur at the fastest speed the combined set of axes and the geometry will allow, and may be faster than the individual axis max velocities. For example, max velocity for X and Y are set to 1000 mm/min. For a 45 degree traverse in X and Y the toolhead would travel at 1414.21 mm/min. 
<pre>
$xvm=1200 sets X maximum velocity (G0) to 1200 mm/min - assuming G21 is active (i.e. the machine is in MM mode)
$zvm=30.0 sets Z to 30 inches per minute - assuming G20 is active
$avm=3600 sets A to 10 revolutions per minute (360 * 10)
</pre>

**$xFR** Maximum Feed Rate. Sets the maximum velocity the axis can move during a feed (G1, G2, G3). Units work similarly to traverse rate (maximum velocity). Axis feed rates should be equal to or less than the maximum velocity. See Setting Feed Rate and Maximum Velocity for more details. Note: The feed rate setting is NOT used to set the Gcode's F value; it is only a maximum. A reset machine will have a zero feed rate for safety reasons.

<pre>
$xfr=1000 sets X max feed rate to 1000 mm/min - assuming G21 is active (i.e. the machine is in MM mode)
</pre>

**$xTM** Travel Maximum. **[$xTS 0.92]** Defines the maximum extent of travel in that axis. This is currently only used during homing but will also be used for soft limits when that feature is implemented.

**$xJM** Jerk Maximum. Sets the maximum jerk value for that axis. Jerk is settable independently for each axis to support machines with different dynamics per axis - such as Shapeoko with belts for X and Y, screws for Z, or Probotix with 5 pitch X and Y screws and 12 pitch Z screws. The planner calculates the resultant jerk for the move given the contributions of each axis to the move. Jerk is in units per minutes^3, so the numbers are quite large.&nbsp;Some common values are shown in *millimeters* in the examples below 

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

**$xJD** Junction Deviation. **[$xCD 0.92]** This one is somewhat complicated. Junction deviation - in combination with Junction Acceleration ($JA) from the system group - sets the velocity reduction used during cornering through the junction of two lines. The reduction is based on controlling the centripetal acceleration through the junction to the value set in JA with the&nbsp;junction deviation being the "tightness" of the controlling cornering circle. An explanation of what's happening here can be found on [Sonny Jeon's blog](http://onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/ onehossshay.wordpress.com/2011/09/24/improving_grbl_cornering_algorithm/). It's important to realize that the tool head does not actually follow the controlling circle - the circle is just used to set the speed of the tool through the defined path. In other words, the tool does go through the sharp corner, just not as fast. This is a Gcode G61 - Exact Path Mode operation, not a Gcode G64 - Continuous Path Mode (aka corner rounding, or splining) operation. 

JA is set globally and applies to all axes. JD is set per axis and can vary depending on the characteristics of the axis. An axis that moves more slowly should have a JD that is less than an axis that can move more quickly, as the larger the JD the faster the machine will move through the junction (i.e.&nbsp;a bigger controlling circle). The following example has some representative values for a Probotix Fireball V90 machine. The V90 has 5 TPI X and Y screws, and 12 TPI Z. All values in MM. 

<pre>
 $xJD 0.05<span class="Apple-tab-span" style="white-space:pre">	</span>Units are mm
 $yJD 0.05
 $zJD 0.02<span class="Apple-tab-span" style="white-space:pre">	</span>Setting Z to a smaller value means that moves with a change in the Z component will move proportionately slower depending on the contribution in Z. 
 $JA 200000<span class="Apple-tab-span" style="white-space:pre">	</span>Units are mm/min^2. As before, commas are ingored and are provided only for clarity
</pre>

**$aRA** **[$x_radius value]** Radius value. The radius value is used by a rotational axis to convert linear units to degrees when in radius mode or in a slaved mode. For example; if the A radius is set to 10 mm it means that a value of 6.28318531&nbsp;mm will make the A axis travel one full revolution - as 62.383... is the circumference of the circle of radius R ( 2*PI*R, or 10 * 2 * 3.14159...) (Assuming $nTR = 360 -- see note below). Receiving the gcode block "G0 A62.83" will turn the A axis one full revolution (360 degrees) from a starting position of 0. All internal computations and settings are still in degrees - it's just that gcode units received for the axis are converted to degrees using the specified radius. 

Note that the Travel per Revolution value ($1TR) is used but unaffected in radius mode. The degrees per revolution still applies, it's just that the degrees were computed based on the radius and the Gcode axis values. See Travel per Revolution (See $1TR) in the motor group. 

<pre>
$aRA=1.00   Setting the radius to 1" means that the Gcode block G0 A6.283 (inches) will make the A axis perform one revolution (1" * 2 * pi).
</pre>

**$xSM**  Switch Mode **[$xLI 0.92]** Sets mode for limit and homing switches. Currently, the following modes are supported:

* 0 = switches are disabled. No actions will occur for either homing or limit operations 
* 1 = Normally Open (NO) switches enabled for homing only. No action will occur for limits 
* 2 = Normally Open (NO) switches enabled for homing and limits

### Per Axis Homing Settings
See also $xTM and $xSM, and [TinyG Homing in Version 0.94 and Earlier](https://github.com/synthetos/TinyG/wiki/TinyG-Homing-(version-0.94-and-earlier))

**$xSV** Homing Search Velocity - velocity for initially finding the homing switch. Set negative for travel in negative direction, positive for travel in positive direction.

**$xLV** Homing Latch Velocity - velocity for homing second pass (latching phase). Sign is ignored. latch will aways move in the same direction as search. 

**$xLB** Homing Latch Backoff -amount to back off switch prior to latch operation 

**$xZB** Homing Zero Backoff - machine coordinate system zero position defined as backoff (offset) from homing switch

## Coordinate System and Origin Offsets
Coordinate system offsets are the values used by G54, G55, G56, G57, G58 and G59 to define the offsets from the machine (absolute) coordinate system for X,Y,Z,A,B and C. G54-G59 correspond to coordinate systems 1-6, respectively. These can be set from Gcode using the G10 command (e.g. G10 P2 L2 X20.000 - the P word is the coordinate system, the L word is accoding to standard, but is ignored). 

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
They can be displayed individually:

`$g54x` - returns a single value 

...or as a group: 

`$g54` - returns all 6 values in the G54 group 

`$g92` - returns all 6 values of the origin offset group 

...or all together: 

`$o` or 

`$ofs` - returns all 6 groups of 6 values, plus the G92 origin offsets 

Note: the G54-G59 settings are persistent settings that are preserved between resets (i.e. in EEPROM), unlike the G92 origin offset settings which are just in the volatile Gcode model and are thus not preserved. <br>

## System Settings

**$FV** Firmware Version: Read-only value. Can be queried. 

**$FB** Firmware Build number: Read-only value. Can be queried. 

**$SI ** Status Interval in milliseconds. Set to 0 to disable automatic status reports. Minimum is about 100 ms.

**$SR** Status Report. Returns a starus report in line form (use ? to return one in multi-line report firm)

### Gcode Default Parameters
These parameters set the values for the Gcode model on power-up or reset. They do not affect the current gcode model state. For example, entering $gun=0 will not change the system to inches, but it will cause it to come up in inches during reset.

**$GPL**  Gcode Default Plane Selection for reset and power-on. 
<pre>
$gpl=0      - G17 (XY plane)
$gpl=1      - G18 (XZ plane)
$gpl=2      - G19 (YZ plane)
</pre> 

**$GUN** Gcode Default Units for reset and power-on. 
<pre>
$gun=0      - G20 (inches)
$gun=1      - G21 (millimeters)
</pre> 

**$GCO** Gcode Default Coordinate System for reset and power-on. 
<pre>
$gco=0      - (absolute coordinate system)
$gco=1      - G54 (coordinate system 1)
$gco=2      - G55 (coordinate system 2)
$gco=3      - G56 (coordinate system 3)
$gco=4      - G57 (coordinate system 4)
$gco=5      - G58 (coordinate system 5)
$gco=6      - G59 (coordinate system 6)
</pre> 

**$GPA** Gcode Default Path Control for reset and power-on 
<pre>
$gpa=0      - G61 (exact stop mode)
$gpa=1      - G61.1 (exact path mode)
$gpa=2      - G64 (continuous mode)
</pre> 

**$GDI** Gcode Distance Mode for reset and power-on 
<pre>
$gdi=0      - G90 (absolute mode)
$gdi=1      - G91 (incremental mode)
</pre> 

###Motion Parameters
**$EA** Enable Acceleration (NOTE: as of 0.93 this setting is disabled. Acceleration is always enabled)
<pre>
$ea=0      - Disable acceleration
$ea=1      - Enable acceleration
</pre> 

**$JA** Junction Acceleration 
<pre>
$ja=50000   - 50,000 mm/min^2 - a reasonable value for a modest performance machine
$ja=200000  - 200,000 mm/min^2 - a reasonable value for a higher performance machine
</pre> 

**$ML** Minimum Line Segment 
<pre>
$ml=0.08    - Do not change this value
</pre> 
**$MA** Minimum Arc Segment 
<pre>
$ma=0.10    - Do not change this value
</pre> 
**$MS** Minimum Segment time in microseconds - Refers to S-curve interpolation segments
<pre>
$ms=5000  - Do not change this value
</pre> 
<br> 

###Communications Parameters
**$IC** Ignore CR on RX. 
<pre>
$ic=0      - Disable
$ic=1      - Enable
</pre> 

**$IL** Ignore LF on RX. 
<pre>
$il=0      - Disable
$il=1      - Enable
</pre> 

**$EC** Enable CR expansion on TX. 
<pre>
$ec=0      - Disable
$ec=1      - Enable
</pre> 

**$EE** Enable Echo. 
<pre>
$ee=0      - Disable
$ee=1      - Enable
</pre> 

**$EX** Enable XON/XOFF protocol 
<pre>
$ex=0      - Disable
$ex=1      - Enable
</pre>

