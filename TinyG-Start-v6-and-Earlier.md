This page describes starting up with a TinyG hardware version 6 or earlier board. The hardware version is printed on the board silkscreen. Here the links for other versions:
* [TinyG version 7](https://github.com/synthetos/TinyG/wiki/TinyG-Start)

The main changes from earlier hardware versions and version 7 are listed and explained below. All references to v6 refer to hardware version 6 and earlier, unless otherwise noted. 
![TinyG v6](http://farm7.staticflickr.com/6161/6138113691_d2a77b606c_b.jpg)

* If you are using v6 hardware with 0.95 (or later) firmware you must set the hardware version as so `$hv=6`  You should only need to do this once.

* v6 requires motor voltage and +5volts DC to be provided on the power terminal wired as below. Be careful to observe correct voltages and polarities. Most 12v/5v dual voltage supplies (e.g. converted PC ATX supplies) observe red for 5v and yellow for 12v, but not all! We have seen this reversed. Always double check voltages and polarities before connecting power to a board. 
![TinyG V6 Power Connections](http://farm7.staticflickr.com/6178/6253402559_b6a5a946d9_b.jpg)

* v6 requires 0.156" quick release terminals for the motors. Housings and crimp pins are below. Motor pinouts are otherwise the same as v7. See [Wire Your Motors](https://github.com/synthetos/TinyG/wiki/Connecting-TinyG#wire-your-motors) for wiring instructions.
 * [Molex 09-50-3041](http://www.mouser.com/ProductDetail/Molex/09-50-3041/?qs=%2fha2pyFaduiq3dSmG9JEt1yANyoojHtFJi0SKaVS0vw%3d) - 4 conductor 0.156" housing
 * [Molex 08-50-0134](http://www.mouser.com/Search/Refine.aspx?Keyword=08-50-0134) - Crimp terminals for above - 4 required for each motor/housing
![TinyG V6 Power terminals](http://farm7.staticflickr.com/6178/6205245951_058c7509fd.jpg)

* The v6 stepper motor current setting potentiometers are 3mm surface mount potentiometers. These have a 270 degree rotation and should not be forced beyond this. Be careful, they are sensitive. If they are forced beyond their limit the axis will likely stop working. If your board has this problem please contact us and we can send some pots you can solder, or arrange to rework your board if you are not comfortable doing this yourself.

* Spindle/coolant outputs and switch inputs are illustrated here. Be advised that the outputs are 3.3 volts, and inputs CANNOT EXCEED 3.3v (or you will damage the board).
![TinyG GPIO](http://farm9.staticflickr.com/8239/8580377050_0148eea297_c.jpg)

Output pin mapping are
 * Out0 - Spindle ON/OFF
 * Out1 - Spindle direction
 * Out2 - Spindle PWM
 * Out3 - Coolant ON/OFF