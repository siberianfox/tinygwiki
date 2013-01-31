The TinyG firmware codebase is available on [Github](https://github.com/synthetos/TinyG github.com/synthetos/TinyG)
<br> This project is based on the [NIST RS274NGC_3 Gcode specification](http://www.isd.mel.nist.gov/documents/kramer/RS274NGC_3.pdf)

Additional guidance is provided by "CNC Programming Handbook, 3rd Edition" by Peter Smid (sorry, no link. buy the book)<br> 
TinyG was originally forked from [grbl](https://github.com/grbl/grbl) in March 2010 and has diverged wildly since then, although we are active in grbl development and support grbl with a hardware solution [grblShield](http://www.synthetos.com/wiki/index.php?title=Projects:grblShield grblshield) <br> 
The TinyG project is Open Source under GPLv3 for software and Creative Commons BY-SA for HW. <br> 

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
