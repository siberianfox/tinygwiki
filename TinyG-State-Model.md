![TinyG State Model](http://farm8.staticflickr.com/7395/10154487504_26c80661e1_o.jpg)

<pre>

// #### LAYER 8 CRITICAL REGION ###
// #### DO NOT CHANGE THESE ENUMERATIONS WITHOUT COMMUNITY INPUT #### 
enum cmCombinedState {				// check alignment with messages in config.c / msg_stat strings
	COMBINED_INITIALIZING = 0,		// [0] machine is initializing
	COMBINED_READY,					// [1] machine is ready for use
	COMBINED_ALARM,					// [2] machine is in alarm state (shut down)
	COMBINED_PROGRAM_STOP,			// [3] program stop or no more blocks
	COMBINED_PROGRAM_END,			// [4] program end
	COMBINED_RUN,					// [5] motion is running
	COMBINED_HOLD,					// [6] motion is holding
	COMBINED_PROBE,					// [7] probe cycle active
	COMBINED_CYCLE,					// [8] machine is running (cycling)
	COMBINED_HOMING,				// [9] homing is treated as a cycle
	COMBINED_JOG					// [10] jogging is treated as a cycle
};
//#### END CRITICAL REGION ####

enum cmMachineState {
	MACHINE_INITIALIZING = 0,		// machine is initializing
	MACHINE_READY,					// machine is ready for use
	MACHINE_ALARM,					// machine is in alarm state (shutdown)
	MACHINE_PROGRAM_STOP,			// program stop or no more blocks
	MACHINE_PROGRAM_END,			// program end
	MACHINE_CYCLE,					// machine is running (cycling)
};

enum cmCycleState {
	CYCLE_OFF = 0,					// machine is idle
	CYCLE_MACHINING,				// in normal machining cycle
	CYCLE_PROBE,					// in probe cycle
	CYCLE_HOMING,					// homing is treated as a specialized cycle
	CYCLE_JOG						// jogging is treated as a specialized cycle
};

enum cmMotionState {
	MOTION_STOP = 0,				// motion has stopped
	MOTION_RUN,						// machine is in motion
	MOTION_HOLD						// feedhold in progress
};

enum cmFeedholdState {				// feedhold_state machine
	FEEDHOLD_OFF = 0,				// no feedhold in effect
	FEEDHOLD_SYNC, 					// start hold - sync to latest aline segment
	FEEDHOLD_PLAN, 					// replan blocks for feedhold
	FEEDHOLD_DECEL,					// decelerate to hold point
	FEEDHOLD_HOLD,					// holding
	FEEDHOLD_END_HOLD				// end hold (transient state to OFF)
};

enum cmHomingState {				// applies to cm.homing_state
	HOMING_NOT_HOMED = 0,			// machine is not homed (0=false)
	HOMING_HOMED = 1				// machine is homed (1=true)
};
</pre>