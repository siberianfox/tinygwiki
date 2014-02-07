This page discusses using TinyG with external drivers. While some users are using external drivers, this is still a rather new area for us. We encourage you help us record your knowledge, folklore, voodoo in this (and other) areas by actively contributing to the wiki.

All pinouts and other board information refers to the version 8 boards (v8), but with some translation should apply as well to the v7 (v6 and earlier did not bring these signals out).

### Connectors
The following connectors are brought out for driving external stepper drivers.

* J17 - corresponds to motor 1
* J18 - corresponds to motor 2
* J19 - corresponds to motor 3
* J20 - corresponds to motor 4

### Signals
* The connectors bring out the Step, Direction, ~Enable signals and a ground. These are clearly labeled on the board silkscreen.

* J17 also brings out 3.3 volts and can be used to draw up to 50 ma.

* The signals are 3 volt signals. The output current is also very limited, as these are simply outputs from the micro-controller itself. However, these signals should be adequate to drive 3.3 volt and 5 volt external systems, but may be marginal in some cases. Please watch for this.

* ~Enable is active low. That is, it goes to zero volts (almost) when the motor is active. There is no software provision to make it active HI.

### Use
The following should be noted if you are going to use these outputs.

* If you use external drivers be aware that the same signals are still being fed to the on-board drivers. It's good practice to turn the on-board current setting potentiometer all the way down for any axis that you are using externally. This is especially true if somehow you have hacked the motor enable code to be active HI.

* Step pulse widths:
 * Step pulse widths on firmware build 380.xx and earlier as about 800 nanoseconds wide (just under 1 microsecond). Some drivers are OK with this (like the on-board drivers), others are not. Additionally, depending on your external wiring, you may experience capacitive losses to the point where these pulses are effectively shortened, or do not achieve full voltage (which may cause some 5v systems to not see them at all).
 * Step pulses have been stretched on builds 412.xx and above and are at least 2 microseconds wide.

* Microsteps 
 * Microsteps need to be set ion 2 places: on the external driver and in the TinyG settings. If these don't match you will see movement that it too fast or too slow - usually by factors of 2. 
 * TinyG supports microsteps if 1x, 2x, 4, and 8x. Some external drivers use microstep settings other than these - like 10x on Gecko drivers. TinyG will accept "non-standard" settings. It will warn you that you are inputting a non-supported setting, but will accept it and compute in just the same. Yet another reason to turn down the ob-board drivers.
 * At some point TinyG cannot output steps fast enough to keep up with higher microstep settings. If you want to use settings above 8x you need to compute the upper limit. TinyG is capable of delivering pulses up to 50,000 pulses per second (50 KHz). The formula for computing maximum velocity via pulses is: 
