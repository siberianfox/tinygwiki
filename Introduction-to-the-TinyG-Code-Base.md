These notes are an adjunct to reading the code, which is pretty well documented - so don't expect this page to say things twice. This page fills in some background and other information that really doesn't belong in the comments as it would make the files too verbose.

Basics:
* The TinyG firmware codebase is available on [Github](https://github.com/synthetos/TinyG)
<br> 
* This project is based on the [NIST RS274NGC_3 Gcode specification](https://rs274ngc.googlecode.com/files/RS274NGC_3.pdf)
* Additional guidance is provided by "CNC Programming Handbook, 3rd Edition" by Peter Smid (sorry, no link. buy the book)<br> 
* TinyG was originally forked from [grbl](https://github.com/grbl/grbl) in March 2010 and has diverged wildly since then, although we are active in grbl development and support grbl with a hardware solution [grblShield](https://github.com/synthetos/grblShield/wiki)<br> 
* The TinyG project is Open Source under [GPLv2 with the beRTOS exception](https://github.com/synthetos/TinyG/wiki/TinyG-Licensing) for software and Creative Commons NC-BY-SA for HW.<br> 

## Code Notes
### Module Organization
The firmware controller, interpreter, canonical machine and stepper layers are organized as so: 

* main.c/tinyg.h - initialization and main loop 
* [controller.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#controller) - scheduler and related functions 
* gcode_parser.c/.h - gcode parser / interpreter 
* json_parser.c/.h - JSON parser
* [canonical_machine.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#canonical-machine) - machine model and machining command execution 
* [planner.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#planner) ... and related line and arc files - acceleration / deceleration planning and feedhold 
* cycle_homing.c - homing cycle. Other canned cycles may be added as cycle_xxxxx.c 
* kinematics.c/.h - inverse kinematics transformations and motor/axis mapping 
* [stepper.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#stepper-module) - stepper controls, DDA 
* spindle.c/.h - spindle controls

Additional modules are:

* [config.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#config-system) - generic part of configuration and command execution sub-system 
* [config_app.c/.h](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base#config-system) - application specific part of configuration and command execution sub-system 
* report.c/.h - exception reports, status reports and other reporting functions
* util.c/.h - general purpose utility functions, debug and logging support 
* gpio.c/.h - parallel ports, LEDs and limit switch support 
* help.c/.h - help screens 
* tests.c/.h - self tests
* settings.h ...and related files in /settings - default settings for various machines 
* system.c./.h - low-level system startup commands and configs
* xio....c/.h - Xmega serial IO sub-system - device drivers for GCC stdio 
* xmega...c/.h - Xmega hardware support files

A note about efficiency: Having all these layers doesn't mean that there are an excessive number of stack operations. TinyG relies heavily on the GCC compiler (-Os) for efficiency. Further hand optimization is sometimes done if profiling shows that the compiler doesn't handle that case, but this is usually not necessary. Much of the code optimizes down to inlines, static scope variables are used for efficiency where this makes sense (i.e. not passed on the stack). And even if there were a lot of function calls, most of the code doesn't need execution-time optimization (with the exception of the inner loops of the planner, steppers and some of the comm drivers). See Module Details / planner.c for a discussion of time budgets.

### Controller
The controller is the main loop for the program. It accepts inputs from various sources, dispatches to parsers / interpreters, and manages task threading / scheduling.

The controller is an event-driven hierarchical state machine (HSM) implementing cooperative multi-tasking using inverted control. This means that the HSM is really just a list of tasks in a main loop, with the ability for a task to skip the rest of the loop depending on its status return (i.e. start the main loop over from the top). 

Tasks are written as continuations, so no function ever actually blocks (there is an exception), but instead returns "busy" (STAT_EAGAIN) when it would ordinarily block. Each task has a re-entry point (callback) to resume the task once the blocking condition has been removed.

All tasks are in a single dispatch loop with the lowest-level (highest priority) tasks ordered first. The highest priority tasks are run first and progressively lower priority tasks are run only if the higher priority tasks are not blocked. 

A task returns STAT_OK or an error if it's complete, or returns STAT_EAGAIN to indicate that its blocked on a lower-level condition. If STAT_EAGAIN is received the controller aborts the dispatch loop and starts over at the top, ensuring that no higher level routines (those further down in the dispatcher) will run until the routine either returns successfully (STAT_OK), or returns an error. Interrupts run at the highest priority levels; then the kernel tasks are organized into priority groups below the interrupt levels. The priority of operations can be seen by reading the main dispatcher table and the interrupt priority settings in system.h

Exception to blocking: The dispatcher scheme allows one (and only one) function to block on a sleep_mode() or infinite loop without upsetting scheduling. Sleep_mode() will sleep until an interrupt occurs, then resume after the interrupt is done. This type of blocking occurs in the character transmit (TX) function. This allows stdio's printf() functions to work - being as how they don't know about continuations. 

#### How To Code Continuations
Continuations are used to manage points where the application would ordinarily block. Call it application managed threading. By coding using continuations the application does not need an RTOS and is extremely responsive (there are no "ticks", or scheduled context changes)

Rules for writing a continuation task: 

* A continuation is a pair of routines. The first is the outer routine, the second the continuation or callback. For an example see ar_arc() and ar_arc_callback() in plan_arc.c.
* The main routine is called first and should never block. It may have function arguments. It performs all initial actions and typically sets up a static structure (singleton) to hold data that is needed by the continuation routine. The main routine should end by returning STAT_OK or a STAT_xxxx error code. 
* The continuation task is a callback that is permanently registered (OK, installed) at the right level of the blocking heirarchy in the tg_controller loop; where it will be called repeatedly by the controller. The continuation cannot have input args - all necessary data must be available in the static struct (or by some other means). 
* Continuations should be coded as state machines. See the homing cycle or the aline execution routines as examples. Common states used by most machines include: OFF, NEW, or RUN. 
 * OFF means take no action (return STAT_NOOP). 
 * NEW is the the state on initial entry after the main routine. This may be used to perform any further initialization at the continuation level. 
 * RUN is a catch-all for simple routines. More complex state machines may have numerous other states. 
* The continuation must return the following codes and may return additional codes to indicate various exception conditions: 
 * STAT_NOOP: No operation occurred. This is the usual return from an OFF state. All continuations must be callable with no effect when they are OFF (as they are called repeatedly by the controller whether or not they are active). 
 * STAT_EAGAIN: The continuation is blocked or still processing. This one is really important. As long as the continuation still has work to do it must return STAT_EAGAIN. Returning eagain causes the tg_controller dispatcher to restart the controller loop from the beginning, skipping all later routines. This enables hierarchical blocking to be performed. The later routines will not be run until the blocking conditions at the lower-level are removed. 
 * STAT_OK: The continuation task has completed - i.e. it has just transitioned to OFF. STAT_OK should only be returned only once. The next state will be OFF, which will return STAT_NOOP. 
 * STAT_COMPLETE: This additional state is used for nesting state machines such as the homing cycle. The lower-level routines called by a parent will return STAT_EAGAIN until they are done, then they return STAT_OK. The return codes from the continuation should be trapped by a wrapper routine that manages the parent and child returns. When the parent REALLY wants to return it sends the wrapper STAT_COMPLETE, which is translated to STAT_OK by the parent routine.
 * STAT_xxxx: Error returns. See tinyg.h for definitions.

## Canonical Machine
The canonical machine provides an interface layer to machining and related functions. It implements the NIST RS274NGC canonical machining functions (more or less). Some functions have been added, some not implemented, and some of the calling conventions are different. The canonical machine normalizes all coordinates and parameters to internal representation, keeps the Gcode model state, and makes calls to the planning and cycle layers for actual movement. 

The canonical machine is extensible to handle canned cycles like homing cycles, tool changes, probe cycles, and other complex cycles using motion primitives. (I'm not sure if this is exactly how Kramer planned it - particularly when it comes to state management, but it's how it's implemented). 

The canonical machine is the sole master of the gm struct, and all accesses to it must go through accessors (setters and getters). This is because in some cases the conversions into and out of gm's internal canonical form are complex and it's best not to have any other parts of the program messing with the internals.

## Planner
The planner module plans and executes moves for lines, arcs, dwells, spindle controls, program stop and end, and any other move that must be synchronized with move execution. It consists of the following files:
* planner.c
* planner.h
* plan_line.c
* plan_line.h
* plan_arc.c
* plan_arc.h

The planner data structures consist of a list of planning buffers (28 bf's), a planning context or move-model (mm), and a runtime execution model (mr). 

When a new move is introduced into the planner from the canonical machine the planner examines the current planning model state (mm) and the moves queued in the bf's to determine how the move can be executed. The planner figures out the initial velocity and maximum "cruise" velocity, and always assumes the exit velocity will be zero - as it doesn't know if there are any other moves behind the move being processed. It takes acceleration / deceleration limits into account, and also takes maximum cornering limits into account. It also replans moves sin the buffer, which may change now that there is a new "last move". Then it queues the move into the bf queue.

When a move is ready to be executed the planner (at this point called the "exec") copies the data from the head bf into the move runtime (mr) and generates 5 ms acceleration / cruise / deceleration segments to be fed to the stepper module. When the move is completed it returns the head buffer to the buffer pool and pulls the next move from the queue - if there is one.

The planner also handles feedholds and cycle starts after feedholds. In a feedhold the planner executes a state machine that
* (1) allows the currently executing segment and the next segment to execute (for a deceleration latency of 5 - 10 ms)
* (2) plans the move currently in mr down to zero at the hold point
* (3) replans the rest of the queue to accelerate back up from the hold point
* (4) halts execution once the deceleration reaches the hold point

When a cycle start is provided the hold is released and the replanned buffer executes from the hold point

## Stepper Module
The stepper module implements a weird variant of the Bresenham Algorithm (aka DDA).

Coordinated motion (line drawing) is performed using a classic Bresenham DDA, but a number of additional steps are taken to optimize interpolation and pulse train accuracy such as constant clock-rate timing generation, fractional step handling within the DDA, and phase correction between pulse sequences ("segments"). 

* The DDA clock always runs at its maximum rate, which is 50 Khz "pulse rate". Sometimes we hear "but this is so inefficient!"  Well yes and no. If you assume that at some point the system will need to run at the maximum rate, then the system must be able to operate steady state (not burst) at that rate without cycle-starving any other processing or communications. So if the system works at the maximum rate, then why not run it at max all the time? This has the advantage of dramatically reducing single-axis and multi-axis aliasing errors, and it keeps the code much simpler.

* Note that since the DDA clock runs at a constant rate, the DDA is is not used as a 'ramp' for acceleration management. Acceleration/deceleration is computed as 3rd order (maximum jerk) equations that generate piecewise linear accel/decel segments that are sent to the DDA for execution. The timing at which the segments are updated (segment update rate) is set by the MIN_SEGMENT_USEC setting in planner.h. That update time (i.e. segment length in milliseconds) is "tweaked" for each move to ensure an integer number of segments for the move, and turns out to be nominally about 5 ms per segment. In special cases it can be as little as 2.5 ms.

* The DDA accepts and processes fractional motor steps. Steps are passed to the move queue as floats (IEEE 32 bit), and may have fractional values for the steps; e.g. 234.93427 steps is OK. The DDA implements fractional steps and interpolation by extending the DDA phase accumulator (i.e. counter) range downward using a variable-precision fixed-point binary scheme. It uses the DDA_SUBSTEPS setting to set the fixed-point precision. 

* Pulse phasing is preserved between segments in most cases (there are a few rare exceptions that must sacrifice phase integrity). This makes for smoother motion, particularly at very low speeds and short segment lengths (avoids pulse jitter). Phase continuity is achieved by simply not resetting the DDA phase accumulators (counters) across segments. In some cases the differences between pulse rates values across segments are too large for this to work, and you risk motor stalls due to pulse starvation. These cases are detected and the accumulators are reset to prevent stalling. 

* Pulse phasing is also helped by minimizing the time spent loading the next move segment. To achieve this the "on-deck" move is pre-computed during the queuing phase (at least as much as possible). Also, all moves are loaded from interrupts, avoiding the need for mutual exclusion locking or volatiles (which slow things down).

There is a rather involved set of nested interrupts and pulls that occur to support the above - see the header comments in stepper.c for an explanation.

## Config System
The config module implements internal data handling for communications in and out and command execution. The internal structure for a command is a cmdObj, and commands are kept in a cmdObj list that can contain one or more commands. 

These structures are used to provide da clean separation between the "wire form" of the commands, which may be text or JSON based, and the internal form on which the system operates. This allows TinyG to support text mode and JSON mode strictly at the parser and serializer level, without carrying knowledge of the IO for to lower levels.

For a complete discussion of the command and config system see the header notes in config.h