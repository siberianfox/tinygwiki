We get a lot of questions about what stepper motors and power supplies to use, so we've started this page. A lot of this information is scattered over the rest of the wiki, but it's nice to pull it together in one place.

# Stepper Motors

## Sources

Here are some sources that carry some good motors. Please feel free to add your favorites.
* [Alltronics](http://www.alltronics.com/cgi-bin/category/55) See the OSM's in particular. Often have some interesting surplus Lin motors. Have NEMA8's
* [Pololu](http://www.pololu.com/category/87/stepper-motors) Have some nice smaller motors as well, e.g. NEMA11
* [3DP2GO](http://www.3dp2go.com/stepper-motor-c-68.html) NEMA11, NEMA14, NEMA17, NEMA23 from China
* [Sparkfun](https://www.sparkfun.com/categories/178)
* [Phidgets](http://www.phidgets.com/products.php?category=23)
* [Oriental Motor Company](http://www.omc-stepperonline.com/)
* [Automation Technologies (Keling)](http://www.automationtechnologiesinc.com/) In particular they carry a 425 oz-in NEMA23 that only takes 2.8 amps. Monster.
* [Automation Direct](http://www.automationdirect.com/adc/Shopping/Catalog/Motion_Control/Stepper_Systems). Check out the STP-MTR-17060, a 125 oz-in NEMA17
* [MPJA](http://www.mpja.com/Stepper-Motors/products/101/ www.mpja.com/Stepper-Motors/products/101/) Surplus motors
* [All Electronics](http://www.allelectronics.com/make-a-store/category/400/Motors/1.html www.allelectronics.com/make-a-store/category/400/Motors/1.html) Surplus motors
* [Robot Digg](http://robotdigg.com/category/13/More-than-3D-Printing)

## References

More some useful information on steppers and wiring steppers:
* [http://reprap.org/wiki/Stepper_motor](http://reprap.org/wiki/Stepper_motor)

## General

### NEMA17 versus NEMA23
TinyG is capable of delivering 2 amps per winding, and 2.5 amps per winding with cooling. Given that, we have never found a NEMA17 that would not work with TinyG, and almost every NEMA23 we have tried will work if rated up to about 3 amps per winding (with the caveat that you might not get full power if it calls for more than 2.5 amps). We also routinely run NEMA34's, but not at full power and therefore not in high mechanical load situations.

### What's with the motor's rated voltage?
The question comes up - can I run a motor with a rated voltage of 4.2 volts (for example) with a 24 volt supply? Will this burn out the motor?

The motor's rated voltage is irrelevant and can be ignored. Any stepper motor rated at any voltage will work, regardless of the power supply voltage provided. The rated motor voltage just states what the max voltage would be if you applied a direct DC voltage to the winding and wanted to stay in the rated current of the motor. Since the stepper drivers regulate the current the voltage doesn’t matter.

In fact, the lower the rated voltage the better as this is a reflection of low impedance windings which will transfer power better and provide "snappier" operation than a similar motor with a higher voltage rating.

### Finding the Coil Pairs on a stepper motor

Bipolar motors have 4 wires (2 pairs), Unipolar motors typically have 6. Some other motors have 5, or 8, or whatever. 8 wire motors are usually wired as 2 sets of bipolar windings (i.e. essentially 2 bipolars wired together).

Bipolar motors (4 wire) or 8 wire motors (that you can therefore wire the phases in parallel to effectively make them a bipolar) are preferred over unipolar motors. This is because bipolars will have lower coil impedance and therefore more power transfer and torque.

Five (5) wire motors are usually in a "star" configuration that has a common ground and require a specialized driver. TinyG cannot drive 5 wire steppers.

Wire pairs are often color coded by convention. Common wire pairings are:
* Green goes with Black. Yellow is often used for the center tap of the Green/Black pair in a unipolar motor
* Red goes with Blue. White is often used for the center tap of the Red/Blue pair in a unipolar motor

E.g:

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

Here's a shortcut to finding wire pairs for a bipolar (4 wire) motor. Spin your stepper motor with your fingers. Depending on the size / holding torque this could be easy or pretty hard. All you really want from this is to get a feel how the motor spins without any of the wires connected to each other. Now that you know how hard it is to spin with your fingers, connect 2 wires together. Just pick any two. Try to spin the motor again. If it feels the same then more than likely these are NOT connected to the same coil. Disconnect these wires. Connect one of the other wires to one of the first wire pairs you tried. Try to spin the motors again. This should be much harder. If so, you have found your wire pairs. Tape these 2 together (not wired but just taped to group them). Tape the remaining 2 wires together as well.

Finding pairs in a unipolar motor is a bit more complicated, but not much. You want to find the outer taps of each coil. These are often color coded by convention (as above). Using a voltmeter to find the resistance across the outer pair. The resistance between the center tap an an outer tap will be 1/2 the resistance between the outer taps.

# Power Supplies

## References

## Sources

Here are some power supplies we like:
* [Meanwell NES-150-24](http://www.mouser.com/ProductDetail/Mean-Well/NES150-24/?qs=sGAEpiMZZMsPs3th5F8koDNPbuqd%252bfezne6r6bnnXjA%3d)
* Above model shows obsolete, replaced by: [LRS-150-24](https://www.mouser.com/ProductDetail/?qs=vDxCgdWo2h%252bym5KOpEI%252bpw%3d%3d) 
* [Meanwell NES-350-24](http://www.mouser.com/ProductDetail/Mean-Well/NES-350-24/?qs=%2fha2pyFaduhxfhzsenBkIkgMfhBr0hSVdTJWNZMLFL2wp6eI7VH7oQ%3d%3d)


_Note: Some power supplies have a 110v/220v switch on the side, and usually shipped in the 220v position. Be sure to switch the AC input from 220v to 110v if you are in the US or a 110v mains country. Most PSs will still work, but will audibly "click" and lose power if you don't set this correctly._

## General

TinyG v8 supports up to 2.5A per winding per motor and needs a 12v-30v motor power input. While 12 volt operation is possible and entirely fine, running with a 24v power supply will allow the motors to be more responsive and actually run cooler (ironically).

You might think that 4 motors at 2 amps per winding (about the top-end of NEMA17's) would require 4 motors X 2 windings X 2 amps = 16 amps to drive, but you'd be wrong. 4 to 4.5 amps or above will handle this as not all motors + phases are ever maxed out at the same time. We like to have at least 6 amps for NEMA17's and this is absolutely required for NEMA23s.
