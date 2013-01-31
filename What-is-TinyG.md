The TinyG project is a multi-axis motion control system. It is designed for small CNC applications and other applications that require highly controllable motion control. TinyG is meant to be a complete embedded solution for small/medium motor control. Here are some of the main features of the v7 hardware.

* Integrated motion control system with embedded microcontroller (Atmel ATxmega192) 
* 4 stepper motor drivers (TI DRV8818) integrated on a ~4 inch square board 
* Stepper drivers handle 2.5 amps per winding which will handle most motors up thru NEMA23 and some NEMA34 motors 
* Accepts Gcode from USB port and interprets it locally on the board 
* 6-axis control (XYZ + ABC rotary axes) maps to any 4 motors
* Constant jerk acceleration planning (3rd order S curves) for smooth and fast motion transitions
* Very smooth step pulse generation using phase-optimized fractional-step DDA running at 50 Khz with very low jitter
* Networkable via SPI to support off-board devices and for networking multiple boards into multi-axis systems
* Microstepping up to 1/8 (optimized DDA makes this smoother than many 1/16 implementations)

## Gcode Support
TinyG implements the NIST RS274v3/ngc dialect of Gcode including the following functions. See [TinyG Gcode Support](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support) for more details 

* G0 Rapid linear motion (traverse)
* G1 Linear motion at feed rate
* G2, G3 Clockwise / counterclockwise arc at feed rate
* G4 Dwell
* G17, G18, G19 Select plane: XY plane {G17}, XZ plane {G18}, YZ plane {G19}
* G20, G21 Length units: inches {G20}, millimeters {G21}<br> 
* G28 Return to home
* G28.1 Homing cycle (reference axes to limit switches)
* G53 Move in absolute coordinates<br> 
* G54-G59 Work coordinate systems 1-6
* G61, G61.1, G64 Set path control mode (group 13)<br> 
* G80 Cancel motion mode<br> 
* G90, G91 Set distance mode; absolute {G90}, incremental {G91}<br> 
* G92, G92.1, G92.2, G92.3 Coordinate System Offsets - full support<br> 
* G93, G94 Set feed rate mode: inverse time mode {93}, units per minute mode {G94}<br> 
* M0, M1, M60, Program stop, optional program stop<br> 
* M2, M30 Program end<br> 
* M3, M4 Turn spindle clockwise / counterclockwise<br> 
* M5 Stop spindle turning<br> 
* M7, M8 Turn on coolant bit
* M9 Turn off coolant bit

## Additional CNC features
* Feedhold and cycle start with full planning (pause and resume motion) 
* Homing cycle 
* Low-power idle mode 
* Status reports for DRO style readouts 
* Independent, per-axis control of jerk, maximum seek and feed rate and other parameters to enable fine tuning a wide variety of machine types 
* Axis/motor mapping to support dual gantry and other configurations (e.g. XYYZ, XYZA, XYZC...) 
* Non-cartesian inverse kinematics are supported at the C code level

## Programming support
TinyG codebase is written in well commented C, open source (GPL), and was originally forked from grbl. A special thank you to Simen Svale Skogsrud, Sonny Jeon and Jens Geisler for making grbl available and continuing the good work.<br> 

