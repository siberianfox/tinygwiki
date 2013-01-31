If you have just received a TinyG this is the place to start.

## What is TinyG?
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

See [What Is TinyG](https://github.com/synthetos/TinyG/wiki/What-is-TinyG) ror more info.