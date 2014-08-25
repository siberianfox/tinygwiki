##Tester Setup
The tester kit should be equipped with the following:
* Test jig 
  * Tester board and pressure contact plate for tinyGv9k
  * Beaglebone Black
  * Atmel-ICE programmer (previously delivered)
* Four stepper motors with 0.156 friction lock headers (previously delivered)
* Bench power supply - set to 24volts, current limit about 1.5 amps (previously delivered)<br>
**OBSERVE POLARITY WHEN CONNECTING TO BOARD**

![setup](https://farm4.staticflickr.com/3845/14787093530_92e84aa772_b.jpg)
![power](https://farm4.staticflickr.com/3859/14787227777_24e21d4ea5_b.jpg)

Connections to tester:
* Connect +24vdc from the bench supply (off, of course)
* Connect USB to the USB on the tester. This will be used to connect to the board via Coolterm
* Connect the Atmel-ICE to the 0.050" JTAG connector. 
  * Be sure the Atmel-ICE is in the SAM position as pictured below.
* Connect a 12 volt PC fan to the fan connector
* Place a jumper on J11 to connect the Interlock
* Place a jumper on J2 when testing OEM configuration boards. This jumper should be removed for End-User configuration boards (but will not do damage if it is left on).
* Connect 4 stepper motors to J6, J7, J8, and J9 positions 

![atmel-ice](https://farm3.staticflickr.com/2912/14813475953_7781856e74_b.jpg)

Apply power to the tester. The Vmot +24vdc blue LED should be lit. If J2 is in place Vmot2 should also be lit. No other LEDs should be lit.

##Board Setup - Done for each board to test
Make sure the following jumpers are placed on the board under test:
* J19 jump pins 1 and 2 (+12v position). J19 is located behind the fan connector
* J20 jump pins 1 and 2 (3.3v position). J20 is located above the U10 14 pin SOIC
* Place board on test jig and secure with clamp.
* Apply 24vdc power form bench supply.
The following LEDs should be lit:
   * Vmot (24vdc)
   * Vmot2 (24vdc)
   * 5v
   * 3.3v
   * 12v
   * The fan should turn on if it is connected to the tester

###NOTE: FROM HERE ON DOWN ARE THE MANUAL TEST INSTRUCTIONS. THESE WILL BE UPDATED WITH AUTOMATED PROCESSES.

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
  The v9k file con be found here: https://www.dropbox.com/s/599xo2xfsy9lih6/TinyG2.elf?dl=0
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
The file coan be found here: https://www.dropbox.com/s/os0mkzr0p26k6ms/test_pattern.txt?dl=0

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
* Use number 8 drill for body drill (1/4" plexi pressure plate) for 10-24 2" screws for pressure plate to DaStaCo 202UL clamp
* Use number 43 drill for tap drill (1/4" plexi pressure plate) for 4-40 tap screws