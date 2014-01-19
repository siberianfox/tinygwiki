We get a lot of questions about what stepper motors and power supplies to use, so we've started this page. As of today (1/19/14) it's pretty sketchy but we'll fill this in.

# Stepper Motors

## Sources

Here are some sources that carry some good motors. Please feel free to add your favorites.
* [Alltronics](http://www.alltronics.com/cgi-bin/category/55) See the OSM's in particular. Often have some interesting surplus Lin motors. Have NEMA8's
* [Pololu](http://www.pololu.com/category/87/stepper-motors) Have some nice smaller motors as well, e.g. NEMA11
* [Automation Technologies (Keling)](http://www.automationtechnologiesinc.com/) In particular they carry a 425 oz-in NEMA23 that only takes 2.8 amps. Monster.
* [Automation Direct](http://www.automationdirect.com/adc/Shopping/Catalog/Motion_Control/Stepper_Systems). Check out the STP-MTR-17060, a 125 oz-in NEMA17


## References

More some useful information on steppers and wiring steppers:
* [http://reprap.org/wiki/Stepper_motor](http://reprap.org/wiki/Stepper_motor)

## General

### Finding the Coil Pairs on a stepper motor

Bipolar motors have 4 wires (2 pairs), Unipolar motors typically have 6. Some other motors have 5, or 8, or whatever. 8 wire motors are usually wired as 2 sets of bipolar windings (i.e. essentially 2 bipolars wired together). 5 wire motors are usually in a "star" configuration that has a common ground and require a specialized driver. TinyG cannot drive 5 wire steppers.

The following color code is typical for many motors

<pre>
	Color    | Bipolar      | Unipolar      | Notes
	---------|--------------|---------------|--------
	Green    |  Winding A1  | Winding A1    |
	Yellow   |  (none)      | Center tap A  |
	Black    |  Winding A2  | Winding A2    |
	Red      |  Winding B1  | Winding B1    |
	White    |  (none)      | Center tap B  |
	Blue     |  Winding B2  | Winding B2    |

</pre>

Use your volt meter to verify that green and black connect together, and red and blue connect together, and that they don't connect to the other pair. Typical DC resistance across a winding is about 1 to 5 ohms. If you have a Unipolar motor you can just leave the center taps disconnected.

If this doesn't work here's a shortcut to finding wire pairs for a bipolar (4 wire) motor.

Spin your stepper motor with your fingers. Depending on the size / holding torque this could be easy or pretty hard. All you really want from this is to get a feel how the motor spins without any of the wires connected to each other. Now that you know how hard it is to spin with your fingers, connect 2 wires together. Just pick any two. Try to spin the motor again. If it feels the same then more than likely these are NOT connected to the same coil. Disconnect these wires. Connect one of the other wires to one of the first wire pairs you tried. Try to spin the motors again. This should be much harder. If so, you have found your wire pairs. Tape these 2 together (not wired but just taped to group them). Tape the remaining 2 wires together as well. 

Unipolars are a bit more complicated, but not much. To do a series wiring find the outer taps of each coil. These are often color coded by convention (see below). Using a volt meter to find the resistance across the outer pair. The resistance between the center tap an an outer tap will be 1/2 the resistance between the outer taps. 

# Power Supplies

## References

## Sources

## General

