If you have just received a TinyG this is the place to start.

## What is TinyG?
The TinyG project is a many-axis motion control system. It is designed for small CNC applications and other applications that require highly controllable motion control. TinyG is meant to be a complete embedded solution for small/medium motor control.&nbsp;Here are some of the main features: 

* Integrated motion control system with embedded microcontroller (Atmel ATxmega192) and 4 stepper motor drivers (TI DRV8811) integrated on a ~4 inch square board 
* Accepts Gcode from USB port and interprets it locally on the board 
* 6-axis control (XYZ + ABC rotary axes) 
* Acceleration planning performed using constant jerk motion equations (3rd order S curves) for very smooth and fast motion transitions for lines and arcs 
* Very smooth step pulse generation using phase-optimized, smart oversampling, fractional step DDA running at 50 Khz with very low jitter (&lt;&lt;1uSec) 
* Networkable via RS485 to support motion peripherals and for networking mutliple boards for multi-axis systems and for really interesting projects (up to 1000 stepper axes) 
* Stepper drivers handle 2.5 amps per winding which will handle most motors up thru NEMA23 and some NEMA34 motors. 
* Microstepping up to 1/8 (optimized DDA makes this smoother than many 1/16 implementations)