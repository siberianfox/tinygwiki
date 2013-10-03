This page describes the setup for a 3 axis (single Y) original shapeoko with 375mm slides. You may need to change this for larger slides or dual Y. 

All settings are in mm, so be sure you are in mm mode. If in doubt enter  G21 at the command line.


	cmd line | setting | Notes
	$jd=0.01 | JUNCTION_DEVIATION |default value, in mm - smaller is faster

2000000      JUNCTION_ACCELERATION - 2 million - centripetal acceleration around corners

// *** settings.h overrides ***

#undef COMM_MODE
#define COMM_MODE			JSON_MODE

#undef JSON_VERBOSITY
#define JSON_VERBOSITY 			JV_CONFIGS

#undef SWITCH_TYPE
#define SWITCH_TYPE 			SW_TYPE_NORMALLY_CLOSED	// one of: SW_TYPE_NORMALLY_OPEN, SW_TYPE_NORMALLY_CLOSED

// *** motor settings ***

#define M1_MOTOR_MAP 			AXIS_X	// 1ma
#define M1_STEP_ANGLE			1.8		// 1sa
#define M1_TRAVEL_PER_REV		36.54	// 1tr
#define M1_MICROSTEPS			8		// 1mi		1,2,4,8
#define M1_POLARITY				1		// 1po		0=normal, 1=reversed
#define M1_POWER_MODE			0		// 1pm		TRUE=low power idle enabled 

#define M2_MOTOR_MAP			AXIS_Y
#define M2_STEP_ANGLE			1.8
#define M2_TRAVEL_PER_REV		36.54
#define M2_MICROSTEPS			8
#define M2_POLARITY				0
#define M2_POWER_MODE			0

#define M3_MOTOR_MAP			AXIS_Z
#define M3_STEP_ANGLE			1.8
#define M3_TRAVEL_PER_REV		1.25
#define M3_MICROSTEPS			4
#define M3_POLARITY				1
#define M3_POWER_MODE			1

#define M4_MOTOR_MAP			AXIS_A
#define M4_STEP_ANGLE			1.8
#define M4_TRAVEL_PER_REV		360		// degrees per motor rev - no gearing
#define M4_MICROSTEPS			8
#define M4_POLARITY				0
#define M4_POWER_MODE			0

/* Mapping for dual grantry setup
#define M1_MOTOR_MAP 			AXIS_X	// 1ma
#define M1_STEP_ANGLE			0.9		// 1sa
#define M1_TRAVEL_PER_REV		36.54	// 1tr
#define M1_MICROSTEPS			8		// 1mi		1,2,4,8
#define M1_POLARITY				0		// 1po		0=normal, 1=reversed
#define M1_POWER_MODE			1		// 1pm		TRUE=low power idle enabled 

#define M2_MOTOR_MAP			AXIS_Y
#define M2_STEP_ANGLE			0.9
#define M2_TRAVEL_PER_REV		36.54
#define M2_MICROSTEPS			8
#define M2_POLARITY				1
#define M2_POWER_MODE			1

#define M3_MOTOR_MAP			AXIS_Y
#define M3_STEP_ANGLE			0.9
#define M3_TRAVEL_PER_REV		36.54
#define M3_MICROSTEPS			8
#define M3_POLARITY				0
#define M3_POWER_MODE			1

#define M4_MOTOR_MAP			AXIS_Z
#define M4_STEP_ANGLE			0.45
#define M4_TRAVEL_PER_REV		2.1166
#define M4_MICROSTEPS			8
#define M4_POLARITY				1
#define M4_POWER_MODE			0
*/

// *** axis settings ***

#define X_AXIS_MODE				AXIS_STANDARD		// xam		see canonical_machine.h cmAxisMode for valid values
#define X_VELOCITY_MAX			16000 				// xvm		G0 max velocity in mm/min
#define X_FEEDRATE_MAX			X_VELOCITY_MAX		// xfr 		G1 max feed rate in mm/min
#define X_TRAVEL_MAX			220					// xtm		travel between switches or crashes
//#define X_TRAVEL_MAX			300					// xtm		travel between switches or crashes
#define X_JERK_MAX				5000000000			// xjm		yes, that's "5 billion" mm/(min^3)
#define X_JUNCTION_DEVIATION	JUNCTION_DEVIATION	// xjd
#define X_SWITCH_MODE_MIN 		SW_MODE_HOMING		// xsn		SW_MODE_DISABLED, SW_MODE_HOMING, SW_MODE_LIMIT, SW_MODE_HOMING_LIMIT
//#define X_SWITCH_MODE_MAX 		SW_MODE_DISABLED	// xsx		SW_MODE_DISABLED, SW_MODE_HOMING, SW_MODE_LIMIT, SW_MODE_HOMING_LIMIT
#define X_SWITCH_MODE_MAX 		SW_MODE_LIMIT	// xsx		SW_MODE_DISABLED, SW_MODE_HOMING, SW_MODE_LIMIT, SW_MODE_HOMING_LIMIT
#define X_SEARCH_VELOCITY		3000				// xsv		minus means move to minimum switch
#define X_LATCH_VELOCITY		100					// xlv		mm/min
#define X_LATCH_BACKOFF			20					// xlb		mm
#define X_ZERO_BACKOFF			3					// xzb		mm
//#define X_JERK_HOMING			X_JERK_MAX			// xjh
#define X_JERK_HOMING			10000000000			// xjh

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

