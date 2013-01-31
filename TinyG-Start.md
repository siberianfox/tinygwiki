If you have just received a TinyG this is the place to start. Here's some background if you want to know [what TinyG is](https://github.com/synthetos/TinyG/wiki/What-is-TinyG)

## Getting started with TinyG v7
The getting started page is your first place to go to figure out what you need to get to get your TinyG up and running quickly. However before we dive into hooking up wires and downloading software the image below is a "diagram" of the important sections / parts of you TinyG board. 

![TinyG v7 Diagram](https://www.dropbox.com/s/92r2bt5p6fqlw08/TinyGv7-2.png)

To highlight a few things in the above diagram:

* The **MOST IMPORTANT** thing to do is to wire your power input correctly. Double check your polarity BEFORE plugging in your TinyG board.
* Power output for the PC fan is an important to make sure you have right! Failure to set your fan jumper may result in blowing up your PC fan by providing 24v to a 12v fan. See more about this below. 
* All input voltages are limited to 3.3v MAX! 
* DO NOT over torque the current trim pots!

### Parts You Will Need
Here is what you are going to need in order to use TinyG: 

* [A TinyG board](https://www.synthetos.com/webstore/index.php/assembled-electronics/tiny-g.html)
* Power supply - Anything between 12VDC and 30VDC, typically 24 volts DC at 4 to 15 amps. 
* 1 - 4 stepper motors - typically NEMA17 or NEMA23 up to 2.5 amps per winding
* Fan - A 12VDC or 24VDC fan is recommended, especially if the board is in an enclosure 
* Programmer (Optional.. If you want firmware updates..)
* Case (Optional) 

Please note: Stepper Motor Connectors are required for v6 and earlier, but are no longer needed with v7s!

#### TinyG Board
You can get the TinyG controller board fully assembled from the [Synthetos Store](https://www.synthetos.com/webstore/index.php/assembled-electronics/tiny-g.html)

TinyG v7's are "blue" boards labeled as TinyGv7 - 23. These use the DRV8818 driver chips as opposed to the DRV8811 driver chips. The 8818 drivers have a lower ON resistance in the FET output stages and therefore don't get as warm as the 8811's are are capable of driving a bit more current. The '23' designation refers to NEMA23 motors, as the blue boards will easily drive most NEMA23 motors. 

We may continue to sell green boards with the DRV8811 driver chips as well, and will begin labeling these as '- 17s". These boards are recommended for NEMA17 motors, although that are quite capable of driving NEMA23's - as they have been doing for the past 2 years in production setups. The green boards will be made available at a lower price than the blue boards.

#### Power Supply
TinyG v7 (either green or blue) supports up to 2.5A per winding per motor and needs a 12v-30v motor power input. While 12 volt operation is possible and entirely fine, running with a 24v power supply will allow the motors to be more responsive and actually run cooler (ironically). We recommand fan cooling the board if you use a voltage over 12 volts. 

Here's one we like: [Meanwell NES-350-24](http://www.jameco.com/webapp/wcs/stores/servlet/ProductDisplay?langId=-1&storeId=10001&catalogId=10001&pa=2149600&productId=2149600&keyCode=WSF&CID=GOOG&gclid=CKGp2eipk7UCFWGnPAod9jMAKA)<br>You can usually hunt around and find this for < $50. They also make lower amperage supplies that are cheaper.

#### Stepper Motors
TinyG will work with bipolar and unipolar stepper motors up to 2.5 amps per winding. This covers most NEMA23 and smaller motors. It will not work with motors wired as 5 wire "star configurations", so avoid these. Some of out favorite sources for stepper motors are: 

* [Alltronics](http://www.alltronics.com/cgi-bin/category/55 www.alltronics.com/cgi-bin/category/55)
* [MPJA](http://www.mpja.com/Stepper-Motors/products/101/ www.mpja.com/Stepper-Motors/products/101/)
* [All Electronics](http://www.allelectronics.com/make-a-store/category/400/Motors/1.html www.allelectronics.com/make-a-store/category/400/Motors/1.html)
* [Sparkfun](https://www.sparkfun.com/categories/178)
* [Phidgets](http://www.phidgets.com/products.php?category=23)

#### Heat Sinks
These are optional, however they come with TinyG so why not use them:)<br> 4x heat sinks are included with TinyG. Simply peel the back sticker off and place the heat sink over each DRV8818 stepper driver chip.  

[[Image:TinyGv7-Heatsinks.png]] 

#### Cooling Fan
The TinyGv7's come equipped with connector that can be used to power a standard 12vdc PC fan, or a 24vdc fan - depending on a jumper setting and your board voltage (Vmot). Please read the silkscreen designators as below - sorry it's not more straightforward:

* The "24v" jumper position connects the fan header to the LM7812 12 volt regulator. This should be used if you are running a 12 volt fan and your board power (Vmot) is > 14 volts. This is the default location and your v7 should have been shipped with the jumper in this position. 
* The "12v" jumper position connects the fan header directly to the board power (Vmot). In this case your fan voltage needs to be the same as your Motor voltage, be it 12 volts, 24 volts, or anything else.<br>
**Use this side with caution as applying 24 volts to some 12 volt fans will burn them out.**

#### Programmer
TinyG runs an Atmel xmega chip which requires Atmel's PDI protocol to program. We use the Atmel AVRISP MKII which is available from Mouser [http://www.mouser.com/Search/ProductDetail.aspx?qs=sGAEpiMZZMsaJrqdZ%252b6EWyua%252bG%2FwcOQP26MNKN%252bCIDE%3D here] for roughly $35.00. This is needed to apply firmware updates on TinyG. We are also testing a boot loader that will program over the USB port, but it's not out yet.<br> 

TinyG is available at the [https://github.com/synthetos/TinyG TinyG github]<br> TinyG comes pre-loaded with the latest version at the time of purchase. Upgrading (flashing) TinyG firmware is here: [[Projects:TinyG-UpdateFirmware|Updating TinyG Firmware]].