This page discusses using TinyG with external drivers. All pinouts and other board information refers to the version 8 boards (v8), but with some translation should apply as well to the v7 (v6 and earlier did not bring these signals out).

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
 * Step pulse widths on firmware build 380.xx and earlier as about 800 nanoseconds wide (just under 1 microsecond). SOme drivers are OK with this (like the on-board drivers), others are not. Additionally, depending on your external wiring, you may experience capacitive losses to the point where these pulses are effectively.
 * Step pulses have been stretched on builds 412.xx and above and are at least 2 microseconds wide.

