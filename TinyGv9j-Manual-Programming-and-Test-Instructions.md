##Setup
The assembled board should be set up with the following:
* Atmel-ICE programmer
* Four stepper motors
* USB connected to host computer
* Bench power supply - set to 24volts, current limit about 1.5 amps<br>**OBSERVE POLARITY WHEN CONNECTING TO BOARD**

![setup](https://farm4.staticflickr.com/3910/14770638616_fa3c1c8794_b.jpg)
![power](https://farm4.staticflickr.com/3902/14791273484_149bdaa802_b.jpg)

The Atmel-ICE should be plugged into the SAM port
![atmel-ice](https://farm3.staticflickr.com/2912/14813475953_7781856e74_b.jpg)

The JTAG (SWD) connector needs to be properly seated. It's all too easy to plug this connector into only one row of pins.
![jtag-connector](https://farm3.staticflickr.com/2927/14607120307_1fdab4157f_b.jpg)

The motor connectors should plug in as shown. The 5th pin (grounding pin) is left unconnected.
![motor-connectors](https://farm4.staticflickr.com/3898/14606999538_19c8b88de2_b.jpg)

The blue power light should light when the power supply is turned on.

##Programming
Start Atmel Studio 6.2
![studio6.2](https://farm4.staticflickr.com/3847/14790500471_6c7aba38db_b.jpg)

Select /Tools/Device Programming 
![device-programming](https://farm4.staticflickr.com/3902/14606994178_5385b2c3fe_b.jpg)

Select the Atmel ICE. Select the device to be ATSAM3X8C. Select SWD programming mode. Hit APPLY.
![setup-programming](https://farm6.staticflickr.com/5596/14793276122_775356456f_b.jpg)

READ the Device. It should return a device signature and read about 3.3v
![read](https://farm4.staticflickr.com/3853/14790490561_3c5e88d333_b.jpg)

Hit the MEMORIES tab to get the programming dialog
![memories](https://farm4.staticflickr.com/3904/14793271732_1052df055e_b.jpg)

Program the chip. First you must select the tinyg2.elf file you wish to program onto the chip. Then hit PROGRAM. Programming and verification takes about 20-30 seconds. 
![](https://farm4.staticflickr.com/3885/14606985478_22c4f78c2a_b.jpg)

Set the boot fuse. Select GPNVM Bits. Select boot from Flash, Bank 0<br>
_I don't have a picture for this yet and I'm going from memory, so I may not have this exactly right. You do not want the chip to boot from ROM, and you want bank 0, not 1._<br>

Close the programming dialog box. This is necessary to release the USB port so it can be connected to Coolterm.

##Test the Motors
Gentlemen, start your Coolterm.

Select the Options menu and Re-Scan Serial Ports. You should see a port labeled usbmodem001. Select it. Don't worry about baud rates or other settings. These are handled natively by USB, which should connect at 12 Mbps.<br><br>
If you see something like usbmodemfa12121 then try the following:
* Power cycle the board and rescan. Please note this condition in testing notes for this board.
* If that doesn't work check the GPNVM bits and make sure the chip is booting from Flash, Bank 0.

![rescan-ports](https://farm3.staticflickr.com/2919/14606961019_465d4811c4_b.jpg)

Select the Transmit window. It's convenient to set to LINE mode and CR 
![transmit](https://farm6.staticflickr.com/5555/14606959559_128d4b7fda_b.jpg)

Connect to the board. 
![connect to board](https://farm6.staticflickr.com/5587/14607097897_2271207ae0_b.jpg)

Confirm startup string. You should see something like this in the terminal window
![startup string](https://farm3.staticflickr.com/2899/14770612536_398eb602f0_b.jpg)

Enter The following Gcode sequence and look for proper motor movement. 
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
You can use the Coolterm Send String command under the Connection menu to run this command multiple times or for subsequent boards. (On the mac the Connection menu item is at the top of the screen, not on the Coolterm window itself)<br><br>
The string you type into the dialog box will remain available for the next board if you move the send-string window so you can get at it later, and then just click on the window when you need it again.
![gcode](https://farm4.staticflickr.com/3871/14607094947_a11a866053_b.jpg)

## The Next Board
The instructions above were for the first board. Here's what's different for subsequent boards.

###Programming the Next Board
* You should be able to just re-open the Studio6 programming dialog. Most values will be the same, but you still have to APPLY the programmer, READ the chip, select MEMORIES, PROGRAM the chip, and then exit.

###Testing the Next Board
* The previous board is probably still connected - at least in Coolterm's mind. You will need to hit the DISCONNECT button and CONNECT again to establish connection with the new board. You can probably skip the whole OPTIONS dialog as the next board will also come up as usbmodem001. If the board does not connect go back to the options dialog, rescan, and look for the new board in the USB choices.
