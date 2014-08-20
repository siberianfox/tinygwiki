##Setup
The assembled board should be set up with the following:
* Atmel-ICE programmer
* Four stepper motors
* USB connected to host computer
* Bench power supply - set to 24volts, current limit about 1.5 amps<br>**OBSERVE POLARITY WHEN CONNECTING TO BOARD**

![setup](https://farm4.staticflickr.com/3845/14787093530_92e84aa772_b.jpg)
![power](https://farm4.staticflickr.com/3859/14787227777_24e21d4ea5_b.jpg)

* The Atmel-ICE should be plugged into the SAM port

![atmel-ice](https://farm3.staticflickr.com/2912/14813475953_7781856e74_b.jpg)

* The JTAG (SWD) connector needs to be properly seated. It's all too easy to plug this connector into only one row of pins.

![jtag-connector](https://farm3.staticflickr.com/2927/14607120307_1fdab4157f_b.jpg)

* The motor connectors should plug in as shown. The 5th pin (grounding pin) is left unconnected. The ground pin is marked by white silkscreen on the board.

![motor-connectors](https://farm4.staticflickr.com/3898/14606999538_19c8b88de2_b.jpg)

**VERIFY that the blue power LED is lit when the power supply is turned on**<br>
(Some other LEDs may also be lit)

##Board Setup
Make sure the following jumpers are placed:
* J19 jump pins 1 and 2 (+12v position). J19 is located behind the fan connector
* J20 jump pins 1 and 2 (3.3v position). J20 is located above the U10 14 pin SOIC
* J15 jump pins 13 and 26. (Interlock override) These pins are located above the 10 pin mini-JTAG connector J6, and are marked with white silkscreen and labeled "Jump for Operation"

##Programming
If you have not already done so bring up the host computer and related programs
* Boot the mac (which is what we are using in this example. You know the password)
* Start the Windows 7 virtual machine (virtual machine labeled as `W7 WORKING CURRENT`)

* Start Atmel Studio 6.2. The file name is TinyG2.atsln. AS6 can be started either from the project directory or from the ladybug on the desktop. Atmel studio 6.2 can take a loooong time to start up. 2-3 minutes on some machines.

![studio6.2](https://farm4.staticflickr.com/3847/14790500471_6c7aba38db_b.jpg)
![studio6.2_2](https://farm4.staticflickr.com/3904/14660499388_fa0c9cb5bc_b.jpg)

* Select `/Tools/Device Programming`

![device-programming](https://farm4.staticflickr.com/3902/14606994178_5385b2c3fe_b.jpg)

* Under TOOL select the Atmel ICE. See the second picture if you don't see the Atmel-ICE as an option 
* Under DEVICE select ATSAM3X8C from the long list of Atmel products
* Under INTERFACE select SWD programming mode
* Hit APPLY

![setup-programming](https://farm6.staticflickr.com/5596/14793276122_775356456f_b.jpg)
![vmware-usb-dialog](https://farm4.staticflickr.com/3915/14846789962_315b1fb2b8_b.jpg)
If the Atmel-ICE is not an option it's either because it's (1) not plugged in, or (2) not connected to the virtual machine. Use the above dialog to connect it. Then go back to the TOOL / DEVICE / INTERFACE step, above.

* Power the board if not already powered
* Hit READ to query the Device
* It should return a device signature and read about 3.3v

![read](https://farm4.staticflickr.com/3853/14790490561_3c5e88d333_b.jpg)

* Hit the MEMORIES tab to get the programming dialog

![memories](https://farm4.staticflickr.com/3904/14793271732_1052df055e_b.jpg)

* Program the chip. 
* First you must select the tinyg2.elf file you wish to program onto the chip. This is usually in the project directory in which you found the TinyG2.atsln file. The file select is sticky so it will stay selected if you are doing more than one board.
* Hit PROGRAM. Programming and verification takes about 20-30 seconds
* When done the red TX LED on the board should cycle at about 1 Hz.

![](https://farm4.staticflickr.com/3885/14606985478_22c4f78c2a_b.jpg)

* Select GPNVM Bits. 
* Select boot options:
  * Flash
  * Bank 0

![boot-fuses](https://farm6.staticflickr.com/5557/14660500899_fd6205cbe7_b.jpg)

* Close the programming dialog box. This is necessary to release the USB port so it can be connected to Coolterm.<br><br>
**Verify programming is OK - the red TX LED on the board should be lit and will begin to cycle at about 1 Hz.**

##Test the Motors
Gentlemen, start your Coolterm.

* Select the Options menu and Re-Scan Serial Ports
* In the drop-down menu you should see a port labeled usbmodem001
* Select it

Don't worry about baud rates or other settings. These are handled natively by USB, which should connect at 12 Mbps.<br><br>
If you see something other than usbmodem001 (like usbmodemfa12121) then try the following:
* Power cycle the board and rescan
* If that doesn't work check the GPNVM bits to make sure it's booting from Flash, Bank 0
* Please note this condition in testing notes for this board

![rescan-ports](https://farm3.staticflickr.com/2919/14606961019_465d4811c4_b.jpg)

* Select the Terminal window
* Set to LINE mode 
* Set to CR

![transmit](https://farm6.staticflickr.com/5555/14606959559_128d4b7fda_b.jpg)

* Connect to the board

![connect to board](https://farm6.staticflickr.com/5587/14607097897_2271207ae0_b.jpg)

* Confirm startup string. You should see something like this in the terminal window

![startup string](https://farm3.staticflickr.com/2899/14770612536_398eb602f0_b.jpg)

* Enter The following Gcode sequence and look for proper motor movement. 
* The test file is on the mac Desktop as `Test Pattern.txt` or `Test Pattern.gcode`
<pre>
G0 X20
Y20
Z20
A20
X0
Y0
Z0
A0
G1 F200 X4
Y4
Z4
A4
X0 Y0 Z0 A0

</pre>
The final move must be terminated with a carriage return or it won't run.<br><br>

Instead of typing this in use the Coolterm `Connection/Send Textfile` to run this command. (On the mac the Connection menu item is at the top of the screen, not on the Coolterm window itself)<br><br>
The string you type into the dialog box will remain available for the next board if you move the send-string window so you can get at it later, and then just click on the window when you need it again.
![gcode](https://farm4.staticflickr.com/3871/14607094947_a11a866053_b.jpg)

* Note: The command `$md` will shut down the motor power if you need to.

## The Next Board
The instructions above were for the first board. Here's what's different for subsequent boards.

###Programming the Next Board
* You should be able to just re-open the Studio6 programming dialog. Most values will be the same, but you still have to APPLY the programmer, READ the chip, select MEMORIES, PROGRAM the chip, and then exit.

###Testing the Next Board
* The previous board is probably still connected - at least in Coolterm's mind. You will need to hit the DISCONNECT button and CONNECT again to establish connection with the new board. You can probably skip the whole OPTIONS dialog as the next board will also come up as usbmodem001. If the board does not connect go back to the options dialog, rescan, and look for the new board in the USB choices.


# Tester Notes
These are mostly notes to anyone building a test jig
* http://littlemachineshop.com/Reference/tapdrill.php
* Use number 43 drill for 4-40 HDPE self tapping
* Use number 8 drill for body drill (clear plexi mount) for 10-24 2-1/4" screws for DaStaCo 202UL clamp
* Use number 25 drill for tap drill (HDPE base) for 10-24 2-1/4" taps for screws for DaStaCo 202UL clamp