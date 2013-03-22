The TinyG project is a multi-axis motion control system. It is designed for CNC applications and other applications that require highly precise motion control. TinyG is meant to be a complete embedded solution for small/medium motor control. Here are some of the main features of the v7 hardware.

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
* G10 L2 Set coordinate system offsets 
* G17, G18, G19 Select plane: G17=XY plane, G18=XZ plane, G19=YZ plane
* G20, G21 Length units: G20=inches, G21=millimeters
* G28, G28.1 Return to home, set home point
* G28.2 Initiate machine homing cycle (reference axes to limit switches)
* G30, G30.1 Return to home, set home point
* G53 Move in absolute (machine) coordinates
* G54-G59 Select work coordinate systems 1-6, respectively
* G61, G61.1, G64 Set path control mode
* G80 Cancel motion mode
* G90, G91 Set distance mode; G90=absolute mode, G91=incremental mode 
* G92, G92.1, G92.2, G92.3 Coordinate System Offsets - full support
* G93, G94 Set feed rate mode: G93=inverse time mode, G94=units per minute mode 
* M0, M1, M60, Program stop, optional program stop
* M2, M30 Program end
* M3, M4 Turn spindle clockwise / counterclockwise
* M5 Stop spindle turning
* M7, M8 Turn on coolant
* M9 Turn off coolant

## Additional CNC features
* Feedhold and cycle start with full planning (pause and resume motion) 
* Homing cycle 
* Low-power idle mode 
* Status reports for DRO style readouts 
* Independent, per-axis control of jerk, maximum seek and feed rate and other parameters to enable fine tuning a wide variety of machine types 
* Axis/motor mapping to support dual gantry and other configurations (e.g. XYYZ, XYZA, XYZC...) 
* Non-cartesian inverse kinematics are supported at the C code level

## Programming support
TinyG codebase is written in commented C, open source (GPL), and was orginally forked from grbl. A special thank you to Simen Svale Skogsrud and Sonny Jeon for making grbl available and continuing the good work 

