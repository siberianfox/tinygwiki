If you have a TinyG v7 this is the place to start. The board revision is printed on the silkscreen at the edge of the board. 

If you have a v8, please use this page instead [Getting Started with TinyG v8](https://github.com/synthetos/TinyG/wiki/TinyG-Start)

## Getting Started with TinyG - What You Need
The getting started page is your first place to go to figure out what you need to get to get your TinyG up and running quickly. However before we dive into hooking up wires and downloading software the image below is a "diagram" of the important sections / parts of you TinyG board. 

![TinyG V7 Diagram](http://farm8.staticflickr.com/7407/8728450697_915a1cdf24_c.jpg)

To highlight a few things in the above diagram:

* The **MOST IMPORTANT** thing to do is to wire your power input correctly. Double check your polarity BEFORE plugging in your TinyG board.
* Power output for the PC fan is an important to make sure you have right! Failure to set your fan jumper may result in blowing up your PC fan by providing 24v to a 12v fan. See more about this below. 
* All input voltages are limited to 3.3v MAX! 
* DO NOT over torque the current trim pots!

### Parts You Will Need
Here is what you are going to need in order to use TinyG: 

* [A TinyG board](http://synthetos.myshopify.com/products/tinyg)
* Power supply - Anything between 12VDC and 30VDC, typically 24 volts DC at 4 to 15 amps. 
* 1 - 4 stepper motors - typically NEMA17 or NEMA23 up to 2.5 amps per winding
* Fan - A 12VDC or 24VDC fan is recommended, especially if the board is in an enclosure 
* Programmer (Optional.. If you want firmware updates..)
* Case (Optional) 

Please note: Stepper Motor Connectors are required for v6 and earlier, but are no longer needed with v7s!

#### TinyG Board
You can get the TinyG controller board fully assembled from the [Synthetos Store](https://www.synthetos.com/webstore/index.php/assembled-electronics/tiny-g.html)

TinyG v7's are "blue" boards labeled as TinyGv7 - 23. These use the DRV8818 driver chips as opposed to the DRV8811 driver chips. The 8818 drivers have a lower ON resistance in the FET output stages and therefore don't get as warm as the 8811's are are capable of driving a bit more current. The '23' designation refers to NEMA23 motors, as the blue boards will easily drive most NEMA23 motors. 

#### Power Supply
TinyG v7 (either green or blue) supports up to 2.5A per winding per motor and needs a 12v-30v motor power input. While 12 volt operation is possible and entirely fine, running with a 24v power supply will allow the motors to be more responsive and actually run cooler (ironically). We recommand fan cooling the board if you use a voltage over 12 volts. 

Here's one we like: [Meanwell NES-350-24](http://www.mouser.com/ProductDetail/Mean-Well/NES-350-24/?qs=%2fha2pyFaduhxfhzsenBkIkgMfhBr0hSVdTJWNZMLFL2wp6eI7VH7oQ%3d%3d)<br>You can usually hunt around and find this for < $50. They also make lower amperage supplies that are cheaper.

#### Stepper Motors
TinyG will work with bipolar and unipolar stepper motors up to 2.5 amps per winding. This covers most NEMA23 and smaller motors. It will not work with motors wired as 5 wire "star configurations", so avoid these. Some of out favorite sources for stepper motors are: 

* [Alltronics](http://www.alltronics.com/cgi-bin/category/55 www.alltronics.com/cgi-bin/category/55)
* [MPJA](http://www.mpja.com/Stepper-Motors/products/101/ www.mpja.com/Stepper-Motors/products/101/)
* [All Electronics](http://www.allelectronics.com/make-a-store/category/400/Motors/1.html www.allelectronics.com/make-a-store/category/400/Motors/1.html)
* [Sparkfun](https://www.sparkfun.com/categories/178)
* [Phidgets](http://www.phidgets.com/products.php?category=23)

#### Heat Sinks
The main heatsinking provided for TinyG is the expanse of 2 oz. copper on the bottom and top of the board. You can see this by inspection. TinyG comes with 4 additional heat sinks. Using them is optional. If you are driving NEMA17 or light-duty NEMA23 motors you will probably not need them and should leave them off the board. If you experience thermal shutdown - stuttering, on/off cycling - we recommend fan cooling, and possibly also using the heatsinks. Fan cooling is far more effective and should be used first. To apply the heatsinks simply peel the back sticker off and place the heat sink over each DRV8818 stepper driver chip. Be careful not to contact any of the chip leads, resistors, or any other electrical parts.

#### Cooling Fan
The TinyGv7's come equipped with a 3-pin fan connector that can be used to power a standard 12vdc PC fan, or a 24vdc fan - depending on a jumper setting and your board voltage (Vmot). Please read the silkscreen designators as below - sorry it's not more straightforward:

* The "24v" jumper position connects the fan header to the LM7812 12 volt regulator. This should be used if you are running a 12 volt fan and your board power (Vmot) is > 14 volts. This is the default location and your v7 should have been shipped with the jumper in this position. 
* The "12v" jumper position connects the fan header directly to the board power (Vmot). In this case your fan voltage needs to be the same as your Motor voltage, be it 12 volts, 24 volts, or anything else.<br>
**Use this side with caution as applying 24 volts to some 12 volt fans will burn them out.**

#### Programmer
The TinyG code is available at the [Synthetos Github](https://github.com/synthetos/TinyG TinyG github) but most people will not need to use this as TinyG comes loaded with the latest firmware in the Master branch. 

If you want to reporgram (flash) TinyG you need a PDI capable programmer to talk to the Atmel xmega chip. We use the [Atmel AVRISP MKII](http://www.mouser.com/Search/ProductDetail.aspx?qs=sGAEpiMZZMsaJrqdZ%252b6EWyua%252bG%2FwcOQP26MNKN%252bCIDE%3D) which is available from Mouser Electronics for roughly $35.00. This is needed to apply firmware updates on TinyG. We are also testing a boot loader that will program over the USB port, but it's not out yet.<br> 

### Connecting TinyG
At this point you can move on to [Connecting TinyG](https://github.com/synthetos/TinyG/wiki/Connecting-TinyG)