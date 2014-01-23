This page explains what you need to know about the process of updating your TinyG firmware.  It provides the links to the firmware files as well as the differences between code branches.  Lastly, it will explain the updating procedure and how to perform this procedure.  

##TinyG Firmware Explained
The TinyG development team uses three code branches.  Master, Edge and Dev.  Each code branch is fully explained below.  After reading each code branch description you should have enough information on to which firmware you would like to load onto your TinyG.  However, when in doubt, pick the master branch.

###Master Branch
The first branch is the `master` branch.  This branch is considered **stable** and 99.9% of the time this is the code base you need to use. You can get the `tinyg.hex` firmware file for the `master` branch at:
https://github.com/synthetos/TinyG/raw/master/firmware/tinyg/default/tinyg.hex


Please note that you will need to **"right click, save as"** to save this file properly.  Failure to do so will introduce a corrupted firmware download.

###Edge Branch
The second branch is the `edge` branch.  **The edge branch should not be used in production!**  This branch is used to test upcoming features and possible bug fixes.  This branch should be considered `unstable` or in a `testing` state.   You can get the `tinyg.hex` firmware file for the `edge` branch at:
https://github.com/synthetos/TinyG/raw/edge/firmware/tinyg/default/tinyg.hex

Please note that you will need to **"right click, save as"** to save this file properly.  Failure to do so will introduce a corrupted firmware download.

###Dev Branch
The last branch is the `dev` branch.  In the spirit of completeness we are mentioning the `dev` branch.  This branch is in the state of **all bets are off**.  **The dev branch should be assumed that it will instantly break your machine or ruin any work on your CNC. ** Therefore we provide NO links for the `tinyg.hex` dev branch firmware file.


Once you have downloaded the firmware of your choice, you can move on to [firmware updating methods](https://github.com/synthetos/TinyG/wiki/_preview#firmware-updating-methods).


##Firmware Updating Methods

You will need to select a TinyG firmware `master` or `edge` branches.  If you have not done so you need to return to the [firmware updating explained](https://github.com/synthetos/TinyG/wiki/_preview#tinyg-firmware-explained) section above and selected a TinyG firmware file to download.  

There are 2 ways to update the firmware on your TinyG.  The first method is to use the bootloader on TinyG along with avrdude.  This method requires no special hardware programming devices, only the TinyG USB cable.
<br>
The second method is using an AVR Programmer (such as an AVRISP MKII programmer).  This method requires more work and is not described here as of yet.  It is also not needed if you have a bootloader loaded on your TinyG.  If you would like to read more about how to tell if you have a bootloader on your TinyG you can go [here](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader).  

<a id="updating"></a>
##Updating TinyG With avrdude
To update the TinyG firmware run avrdude from a directory that has the tinyg.hex file you want to load.<br>

###**Step0**
Again, if you have not already acquired the `tinyg.hex` from the code branch your choose (TinyG Firmware File) you will need to do so. In 99.9% of cases you will want the hex file located in the master branch. The tinyg.hex file is found here:

`https://raw.github.com/synthetos/TinyG/master/firmware/tinyg/default/tinyg.hex`

Save page as tinyg.hex (you may need to get rid of a .txt extension during this save). Save it in some known directory that you can get back to.

Notes: 
- If you just go to the github file page and download from there you will not get the hex, you will get HTML. You need the RAW page. 
- The hex in the default directory is compiled using AVR Studio 4, which is the one we test on. There may also be a hex in a Debug directory. This is compiled in Atmel Studio 6, and will have a different size.

###**Step1**
Navigate to the directory that has the tinyg.hex file you want.

###**Step2**
Find your serial port. You will need to enter the USB port you are actually using. To find your serial port in Mac/Linux you can run `ls /dev` and look for the tty.usbserial-XXXXXXX port<br>

###**Step3**
To update your firmware we must enter your TinyG into bootloader mode.  To enter bootloader mode on the TinyG it is as easy as hitting the `reset` button on the TinyG board.  (You can locate the reset button on this [diagram](https://github.com/synthetos/TinyG/wiki/TinyG-Start#getting-started-with-tinyg---what-you-need)).  You can tell that your TinyG is in bootloader mode by the flashing red Spindle CW/CCW light.  You can also enter bootloader mode by sending `$boot=1` or `{"boot":1}` from the command line interface via console program.  You can read more about the [command line interface](https://github.com/synthetos/TinyG/wiki/TinyG-Command-Line) if you wish.  You will have 3 seconds to initiate an firmware update via `avrdude`.  However, we will describe how to do this next.  Just remember to recap, this is how you can enter your TinyG into bootloader mode:

* Hit the reset button on the board
* Send a `^x` (control X) to the board (software reset)
* Send the command `$boot=1`
* Send the JSON command `{"boot":1}`


To transfer the firmware update to your TinyG you need to use a program called avrdude.  avrdude is open-source and runs on all major operating systems.  The quickest way to get a binary version of avrdude for your system is to download the latest version of the [Arduino IDE](http://arduino.cc/en/Main/Software#toc1).  

Inside of the Arduino IDE directory you will find avrdude.exe (or just avrdude if in mac or linux).  Below is the command that you need to issue to send and updated firmware to TinyG.
`avrdude -p x192a3 -c avr109 -b 115200 -P COM1 -U flash:w:tinyg.hex`

**Notes:** 
1. This assumes that the file (tinyg.hex) that you downloaded above, is in the same directory as the avrdude.exe is.
2.  While trying to update your TinyG you can have only 1 connection open to your serial port.  If you have coolterm, tgFX or any other program that is using your TinyG's serial port connection you will first need to disconnect before attempting to update your TinyG.
3.  COM1 was the port that I used on my system.  This could be different on your system.   For instance this is an example of an OSX avrdude commandline.  `avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg.hex`


If all worked correctly, you should see the following dialog if the loader works correctly
<pre>
macintosh-3:default username$ avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg.hex

Connecting to programmer: .
Found programmer: Id = "XBoot++"; type = S
    Software Version = 1.7; No Hardware Version given.
Programmer supports auto addr increment.
Programmer supports buffered memory access with buffersize=512 bytes.

Programmer supports the following devices:
    Device code: 0x7b

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.00s

avrdude: Device signature = 0x1e9744
avrdude: NOTE: FLASH memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file "tinyg.hex"
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: writing flash (96706 bytes):

Writing | ################################################## | 100% 12.09s



avrdude: 96706 bytes of flash written
avrdude: verifying flash memory against tinyg.hex:
avrdude: load data flash data from input file tinyg.hex:
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: input file tinyg.hex contains 96706 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 11.51s



avrdude: verifying ...
avrdude: 96706 bytes of flash verified

avrdude done.  Thank you.

macintosh-3:default username$ 
</pre>