This page provides and example setup for a reasonably well tuned 3 axis or 4 axis Shapeoko with 375mm slides. Depending on your mechanics you may need to change values, but this should offer a reasonable starting point. Please see the TinyG Tuning Page for some details about tuning up the machine.

Settings use the following format by way of example:

  $jd=0.01<br>
  $xvm=16000<br>

All settings are in mm, so be sure you are in mm mode. If in doubt enter  G21 at the command line.

For details of the settings see the [TinyG Configuration Page](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)

## System Settings
	setting | value | description |notes
	---------|---------|---------|-------
	$ja | 2000000 | JUNCTION_ACCELERATION | 2 million - centripetal acceleration around corners
	$st | 1 | SWITCH_TYPE | 0=normally open, 1=normally closed. Use NC for better noise immunity 

## Motor Settings
###Motor 1
	setting | value | description |notes
	---------|---------|---------|-------
	$1ma | 0 | MOTOR_MAP | map motor 1 to X axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$1sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$1tr | 36.54 | TRAVEL_PER_REV | Amount X moves in 1 motor revolution. Your setup may be slightly different.
	$1mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$1po | 0 | POLARITY | Depends on how you wired your motors 
	$1pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves. Good for belt machines like Shapeoko 

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
	setting | value | description |notes
	---------|---------|---------|-------
	$4ma | 2 | MOTOR_MAP | map motor 4 to second Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$4sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$4tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 motor revolution
	$4mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$4po | 0 | POLARITY | Set so it doesn't fight motor 2's Y. 
	$4pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

Note on polarity. X should move right for positive moves. Y should move away from the front of the table for positive moves. Z should move up for positive moves. Reverse polarity of the axis if this is not true. 

Dual gantry setups: Map motor 4 to the Y axis and set everything else up the same. Set polarity so the motors are not fighting each other.


## Axis Settings
### X Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$xam | 0 | AXIS_MODE | 1=standard mode
	$xvm | 16000 | VELOCITY_MAX | Your machine might go faster or slower than this. Test it and adjust current pots
	$xfr | 16000 | FEEDRATE_MAX | Typcially set the same as velocity. May be set slower but not faster.
	$xtm | 300 | TRAVEL_MAX | Max travel before crash
	$xjm | 5000000000 | JERK_MAX | That's 5 billion. Count the zeros to be sure
	$xjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$xsn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is disabled
	$xsx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is disabled
	$xsv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$xlv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$xlb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$xzb | 5 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position
	$xjh | 10000000000 | JERK_HOMING | Jerk to use during homing operations. Typically bigger than JERK_MAX

### Y Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$yam | 0 | AXIS_MODE | 1=standard mode
	$yvm | 16000 | VELOCITY_MAX | Your machine might go faster or slower than this
	$yfr | 16000 | FEEDRATE_MAX | Typcially set the same as velocity. May be set slower but not faster.
	$ytm | 220 | TRAVEL_MAX | Max travel before crash
	$yjm | 5000000000 | JERK_MAX | That's 5 billion. Count the zeros to be sure
	$yjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$ysn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is disabled
	$ysx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is disabled
	$ysv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$ylv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$ylb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$yzb | 5 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position
	$yjh | 10000000000 | JERK_HOMING | Jerk to use during homing operations

### Z Axis
	setting | value | description |notes
	---------|---------|---------|-------
	$zam | 0 | AXIS_MODE | 1=standard mode
	$zvm | 800 | VELOCITY_MAX | Heavily dependent on how clean you can get the Z axis setup. Might go faster.
	$zfr | 800 | FEEDRATE_MAX | Typically set the same as velocity. May be set slower but not faster.
	$ztm | 100 | TRAVEL_MAX | Max travel before crash
	$zjm | 50000000 | JERK_MAX | That's 50 million. Count the zeros to be sure
	$zjd | 0.01 | JUNCTION_DEVIATION | in mm - smaller is faster cornering
	$zsn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is disabled
	$zsx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is disabled
	$zsv | 3000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$zlv | 100 | LATCH_VELOCITY | Speed to back off homing switch
	$zlb | 20 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$zzb | 5 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position
	$zjh | 100000000 | JERK_HOMING | 100 million. Jerk to use during homing operations

### A, B and C Axes
Usually you are not using these, so just set some reasonable values like below. All distances are in degrees. 

	setting | value | description |notes
	---------|---------|---------|-------
	$aam | 0 | AXIS_MODE | 1=standard mode
	$avm | 60000 | VELOCITY_MAX | 
	$afr | 60000 | FEEDRATE_MAX | 
	$atm | -1 | TRAVEL_MAX | -1=infinite
	$ajm | 24000000000 | JERK_MAX | 24 billion
	$ajd | 0.1 | JUNCTION_DEVIATION |
	$ara | 1 | RADIUS |
	$asn | 0 | SWITCH_MODE_MIN | Switch mode for minimum travel switch. 0 is disabled
	$asx | 0 | SWITCH_MODE_MAX | Switch mode for maximum travel switch. 0 is disabled
	$asv | 6000 | SEARCH_VELOCITY | Homing speed to drive onto switch
	$alv | 1000 | LATCH_VELOCITY | Speed to back off homing switch
	$alb | 5 | LATCH_BACKOFF | Max distance to back off switch during latch phase
	$azb | 2 | ZERO_BACKOFF | Distance to back off switch before setting axis zero position
	$ajh | 2400000000 | JERK_HOMING | Jerk to use during homing operations