* Code is written in well commented C. Complies under avrGCC in AVR Studio4, AtmelStudio6 and native AVRGCC environments
* Entire code base is open source, available at the [Synthetos Github](https://github.com/synthetos/tinyg)
* See [TinyG Support Forum](https://www.synthetos.com/forum/tinyg/) if you need help 

## Hardware and other technical details
TinyG is up to v7 PRODUCTION boards, which are reflected in the TinyG hardware specs. We also have grblshield available, which is a 3 axis "no CPU" version of TinyG with 3 axes of stepper controller that plugs onto an Arduino as a shield. TinyG hardware has:

* Atmel ATxmega192A3 running at 32 Mhz 
* USB via FTDI - runs 115,200 baud by default 
* Direct TTL serial inout for drive from Arduino or similar 
* GPIO ports provide 8 inputs for limit / homing switches, plus 4 general purpose input/output ports for spindle control and other uses 
* PDI programming connector (3x2) 

## TinyG and grbl are related but not the same
People ask what's the difference. Here's an attempt to explain that.

TinyG was forked from grbl in early 2010 as the base for building a 6 axis controller with jerk controlled acceleration planning. So some things in TinyG work the same as grbl but many are different. It's worth noting that we (Synthetos) are active in each project. We offer both TinyG and the grblshield as we recognize that they serve different needs and user bases. When we talk about grbl hardware features, below, we are referring to grbl+grblShield.

Basic similarities between grbl and tinyg: 

* Both basically support the same set of [Gcode](https://github.com/synthetos/TinyG/wiki/TinyG-Gcode-Support) - with some minor differences

* Both adhere as closely as possible to the NIST Gcode spec. and use the LinuxCNC Gcode spec for additional guidance. Refs:<br>
[Kramer's NIST RS274NGCv3 Gcode Specification](http://technisoftdirect.com/catalog/download/RS274NGC_3.pdf)<br>
[LinuxCNC Gcode Specification](http://www.linuxcnc.org/docs/2.4/html/gcode_main.html)<br>

* Both implement enhanced CNC features such as homing cycles, feedhold (!) and restart (~ , aka cycle start) and software reset (control-x) 
* Both use Texas Instruments DRV8818 stepper controller chips (as of grblShield v4 and TinyG v7)
* Both are written in C, GNU GPL open source licensed
* Both projects are currently widely deployed but are technically still in beta - with production for both expected "any day now" - realistically sometime in 2Q2013, if we both keep on schedule.

Some fundamental differences are: 
* grbl is an XYZ 3 axis controller (i.e. a cartesian robot). TinyG is a 6 axis controller that runs XYZ and also ABC rotational axes. Many of the differences are attributable to this fact. See the [NIST spec](http://technisoftdirect.com/catalog/download/RS274NGC_3.pdf) as to how rotary axes work.

* TinyG has 4 motors, grblshield has 3. It is possible (and common) for grbl to run dual gantry configs - like a dual Y by using 2 stepper drivers attached to the Y step and dir lines. This can present some challenges in homing, but in general this works pretty well. Grblshield only supports 3 axes, and the motors are tied to the X, Y and Z axes. In TinyG the motors are configurable (mappable) to an axis. If you want 4 X axes, map motors 1-4 to X and have a great day. Generally people map the 4th motor to Y the or A axis.

* TinyG runs 3rd order, constant jerk acceleration profiles, grbl runs 2nd order constant acceleration profiles. What does this mean? In grbl the velocity profile during acceleration and deceleration looks like a pure trapezoid in time. For example the move starts at zero velocity, then velocity ramps in a straight line to the target velocity, then decelerates in a straight line back to zero. In TinyG the velocity profile is an S curve that ramps to the target velocity during acceleration and in reverse during the deceleration phase. The means that you can run to motors harder in transition and hence operate at faster accelerations and decelerations. It also means there are fewer machine resonances excited (that cause chatter and other problems) as the jerk term is controlled. Jerk is a measure of the impact a machine is hit with during a velocity change. See: [TinyG driving an Ultimaker](http://www.youtube.com/watch?v=Om0wTqFA-Dw) and [driving a Shapeoko](http://www.youtube.com/watch?v=pCC1GXnYfFI). The machines are not fastened to the table and don't jump around because of the jerk control.

* TinyG has a lot more configuration parameters than grbl. This is both good and bad. There are more axes on TinyG and all settings on TinyG are configurable on a per axis or per motor basis. _(In grbl one parameter used to apply to all axes (XYZ), but as of 0.9 grbl has independent axis acceleration parameters)_. 

* TinyG also separates the motor configs from the axis configs to enable non-cartesian machines. But this means more parameters as they are configured independently and them mapped together. grbl treats them as the same object - which is fine given its XYZ mission. TinyG does not have that luxury as it needs to support ABC axes which need very different configurations that X Y and Z (to start with, they are in degrees, not linear units...). Independent control also becomes an issue if the dynamics of the Z axis are significantly different than X and Y, like on Shapeoko where Z is a screw axis and X and Y are belts. 

* TinyG and grbl are both designed to be run "from the command line". Since there are more configuration settings to worry about, TinyG offers a set of mnemonics for configuration, machine state and configuration status inquiries. 

* TinyG offers real-time status reports (DRO-type output).

* TinyG has a set of help screens available from the command prompt. grbl offers some of these features but in general is more "silent". There are plans to add new features to grbl in future releases.

* In addition to command line operation, TinyG implements a [JSON interface](https://github.com/synthetos/TinyG/wiki/JSON-Operation). This gets pretty arcane, but is useful if you are writing a controller for TinyG. The JSON interface is really a REST interface that treats the TinyG system as a collection of resources (in the REST sense of that word). It's different from what people normally think of as REST in that the transport is USB serial, not HTTP.

* TinyG implements a set of embedded self tests to verify proper system operation and assist in setup

* Different embedded processors:
 * The processor chip for grbl is an [Atmel ATmega328p](http://www.atmel.com/Images/doc8161.pdf) that runs on the Arduino *hardware*. Note that once you program grbl onto it it is no longer an "Arduino" as you have taken over the chip and it it will not run Processing anymore (until you re-flash it - then it's no longer grbl) The 328p runs at 16 Mhz, has 32K FLASH memory (program memory) and 2K of RAM. 
 * The processor chip on tinyg is an [Atmel Xmega192A3](http://www.atmel.com/Images/doc8068.pdf) that runs at 32 Mhz, has 192K FLASH and 16K RAM.
 * The difference in processors means that tinyg can do more computation and have larger firmware and RAM usage. It also means that grbl is programmable using a garden variety Atmel ISP programmer, and TinyG requires a programmer that implements the newer PDI programming protocol - such as the Atmel ISP MKII programmer _(We are working on a boot loader)_. We also offer a firmware upgrade service for the cost of postage back and forth, but really if you are interested in keeping up with the project you should get a capable programmer. 
 * The processor difference also means that grbl generates step pulses at a rate of 30Khz, TinyG at 50Khz. 

There are a number of other differences such as communications and various system settings, but this really gets lost in the weeds.