* Code is written in well commented C. Complies under avrGCC 
* Entire code base is open source, available at&nbsp;[https://github.com/synthetos github.com/synthetos] 
* Forum is available at&nbsp;[http://www.synthetos.com/forums/ www.synthetos.com/forums/]

## Hardware and other technical details
* GPIO ports provide 8 inputs for limit / homing switches, plus 4 general purpose input/output ports for spindle control and other uses 
* PDI programming connector (3x2) 
* JTAG connector (2x5 variety) 
* USB via FTDI - runs 115,200 baud by default 
* Atmel ATxmega192A3 running at 32 Mhz 
* Direct TTL serial inout for drive from Arduino or similar 
* See [[Projects:TinyG-Hardware-Info:|TinyG Hardware Info]]&nbsp;for more details

TinyG is up to v6 PRODUCTION boards, which are reflected in the TinyG hardware specs.<br> We also have grblshield available, which is a 3 axis "no CPU" version of TinyG with 3 axes of stepper controller that plugs onto an Arduino as a shield.<br> [http://www.synthetos.com/wiki/index.php?title=Projects:TinyG Back to TinyG Home] 

## TinyG and grbl are related but not the same
TinyG was forked from grbl in early 2010 as the base for building a 6 axis controller with jerk controlled acceleration planning. So some things in TinyG are the same as grbl and many are different. It's worth noting that we (Synthetos) are active in each project. We offer both TinyG and the grblshield as we recognize that they serve different needs and user bases. 

Basic similarities between grbl+grblshield and tinyg: 

* Both basically support the same set of Gcodes - with some minor differences. refs:https://github.com/grbl/grbl/wiki, [[Projects:TinyG-Gcode-Support|TinyG Gcode Support]] <br> 

* Both adhere as closely as possible to the NIST Gcode spec. refs: http://www.isd.mel.nist.gov/documents/kramer/RS274NGC_3.ps http://technisoftdirect.com/catalog/download/RS274NGC_3.pdf 

* Both implement enhanced CNC features such as homing cycles, feedhold (!) and restart (~ , aka cycle start) and software reset (control-x) 
* Both use Texas Instruments DRV8811 stepper controller chips 
* Both are written in C, GNU GPL open source licensed 
* Both projects are currently in beta

Some fundamental differences are: 
* grbl is an XYZ 3 axis controller (i.e. a cartesian robot). TinyG is a 6 axis controller that runs XYZ and also ABC rotational axes. Many of the differences are attributable to this fact. See the NIST spec (above) as to how rotary axes work, or refer to the discussion on the TinyG wiki on rotational axes: http://www.synthetos.com/wiki/index.php?title=TinyG:Configuring#Rotational_Axis_Settings_and_Modes 

* TinyG has 4 motors, grblshield has 3. It is possible (and common) for grbl to run dual gantry configs - like a dual Y by using 2 stepper drivers attached to the Y step and dir lines. This can present some challenges in homing, but in general this works pretty well. Grblshield only supports 3 axes, and the motors are tied to the X, Y and Z axes. In TinyG the motors are configurable (mappable) to an axis. If you want 4 X axes, map motors 1-4 to X and have a great day. Generally people map the 4th motor to Y the or A axis.

* TinyG runs 3rd order, constant jerk acceleration profiles, grbl runs 2nd order constant acceleration profiles. What does this mean? In grbl the velocity profile during acceleration and deceleration looks like a pure trapezoid in time. For example the move starts at zero velocity, then velocity ramps in a straight line to the target velocity, then decelerates in a straight line back to zero. In TinyG the velocity profile is an S curve that ramps to the target velocity during acceleration and in reverse during the deceleration phase. The means that you can run to motors harder in transition and hence operate at faster accelerations and decelerations. It also means there are fewer machine resonances excited (that cause chatter and other problems) as the jerk term is controlled. Jerk is a measure of the impact a machine is hit with during a velocity change. See: http://www.youtube.com/watch?v=pCC1GXnYfFI 

* All settings on TinyG are configurable on a per axis or per motor basis. In grbl one parameter applies to all axes (XYZ) - with the exception of steps per mm and polarity - which must be settable on a per axis/per motor basis. A subtlety is that TinyG treats axes and motors as different objects that are configured independently and them mapped together. grbl treats them as the same object - which is fine given its XYZ mission. TinyG does not have that luxury as it needs to support ABC axes which need very different configurations that X Y and Z (to start with, they are in degrees, not linear units...). Independent control also becomes an issue if the dynamics of the Z axis are significantly different than X and Y, like on Shapeoko where Z is a screw axis and X and Y are belts. ref: [[TinyG:Configuring|Configuring TinyG]] <br>

* TinyG is designed to be run "from the command line". Since there are more configuration settings to worry about it offers a set of mnemonics for configuration, machine state and configuration status inquiries. Full friendly names can also be used (if you don't mind typing more). It also offers real-time status reports (DRO-type output). grbl also has real-time status reports in edge right now. TinyG has a set of help screens available from the command prompt. grbl offers some of these features but in general is more "silent". There are plans to add new features to grbl in future releases. We are coordinating with the grbl project to keep these commands as aligned as possible. ref: [[Using TinyG]]<br> 

* In addition to command line operation, TinyG implements a JSON interface. This gets pretty arcane, but is useful if you are writing a controller for TinyG. The JSON interface is really a REST interface that implements the objects and verbs in the system. Its different from what people normally think of as REST in that the transport is USB serial, not HTTP. ref: [[Projects:TinyG-JSON|JSON commands]]<br>

* TinyG implements a set of embedded self tests to verify proper system operation and assist in setup

* Different embedded processors:
 * The processor chip for grbl is an Atmel ATmega328p that runs on the Arduino *hardware*. Note that once you program grbl onto it it is no longer an "Arduino" as you have taken over the chip and it it will not run Processing anymore (until you re-flash it - then it's no longer grbl) The 328p runs at 16 Mhz, has 32K FLASH memory (program memory) and 2K of RAM ref: http://www.atmel.com/Images/doc8161.pdf). 
 * The processor chip on tinyg is an Atmel Xmega192A3 that runs at 32 Mhz, has 192K FLASH and 16K RAM. ref: http://www.atmel.com/Images/doc8068.pdf) 
 * The difference in processors means that tinyg can do more computation and have larger firmware and RAM usage. It also means that grbl is programmable using a garden variety Atmel ISP programmer, and TinyG requires a programmer that implements the newer PDI programming protocol - such as the Atmel ISP MKII programmer (at $35 from Mouser electronics. This programmer also works fine on the older protocols). We implemented a boot loader on tinyg at one point (xboot project) but found AVRdude to be so unreliable that we did not release it. We also offer a firmware upgrade service for the cost of postage back and forth, but really if you are interested in keeping up with the project you should get a capable programmer. 
 * The processor difference also means that grbl generates step pulses at a rate of 30Khz, TinyG at 50Khz. 

There are a number of other differences such as communications and various system settings, but this really gets lost in the weeds.