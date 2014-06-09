This page provides and example setup for a reasonably well tuned 3 axis or 4 axis Shapeoko with 375mm slides. Depending on your mechanics you may need to change values, but this should offer a reasonable starting point. Please see the [TinyG Tuning Page](https://github.com/synthetos/TinyG/wiki/TinyG-Tuning) for some details about tuning up the machine.

The settings on this page are listed for TinyG build 412.01 and higher. If you have build 380.08 (or lower) these settings are listed when there are differences. You can tell what build you are on by typing $fb at the command prompt, or looking at the build number display in tgfx. Better yet, update to 412.01.

Settings use the following format by way of example:

* $jd=0.01<br>
* $xvm=16000<br>

All settings are in mm, so be sure you are in mm mode. If in doubt enter  G21 at the command line.

Settings can also be entered in JSON, which is a better way to do it for machine-to-machine communications:

* {"jd":0.01}<br>
* {"xvm":16000}<br>

Additionally, using JSON all the settings for a motor or an axis can be done in one command. See [JSON Operation](https://github.com/synthetos/TinyG/wiki/JSON-Operation)

For details of the settings see the [TinyG Configuration Page](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)

The general settings for a 3 motor machine are listed below. For dual Y axis setups use the general settings, but also see [Dual Y Motor settings](https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup#dual-y-motor-settings)

## System Settings
	setting | value | description |notes
	---------|---------|---------|-------
	$ja | 2000000 | JUNCTION_ACCELERATION | 2 million - centripetal acceleration around corners
	$st | 1 | SWITCH_TYPE | 0=normally open, 1=normally closed. Use NC for better noise immunity 
	$mt | 10 | MOTOR_TIMEOUT | In build 412.01 and above this sets the number of seconds motors will stay energized after a machining cycle is complete. (this setting is not in 380.08)

## Motor Settings
Use the following settings for a 3 motor system - and assumes:
* X axis is wired to Motor 1
* Y axis is wired to Motor 2
* Z axis is wired to Motor 3

See [Dual Y Settings](https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup#dual-y-motor-settings) for dual Y gantry setups.

_A note on polarity:_ The motor polarity is the most likely setting to need adjustment, as it's dependent on how the motors are wired. Set polarity so that X moves to the right for positive moves. Y should move away from the front of the table for positive moves. Z should move up for positive moves. Reverse polarity of the axis if this is not true. Or you can swap the two leads of one of the coil pairs on your motor wiring to do this in hardware.

###Motor 1
	setting | value | description |notes
	---------|---------|---------|-------
	$1ma | 0 | MOTOR_MAP | map motor 1 to X axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$1sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$1tr | 36.54 | TRAVEL_PER_REV | Amount X moves in 1 motor revolution. Your setup may be slightly different.
	$1mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$1po | 0 | POLARITY | Depends on how you wired your motors 
	$1pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves 

###Motor 2
	setting | value | description |notes
	---------|---------|---------|-------
	$2ma | 1 | MOTOR_MAP | map motor 2 to Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$2sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$2tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 revolution
	$2mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$2po | 0 | POLARITY | Depends on how you wired your motors 
	$2pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

###Motor 3
	setting | value | description |notes
	---------|---------|---------|-------
	$3ma | 2 | MOTOR_MAP | map motor 3 to Z axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$3sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$3tr | 1.25 | TRAVEL_PER_REV | Amount Z moves in 1 revolution
	$3mi | 4 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$3po | 0 | POLARITY | Depends on how you wired your motors 
	$3pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

###Motor 4
In this config motor 4 does not need to be set up. The following settings are for an A axis, and are provided just as an example.

	setting | value | description |notes
	---------|---------|---------|-------
	$4ma | 3 | MOTOR_MAP | map motor 4 to second A axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$4sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$4tr | 360 | TRAVEL_PER_REV | Amount A moves in degrees for 1 motor revolution. Reduce if A is geared.
	$4mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$4po | 0 | POLARITY | Reverse from Motor 2 so it doesn't fight motor 2's Y. 
	$4pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

## Axis Settings
The axis settings below assume the following:

* The Z axis can travel relatively fast because it is relatively friction-free. If there is too much friction lower the velocity and jerk values to avoid stalling.
* The machine is set up for homing switches but not limits. Homing switches are on Xmin, Ymin, and Zmax. If there are not homing switches set the switch values to disabled.

Axis settings apply to both 3 motor and 4 motor configurations. Only the motor parameters and mappings need to change.

### X Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$xam | 1 | AXIS_MODE | 1=standard mode
	$xvm | 16000 | VELOCITY_MAX | Your machine might go faster or slower than this. Test it and adjust current pots
	$xfr | 16000 | FEEDRATE_MAX | Typcially set the same as velocity. May be set slower but not faster.
	$xtn | 0 | TRAVEL_MIN | Minimum travel (almost aways zero) (not in 380.08)
	$xtm | 220 | TRAVEL_MAX | Max travel before crash
	$xjm | 5000 | JERK_MAX | Jerk X 1 million. (see note for 380.08 or earlier)
	$xjh | 10000 | JERK_HOMING | Jerk X 1 million to use for homing. Typically > JERK_MAX (see 380.08 note)
	$xjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$xsn | 1 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 1 is HOMING
	$xsx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is DISABLED
	$xsv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$xlv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$xlb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$xzb | 3 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position

Note: Build 380.08 requires the full number for 5 billion: 5000000000.  No spaces or commas. Count the zeros

### Y Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$yam | 1 | AXIS_MODE | 1=standard mode
	$yvm | 16000 | VELOCITY_MAX | Your machine might go faster or slower than this
	$yfr | 16000 | FEEDRATE_MAX | Typcially set the same as velocity. May be set slower but not faster.
	$ytn | 0 | TRAVEL_MIN | Minimum travel (almost aways zero) (not in 380.08)
	$ytm | 220 | TRAVEL_MAX | Max travel before crash
	$yjm | 5000 | JERK_MAX | Jerk X 1 million.
	$yjh | 10000 | JERK_HOMING | Jerk X 1 million to use for homing. Typically > JERK_MAX
	$yjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$ysn | 1 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 1 is HOMING
	$ysx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is DISABLED
	$ysv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$ylv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$ylb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$yzb | 3 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position

### Z Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$zam | 1 | AXIS_MODE | 1=standard mode
	$zvm | 800 | VELOCITY_MAX | Heavily dependent on how clean you can get the Z axis setup. Might go faster.
	$zfr | 800 | FEEDRATE_MAX | Typically set the same as velocity. May be set slower but not faster.
	$ztn | 0 | TRAVEL_MIN | Typically zero but may be negative (e.g. set travel max to zero)
	$ztm | 100 | TRAVEL_MAX | Max travel before crash
	$zjm | 50 | JERK_MAX | 50 million
	$zjh | 1000 | JERK_HOMING | 1 billion. Jerk to use during homing operations
	$zjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$zsn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is DISABLED
	$zsx | 1 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 1 is HOMING
	$zsv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$zlv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$zlb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$zzb | 3 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position

### A, B and C Axes
Usually you are not using these, so just set some reasonable values like below. All distances are in degrees. 

	setting | value | description |notes
	---------|---------|---------|-------
	$aam | 0 | AXIS_MODE | 1=standard mode
	$avm | 60000 | VELOCITY_MAX | 
	$afr | 60000 | FEEDRATE_MAX | 
	$atn | -1 | TRAVEL_MIN | -1 
	$atm | -1 | TRAVEL_MAX | -1 ... if min and max are the same it's treated as an infinite axis
	$ajm | 24000 | JERK_MAX | 24 billion
	$ajh | 24000 | JERK_HOMING | Jerk to use during homing operations
	$ajd | 0.1 | JUNCTION_DEVIATION |
	$ara | 1 | RADIUS |
	$asn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is disabled
	$asx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is disabled
	$asv | 6000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$alv | 1000 | LATCH_VELOCITY | Speed to back off homing switch
	$alb | 5 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$azb | 2 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position

# Dual Y Motor Settings
For a dual Y setup only the motor settings need to change. Axis settings are the same.

###Motor 1
	setting | value | description |notes
	---------|---------|---------|-------
	$1ma | 0 | MOTOR_MAP | map motor 1 to X axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$1sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$1tr | 36.54 | TRAVEL_PER_REV | Amount X moves in 1 motor revolution. Your setup may be slightly different.
	$1mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$1po | 0 | POLARITY | Depends on how you wired your motors 
	$1pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

###Motor 2
	setting | value | description |notes
	---------|---------|---------|-------
	$2ma | 1 | MOTOR_MAP | map motor 2 to first Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$2sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$2tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 revolution
	$2mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$2po | 0 | POLARITY | Depends on how you wired your motors 
	$2pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

###Motor 3
	setting | value | description |notes
	---------|---------|---------|-------
	$3ma | 1 | MOTOR_MAP | map motor 3 to second Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$3sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$3tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 motor revolution
	$3mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$3po | 1 | POLARITY | Reverse from Motor 2 so it doesn't fight motor 2's Y. 
	$3pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

###Motor 4
	setting | value | description |notes
	---------|---------|---------|-------
	$4ma | 2 | MOTOR_MAP | map motor 4 to Z axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$4sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$4tr | 1.25 | TRAVEL_PER_REV | Amount Z moves in 1 revolution
	$4mi | 4 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$4po | 0 | POLARITY | Depends on how you wired your motors 
	$4pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves
