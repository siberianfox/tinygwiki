These notes are an adjunct to reading the code, which is pretty well documented - so don't expect this page to say things twice. This page fills in some background and other information that really doesn't belong in the comments as it would make the files too verbose.

Basics:
* The TinyG firmware codebase is available on [Github](https://github.com/synthetos/TinyG github.com/synthetos/TinyG)
<br> 
* This project is based on the [NIST RS274NGC_3 Gcode specification](http://www.isd.mel.nist.gov/documents/kramer/RS274NGC_3.pdf)
* Additional guidance is provided by "CNC Programming Handbook, 3rd Edition" by Peter Smid (sorry, no link. buy the book)<br> 
* TinyG was originally forked from [grbl](https://github.com/grbl/grbl) in March 2010 and has diverged wildly since then, although we are active in grbl development and support grbl with a hardware solution [grblShield](http://www.synthetos.com/wiki/index.php?title=Projects:grblShield grblshield) <br> 
* The TinyG project is Open Source under GPLv3 for software and Creative Commons BY-SA for HW. <br> 

## Code Overview
The firmware controller, interpreter and stepper layers are organized as so: 

* main.c/tinyg.h<span class="Apple-tab-span" style="white-space:pre">			</span>initialization and main loop 
* controller.c/.h <span class="Apple-tab-span" style="white-space:pre">			</span>scheduler and related functions 
* gcode_parser.c/.h<span class="Apple-tab-span" style="white-space:pre">		</span>gcode parser / interpreter 
* json_parser.c/.h<span class="Apple-tab-span" style="white-space:pre">			</span>JSON parser
* canonical_machine.c/.h <span class="Apple-tab-span" style="white-space:pre">	</span>machine model and machining command execution 
* [[Projects:TinyG-Planner|planner.c/.h]]<span class="Apple-tab-span" style="white-space:pre">			</span>acceleration / deceleration&nbsp;line planning and feedhold execution 
* kinematics.c/.h &nbsp;<span class="Apple-tab-span" style="white-space:pre">		</span>inverse kinematics transformations and motor/axis mapping 
* stepper.c/.h<span class="Apple-tab-span" style="white-space:pre">			</span>stepper controls, DDA 
* spindle.c/.h<span class="Apple-tab-span" style="white-space:pre">			</span>spindle controls

Additional modules are:

* cycle_homing.c<span class="Apple-tab-span" style="white-space:pre">			</span>homing cycle. Other canned cycles may be added as cycle_xxxxx.c 
* arc.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>planner for arc motion. Split with canonical machine 
* util.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>general purpose utility functions, debug and logging support 
* gpio.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>parallel ports, LEDs and limit switch support 
* help.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>help screens 
* tests.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>self tests
* config.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>configuration sub-system 
* report.c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>status reports
* settings.h<span class="Apple-tab-span" style="white-space:pre">				</span>default settings and machine selection 
* settings_lumenlabMicRoV3.h ... various machine specific default files<br> 
* system.c./.h<span class="Apple-tab-span" style="white-space:pre">			</span>low-level system startup commands and configs
* xio....c/.h<span class="Apple-tab-span" style="white-space:pre">				</span>Xmega serial IO sub-system - device drivers for GCC stdio 
* xmega..c/.h<span class="Apple-tab-span" style="white-space:pre">			</span>Xmega hardware support files

A note about efficiency: Having all these layers doesn't mean that there are an excessive number of stack operations. TinyG relies heavily on the GCC compiler (-Os) for efficiency. Further hand optimization is sometimes done if profiling shows that the compiler doesn't handle that case, but this is usually not necessary. Much of the code optimizes down to inlines, static scope variables are used for efficiency where this makes sense (i.e. not passed on the stack). And even if there were a lot of function calls, most of the code doesn't need execution-time optimization (with the exception of the inner loops of the planner, steppers and some of the comm drivers). See Module Details / planner.c for a discussion of time budgets.

== Detail Pages  ==

[[Projects:TinyG-Module-Details|Module Details]] Descriptions of the above code modules<br> 
[[Projects:TinyG-Gcode-Support|Gcode Language Support]] List of G-codes, M-codes, discussion of length units and coordinate systems, feed and seek rates, state management - cycle start, stop, end, feedhold<br> 
[[Projects:TinyG-Communications|TinyG Communications]] Information on USB, [[Projects:TinyG-JSON|JSON]], RS-485 and the like<br> 
[[Projects:TinyG-JSON|TinyG JSON]] Specifications <br>
[[Projects:TinyG-Developer-Info-Project-Details|Project Details]] Project setup in AVRstudio 4, AVRStudio6, and native AVRGCC4.8 / stdlib 1.8<br> 
[[Projects:TinyG-Testing|TinyG Regression Testing Notes]]<br> [[Projects:TinyG-Github-Notes|TinyG Github Notes]] ...on using the Synthetos TinyG Github

TinyG Developer Information
===========================

[[Back to TinyG main page][]]\
 The TinyG firmware codebase is available here:
[github.com/synthetos/TinyG][]\
 This project is based on the NIST RS274NGC\_3 Gcode spec which can be
found here: [www.isd.mel.nist.gov/documents/kramer/RS274NGC\_3.pdf][]

Additional guidance is provided by "CNC Programming Handbook, 3rd
Edition" by Peter Smid (sorry, no link. buy the book)\
 TinyG was originally forked from [grbl][]in March 2010 and has diverged
wildly since then, although we are active in grbl development and
support grbl with a hardware solution ([grblshield][])\
 The TinyG project is Open Source under GPLv3 for software and Creative
Commons BY-SA for HW.\

  [[Back to TinyG main page]: Projects:TinyG "wikilink"
  [github.com/synthetos/TinyG]: https://github.com/synthetos/TinyG
  [www.isd.mel.nist.gov/documents/kramer/RS274NGC\_3.pdf]: http://www.isd.mel.nist.gov/documents/kramer/RS274NGC_3.pdf
  [grbl]: https://github.com/simen/grbl
  [grblshield]: http://www.synthetos.com/wiki/index.php?title=Projects:grblShield

Code Overview
-------------

The firmware controller, interpreter and stepper layers are organized as
so:

-   main.c/tinyg.h<span class="Apple-tab-span" style="white-space:pre">
    </span>initialization and main loop
-   controller.c/.h
    <span class="Apple-tab-span" style="white-space:pre">
    </span>scheduler and related functions
-   gcode\_parser.c/.h<span class="Apple-tab-span" style="white-space:pre">
    </span>gcode parser / interpreter
-   json\_parser.c/.h<span class="Apple-tab-span" style="white-space:pre">
    </span>JSON parser
-   canonical\_machine.c/.h
    <span class="Apple-tab-span" style="white-space:pre"> </span>machine
    model and machining command execution
-   [planner.c/.h][]<span class="Apple-tab-span" style="white-space:pre">
    </span>acceleration / deceleration line planning and feedhold
    execution
-   kinematics.c/.h
     <span class="Apple-tab-span" style="white-space:pre">

  [planner.c/.h]: Projects:TinyG-Planner "wikilink"

A note about efficiency: Having all these layers doesn't mean that there are an excessive number of stack operations. TinyG relies heavily on the GCC compiler (-Os) for efficiency. Further hand optimization is sometimes done if profiling shows that the compiler doesn't handle that case, but this is usually not necessary. Much of the code optimizes down to inlines, static scope variables are used for efficiency where this makes sense (i.e. not passed on the stack). And even if there were a lot of function calls, most of the code doesn't need execution-time optimization (with the exception of the inner loops of the planner, steppers and some of the comm drivers). See Module Details / planner.c for a discussion of time budgets.



The controller is the main loop for the program. It accepts inputs from various sources, dispatches to parsers / interpreters, and manages task threading / scheduling.&nbsp; 

Controller requirements include: 

*Accept and interpret various types of inputs, including: 
*Gcode blocks 
*Configuration commands for various sub-systems 
*Accept control commands from stdio - e.g.&nbsp;!, ~, ^x... (these arrive through the interrupt system) 
*Accept and mix inputs from multiple sources: 
**USB 
**RS-485 
**Arduino serial port (Aux) 
**strings in program memory ("files") 
**EEPROM data&nbsp; 
**SD card data (future)

'''Controller Implementation'''<br> 

The controller is an event-driven hierarchical state machine (HSM) implementing cooperative multi-tasking using inverted&nbsp;control.&nbsp;(same basic concepts as in: [http://www.state-machine.com/ www.state-machine.com/]). This means that the&nbsp;HSM is really just a list of tasks in a main loop, with the ability for a task to abort the rest of the loop depending on its status return (i.e. start the main loop over from the top).&nbsp;The highest priority tasks are run first and progressively lower priority tasks are run only if the higher priority tasks are not blocked. 

Tasks are written as continuations, so no function ever actually blocks (there is an exception), but instead returns "busy" (TG_EAGAIN) when it would ordinarily block. Each task has a&nbsp;re-entry point (callback) to resume the task once the blocking condition has been removed.&nbsp;All tasks are in a single dispatch loop with the lowest-level tasks ordered first. A task returns TG_OK or an error if it's complete, or returns TG_EAGAIN to indicate that its blocked on a lower-level condition. If TG_EAGAIN is received the controller aborts the dispatch loop and starts over again at the top, ensuring that no higher level routines (those further down in the dispatcher) will run until the routine either returns successfully (TG_OK), or returns an error.&nbsp;Interrupts run at the highest priority levels; then the kernel tasks are organized into priority groups below the interrupt levels. The priority of operations is: 

*High priority ISRs 
**issue steps to motors / count dwell timings 
**dequeue and load next stepper move 
*Medium priority ISRs 
**receive serial input (RX) 
**send serial output (TX) 
**detect and flag limit switch closures 
*Low priority ISRs 
**execute moves from the planner to compute them for the stepper loader 
*Main loop tasks&nbsp;These are divided up into layers depending on priority and blocking&nbsp;hierarchy. See tg_controller() for details.

Controller Operation 

*XIO is used to read a line of text from the current stdin device (See XIO for details)<br> 
*tg_parser is the top-level parser / dispatcher. Examines the head of the string to determine how to dispatch: 
**Gcode blocks 
**Configuration items 
**Network command / config (not implemented) 
*Individual parsers/interpreters are called from tg_parser (i.e. gcode, JSON, config). These can assume: 
**They will only receive a single line (multi-line inputs have been split) 
**They perform line normalization required for that dispatch type 
**Can run the current command to completion before receiving another command

Exception to blocking: The dispatcher scheme allows a single function to block on a sleep_mode() without upsetting scheduling. Sleep_mode() will sleep until an interrupt occurs, then resume after the interrupt is done. This type of blocking occurs in the character transmit function. This allows stdio's fprint() functions to work - being as how they don't know about continuations. 

Futures: Using a super loop instead of an event system is a design tradoff - or more to the point - a hack. If the flow of control gets much more complicated it will make sense to replace this section with an event driven dispatcher or something like FreeRTOS. 

'''Flow Control''' 

There are two types of flow control to avoid overrunning the inputs: 

APPLICATION_LEVEL FLOW CONTROL - The appication will return a prompt with the characters "ok" in it when it is ready for the next block. It's possible to edit the code so only "ok" is returned if you want it to work more like grbl.<br>SERIAL FLOW CONTROL - XON/XOFF flow control can also be enabled at the serial level. In this case the serial RX buffer is kept full by a certain percentage set by the XOFF_HI_WATER_MARK / XOFF_LO_WATER_MARK settings (compile time).<br>

=== How To Code Continuations  ===

Continuations are used to manage points where the application would ordinarily block. Call it application managed threading. By coding using continuations the application does not need an RTOS and is extremely responsive (there are no "ticks") 

Rules for writing a continuation task: 

*A continuation is a pair of routines. The first is the outer routine,&nbsp;the second the continuation or callback. for an example see mp_dwell() and mp_dwell_callback() 
*The main routine is called first and should never block. It may have function arguments. It performs all initial actions and typically sets up a static structure (singleton) to hold data that is needed by the continuation routine. The main routine should end by returning a uint8_t TG_OK or an error code. 
*The continuation task is a callback that is permanently registered (OK, installed)&nbsp;at the right level of the blocking heirarchy in the tg_controller loop; where it will be called repeatedly by the controller. The continuation cannot have input args - all necessary data must be available in the static struct (or by some other means). 
*Continuations should be coded as state machines. See the homing cycle or the aline execution routines as examples. Common states used by most machines include: OFF, NEW, or RUN. 
**OFF means take no action (return NOOP). 
**NEW is the the state on initial entry after the main routine. This may be used to perform any further initialization at the continuation level. 
**RUN is a catch-all for simple routines. More complex state&nbsp;machines may have numerous other states. 
*The continuation must return the following codes and may return additional codes to indicate various exception conditions: 
**TG_NOOP: No operation occurred. This is the usual return from an OFF state. All continuations must be callable with no effect when they are OFF (as they are called repeatedly by the controller whether or not they are active). 
**TG_EAGAIN: The continuation is blocked or still processing. This one is really important. As long as the continuation still has work to do it must return TG_EAGAIN. Returning eagain causes the tg_controller dispatcher to restart the controller loop from the beginning, skipping all later routines. This enables heirarchical blocking to be performed. The later routines will not be run until the blocking conditions at the lower-level are&nbsp;removed. 
**TG_OK; The continuation task has completed - i.e. it has just transitioned to OFF. TG_OK should only be returned only once. The next state will be OFF, which will return NOOP. 
**TG_COMPLETE: This additional state is used for nesting state machines such as the homing cycle. The lower-level routines called by a parent will return TG_EAGAIN until they are done, then they return TG_OK. The return codes from the continuation should be trapped by a wrapper routine that manages the parent and child returns. When the parent REALLY wants to return it sends the wrapper TG_COMPLETE, which is translated to an OK for the parent routine.<br>

== canonical_machine.c/.h  ==

This provides an interface layer to machining and related functions. It implements the NIST RS274NGC canonical machining functions (more or less). Some functions have been added, some not implemented, and some of the calling conventions are different. The canonical machine normalizes all coordinates and parameters to internal representation, keeps the Gcode model state, and makes calls to the planning and cycle layers for actual movement.&nbsp; 

The canonical machine is extensible to handle canned cycles like homing cycles, tool changes, probe cycles, and other complex cycles using motion primitives. (I'm not sure if this is exactly how Kramer planned it - particularly when it comes to state management, but it's how it's implemented). 

The canonical machine is the sole master of the gm struct, and all accesses to it must go through accessors (setters and getters). This is because in some cases the conversions into and out of gm's internal canonical form are complex and it's best not to have any other parts of the program messing with the internals.

== stepper.c/.h  ==

The stepper module performs the following functions: 

*Stepper system init 
*Stepper DDA load&nbsp; 
*Stepper pulse generation / DDA execution (ISR) 
*Dwell downcounter (ISR) 
*Software interrupt (timer) to load stepper segment (ISR) 
*Software interrupt (timer) to request move execution (ISR) 
*Various prep routines and other supporting functions, including a null prep for M code and other non-timer functions.

=== Digital Differential Analyser aka Bresenham Algorithm aka DDA  ===

Coordinated motion (line drawing) is performed using a classic Bresenham DDA as per reprap and grbl.&nbsp;A number of additional steps are taken to optimize interpolation and pulse train accuracy such as the addition of&nbsp;fractional step handing within the DDA, optimal clock timing generation, and phase correction between pulse sequences ("segments"). 

*The DDA accepts and processes fractional motor steps. Steps are passed to the move queue as doubles, and may have fractional values for the steps (e.g. 234.934 steps is OK). The DDA implements fractional steps and interpolation by extending the counter range downward using a variable-precision fixed-point binary scheme. It uses the DDA_SUBSTEPS setting to control default fixed-point precision. 
*The DDA is not used as a 'ramp' for acceleration management. Accel is computed as 3rd order (maximum jerk) equations that generate piecewise linear accel/decel segments to the DDA. The DDA runs at a constant rate for each segment up to a maximum of 50 Khz step rate. The segment update rate (time length of segments) is set by the MIN_SEGMENT_USEC setting in planner.h - which is typically 10 ms segment times. 
*The DDA rate for a segment is set to an integer multiple of the step freqency of the fastest motor (major axis). This amount of overclocking is controlled by the DDA_OVERCLOCK value, typically 16x. A minimum DDA rate is enforced that prevents overflowing the 16 bit DDA timer PERIOD value. The DDA timer always runs at 32 Mhz. The prescaler is not used. Various methods are used to keep the numbers in range for long lines. See _mq_set_f_dda() for details. (This is similar to the oversampling done by CD players to reduce aliasing noise). 
*Pulse phasing is preserved between segments if possible. This makes for smoother motion, particularly at very low speeds and short segment lengths (avoids pulse jitter). Phase continuity is achieved by simply not resetting the DDA counters across segments. In some cases the differences between timer values across segments are too large for this to work, and you risk motor stalls due to pulse starvation. These cases are detected and the counters are reset to prevent stalling. 
*Pulse phasing is also helped by minimizing the time spent loading the next move segment. To this end as much as possible about that move is pre-computed during the queuing phase. Also, all moves are loaded from the interrupt level, avoiding the need for mutual exclusion locking or volatiles (which slow things down).

