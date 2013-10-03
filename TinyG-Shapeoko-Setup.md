This page describes the setup for a 3 axis (single Y) original shapeoko with 375mm slides. You may need to change this for larger slides or dual Y. 

Settings use the following format by way of example:

  $jd=0.01<br>
  $xvm=16000<br>

All settings are in mm, so be sure you are in mm mode. If in doubt enter  G21 at the command line.

For details of the command see the [TinyG Configuration Page](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)

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
	$2ma | 1 | MOTOR_MAP | map motor 2 to Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$2sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$2tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 revolution
	$2mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$2po | 0 | POLARITY | Depends on how you wired your motors 
	$2pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves
	$3ma | 2 | MOTOR_MAP | map motor 3 to Z axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$3sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$3tr | 1.25 | TRAVEL_PER_REV | Amount Z moves in 1 revolution
	$3mi | 4 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$3po | 0 | POLARITY | Depends on how you wired your motors 
	$3pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves
	$4ma | 2 | MOTOR_MAP | map motor 4 to second Y axis (0=X, 1=Y, 2=Z, 3=A, 4=B, 5=C)
	$4sa | 1.8 | STEP_ANGLE | set 1.8 degrees for 200 step motors, 0.9 for 400 step motors
	$4tr | 36.54 | TRAVEL_PER_REV | Amount Y moves in 1 motor revolution
	$4mi | 8 | MICROSTEPS | Supported values are 1, 2, 4 and 8
	$4po | 0 | POLARITY | Set so it doesn't fight motor 2's Y. 
	$4pm | 0 | POWER_MODE | 0 leaves steppers on if anything moves

Note on polarity. X should mov the right for positive moves. Y should move away from the front of the table for positive moves. Z should move up for positive moves. Reverse polarity of that axis if this is not true. 

Dual gantry setups: Map motor 4 to the Y axis and set everything else up the same. Set polarity so the motors are not fighting each other.


### Axis Settings
#### X Axis
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
	$xjh | 5000000000 | JERK_HOMING | Jerk to use during homing operations. Typically bigger than JERK_MAX


#define Y_AXIS_MODE				AXIS_STANDARD
#define Y_VELOCITY_MAX			16000
#define Y_FEEDRATE_MAX			Y_VELOCITY_MAX
#define Y_TRAVEL_MAX			220
#define Y_JERK_MAX				5000000000			// 5,000,000,000
#define Y_JUNCTION_DEVIATION	JUNCTION_DEVIATION
#define Y_SWITCH_MODE_MIN		SW_MODE_HOMING
#define Y_SWITCH_MODE_MAX		SW_MODE_DISABLED
#define Y_SEARCH_VELOCITY		3000
#define Y_LATCH_VELOCITY		100
#define Y_LATCH_BACKOFF			20
#define Y_ZERO_BACKOFF			3
#define Y_JERK_HOMING			10000000000			// xjh

#define Z_AXIS_MODE				AXIS_STANDARD
#define Z_VELOCITY_MAX			800
#define Z_FEEDRATE_MAX			Z_VELOCITY_MAX
#define Z_TRAVEL_MAX			100
#define Z_JERK_MAX				50000000			// 50,000,000
#define Z_JUNCTION_DEVIATION	JUNCTION_DEVIATION
#define Z_SWITCH_MODE_MIN		SW_MODE_DISABLED
#define Z_SWITCH_MODE_MAX		SW_MODE_HOMING
#define Z_SEARCH_VELOCITY		Z_VELOCITY_MAX
#define Z_LATCH_VELOCITY		100
#define Z_LATCH_BACKOFF			20
#define Z_ZERO_BACKOFF			10
#define Z_JERK_HOMING			1000000000			// xjh

#define A_AXIS_MODE				AXIS_STANDARD
#define A_VELOCITY_MAX			60000
#define A_FEEDRATE_MAX			48000
#define A_TRAVEL_MAX			400					// degrees
#define A_JERK_MAX				24000000000			// yes, 24 billion
#define A_JUNCTION_DEVIATION	0.1
#define A_RADIUS				1.0
#define A_SWITCH_MODE_MIN		SW_MODE_HOMING
#define A_SWITCH_MODE_MAX		SW_MODE_DISABLED
#define A_SEARCH_VELOCITY		6000
#define A_LATCH_VELOCITY		1000
#define A_LATCH_BACKOFF			5
#define A_ZERO_BACKOFF			2
#define A_JERK_HOMING			A_JERK_MAX

#define B_AXIS_MODE				AXIS_DISABLED
#define B_VELOCITY_MAX			3600
#define B_FEEDRATE_MAX			B_VELOCITY_MAX
#define B_TRAVEL_MAX			-1
#define B_JERK_MAX				20000000
#define B_JUNCTION_DEVIATION	JUNCTION_DEVIATION
#define B_RADIUS				1

#define C_AXIS_MODE				AXIS_DISABLED
#define C_VELOCITY_MAX			3600
#define C_FEEDRATE_MAX			C_VELOCITY_MAX
#define C_TRAVEL_MAX			-1
#define C_JERK_MAX				20000000
#define C_JUNCTION_DEVIATION	JUNCTION_DEVIATION
#define C_RADIUS				1

#ifdef __PLAN_R2
#undef  X_JERK_MAX
#define X_JERK_MAX				6000000				// xjm
#undef  Y_JERK_MAX
#define Y_JERK_MAX				6000000				// xjm
#undef  Z_JERK_MAX
#define Z_JERK_MAX				600000				//
#endif

// *** DEFAULT COORDINATE SYSTEM OFFSETS ***
// Our convention is:
//	- leave G54 in machine coordinates to act as a persistent absolute coordinate system
//	- set G55 to be a zero in the middle of the table
//	- no action for the others

#define G54_X_OFFSET 0			// G54 is traditionally set to all zeros
#define G54_Y_OFFSET 0
#define G54_Z_OFFSET 0
#define G54_A_OFFSET 0
#define G54_B_OFFSET 0
#define G54_C_OFFSET 0

#define G55_X_OFFSET (X_TRAVEL_MAX/2)	// set g55 to middle of table
#define G55_Y_OFFSET (Y_TRAVEL_MAX/2)
#define G55_Z_OFFSET 0
#define G55_A_OFFSET 0
#define G55_B_OFFSET 0
#define G55_C_OFFSET 0

#define G56_X_OFFSET (X_TRAVEL_MAX/2)	// special settings for running braid tests
#define G56_Y_OFFSET 20
#define G56_Z_OFFSET -10
#define G56_A_OFFSET 0
#define G56_B_OFFSET 0
#define G56_C_OFFSET 0

#define G57_X_OFFSET 0
#define G57_Y_OFFSET 0
#define G57_Z_OFFSET 0
#define G57_A_OFFSET 0
#define G57_B_OFFSET 0
#define G57_C_OFFSET 0

#define G58_X_OFFSET 0
#define G58_Y_OFFSET 0
#define G58_Z_OFFSET 0
#define G58_A_OFFSET 0
#define G58_B_OFFSET 0
#define G58_C_OFFSET 0

#define G59_X_OFFSET 0
#define G59_Y_OFFSET 0
#define G59_Z_OFFSET 0
#define G59_A_OFFSET 0
#define G59_B_OFFSET 0
#define G59_C_OFFSET 0