If you have just received a TinyG v8 this is the place to start. The board revision is printed on the silkscreen at the edge of the board. If you do not have a v8 board you may want one of these links instead:

* [TinyG version 7](https://github.com/synthetos/TinyG/wiki/TinyG-Start-v7/)
* [TinyG version 6 or earlier](https://github.com/synthetos/TinyG/wiki/TinyG-Start-v6-and-Earlier)

Here's some background if you want to know [what TinyG is](https://github.com/synthetos/TinyG/wiki/What-is-TinyG)

## Getting Started with TinyG - What You Need
The getting started page is your first place to go to figure out what you need to get to get your TinyG up and running quickly. However before we dive into hooking up wires, configuring, and running Gcode files the image below is a "diagram" of the important sections / parts of you TinyG board. 

![TinyG v8 Diagram](http://farm4.staticflickr.com/3668/9642075945_e073444e0a_b.jpg)

To highlight a few things in the above diagram:

* The **MOST IMPORTANT** thing to do is to wire your power input correctly. The input will take up to 30 volts, but most people use 24, 19 or 12 volt power supplies. Double check that the polarity for the GND and Vmot are correct BEFORE plugging in your TinyG board. If you have ANY doubt about the power supply output please check it with a volt meter first.
* Power output for the PC fan is an important to make sure you have right! Failure to set your fan jumper may result in providing 24v to a 12v fan and maybe blowing it up. See more about this below. 
* All logic input voltages are limited to 3.3v MAX! 
* Start with the current setting trim pots in the middle, 6:00, straight-up-and-down position, and DO NOT over torque the trim pots!

### Parts You Will Need
Here is what you are going to need in order to use TinyG: 

* [A TinyG board](http://synthetos.myshopify.com/products/tinyg)
* Power supply - Anything between 12VDC and 30VDC, typically 24 volts DC at 4 to 15 amps. 
* 1 - 4 stepper motors - typically NEMA17 or NEMA23 up to 2.5 amps per winding (See Notes)
* Fan - A 12VDC or 24VDC fan is recommended, especially if the board is in an enclosure 
* Programmer (Optional.. If you want firmware updates..)
* Case (Optional) 

Notes: We have never found a NEMA17 that would not work with TinyG, and almost every NEMA23 we have tried will work if rated up to about 3 amps per winding. We also routinely run NEMA34's, but not in high mechanical load situations. The motor's rated voltage is irrelevant and can be ignored. When running NEMA23's (or above) we recommend fan cooling. Note that most of the heat comes off the bottom copper, so be sure to provide air circulation for the bottom as well as the top.

#### TinyG Board
You can get the TinyG controller board fully assembled from the [Synthetos Store](https://www.synthetos.com/webstore/index.php/assembled-electronics/tiny-g.html)

TinyG v8's are "blue" boards labeled as TinyGv8. These use the DRV8818 driver chips. TI also makes other driver chips in this same family, but the 8818's have the lowest ON resistance therefore the best current handling. 

#### Power Supply
TinyG v8 supports up to 2.5A per winding per motor and needs a 12v-30v motor power input. While 12 volt operation is possible and entirely fine, running with a 24v power supply will allow the motors to be more responsive and actually run cooler (ironically).

You might think that 4 motors at 2 amps per winding (about the top-end of NEMA17's) would require 4 motors X 2 windings X 2 amps = 16 amps to drive, but you'd be wrong. 4.5 amps or above will handle this fine as not all motors + phases are ever maxed out at the same time. We recommend 6 amps or above if using NEMA23s.

Here's a really beefy power supply we like: [Meanwell NES-350-24](http://www.jameco.com/webapp/wcs/stores/servlet/ProductDisplay?langId=-1&storeId=10001&catalogId=10001&pa=2149600&productId=2149600&keyCode=WSF&CID=GOOG&gclid=CKGp2eipk7UCFWGnPAod9jMAKA)<br>You can usually hunt around and find this for < $50. They also make lower amperage supplies that are cheaper. 

PS Caveat: Be sure to switch the AC input from 240v to 120v if you are in the US or a 120v mains country. Most PSs will still work, but will audibly "click" and lose power if you don't set this correctly.

#### Stepper Motors
TinyG will work with bipolar and unipolar stepper motors up to 2.5 amps per winding. This covers most NEMA23 and smaller motors. It will not work with motors wired as 5 wire "star configurations", so avoid these. Some of out favorite sources for stepper motors are: 

* [Alltronics](http://www.alltronics.com/cgi-bin/category/55 www.alltronics.com/cgi-bin/category/55)
* [MPJA](http://www.mpja.com/Stepper-Motors/products/101/ www.mpja.com/Stepper-Motors/products/101/)
* [All Electronics](http://www.allelectronics.com/make-a-store/category/400/Motors/1.html www.allelectronics.com/make-a-store/category/400/Motors/1.html)
* [Sparkfun](https://www.sparkfun.com/categories/178)
* [Phidgets](http://www.phidgets.com/products.php?category=23)

#### Heat Sinks
The TI drivers on the TinyG are incredibly robust and will shut down in case of over-current instead of blowing up (unlike some other brands that shall remain nameless). But you don't want to go into thermal shutdown as it will will ruin your job even though the board is still OK. Thermal shutdown is evidenced by anything from a slow on-off cycling of the motor power, getting shorter as the current raises, to a stutter in extreme cases. The chips will be quite hot to the touch.

The main heatsinking provided for TinyG is the expanse of 2 oz. copper on the bottom and top of the board. You can see this by inspection. This is usually sufficient for NEMA17 installations. If you experience thermal shutdown we recommend fan cooling. Fan cooling is the most effective way to cool and far more effective than heatsinking.

#### Cooling Fan
The TinyGv8's come equipped with a 3-pin fan connector that can be used to power a standard 12vdc PC fan, or a 24vdc fan - depending on a jumper setting and your board voltage (Vmot). Please read the silkscreen designators as below - sorry it's not more straightforward:

* The "+12v" jumper position connects the fan header to the LM7812 12 volt regulator. This should be used if you are running a 12 volt fan and your board power (Vmot) is > 14 volts. This is the default jumper position and your v8 should have been shipped with the jumper in this position. 
* The "+Vmot" jumper position connects the fan header directly to the board power (Vmot). In this case your fan voltage needs to be the same as your Motor voltage, be it 12 volts, 24 volts, or anything else.<br>
**Use this side with caution as applying 24 volts to some 12 volt fans will burn them out.**

#### Flashing TinyG (Firmware Updates)
The TinyG code is available at the [Synthetos Github](https://github.com/synthetos/TinyG TinyG github) but most people will not need to use this as TinyG comes loaded with the latest firmware in the Master branch. 

If you want to re-program (flash) TinyG you will either need to use the boot loader on the TinyG, or use a PDI capable programmer to talk to the Atmel xmega chip. See the [Boot Loader Page](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader) for that option.

If you want to program 'down to the metal" we recommend the [Atmel AVRISP MKII programmer](http://www.mouser.com/Search/ProductDetail.aspx?qs=sGAEpiMZZMsaJrqdZ%252b6EWyua%252bG%2FwcOQP26MNKN%252bCIDE%3D) which is available from Mouser Electronics for roughly $35.00. See [TinyG Programming Page](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer) for instructions.

### Connecting TinyG
At this point you can move on to [Connecting TinyG](https://github.com/synthetos/TinyG/wiki/Connecting-TinyG)