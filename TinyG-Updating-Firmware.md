This page explains what you need to know about the process of updating your TinyG firmware.  It provides the links to the firmware files as well as the differences between code branches.  Lastly, it will explain the updating procedure and how to perform this procedure.  However, if you are experiencing something different than anything below we urge you to open a thread on the [TinyG Support Forum](https://www.synthetos.com/forums/tinyg/tinyg-support/) for additional assistance.

### Flashing TinyG (Firmware Updates)
The TinyG code base is still under development. We introduce new features and fix bugs quite often. This page contains all information on how to update TinyG's firmware.

###Firmware Updates the Easy Way!
We created a TinyG Updater app that is a GUI and will go and pull the latest Edge and Master PREBUILD binaries to update your TinyG with.  You can read all about it here:
[Synthetos TG Updater App Instructions](https://github.com/synthetos/TinyG/wiki/TinyG-TG-Updater-App).

###Downloading TinyG Firmware
For downloads of the binaries (hex files), see [Synthetos Download Page](http://synthetos.github.io/)

###TinyG Firmware Explained
The TinyG development team uses three main code branches, Master, Edge and Dev. Each code branch is explained below.  After reading each code branch description you should have enough information on to which firmware you would like to load onto your TinyG.  However, when in doubt, pick the master branch.

####Master Branch
The main branch is the `master` branch.  Master is considered **stable** and 99.9% of the time this is the code base you need to use. See here for the master hex file: [Synthetos Download Page](http://synthetos.github.io/)

####Edge Branch
The second branch is the `edge` branch.  **The edge branch should not be used in production!**  This branch is used to test upcoming features and possible bug fixes.  This branch should be considered `semi-stable` or in a `testing` state. See here for the edge hex file: [Synthetos Download Page](http://synthetos.github.io/)

####Dev Branch
The last branch is the `dev` branch.  In the spirit of completeness we are mentioning the `dev` branch.  This branch is in the state of **all bets are off**.  **The dev branch should be assumed that it will instantly break your machine or ruin any work on your CNC. ** Therefore we provide NO links for the `tinyg.hex` dev branch firmware file. If you want to use dev you will need to compile the project. Setting up a project is described here: 


Once you have downloaded the firmware of your choice, you can move on to firmware updating methods, below.

##Firmware Update Methods
You will need to select a TinyG firmware `master` or `edge` branches.  If you have not done so you need to return to the [firmware updating explained](#tinyg-firmware-explained) section above and selected a TinyG firmware file to download.  

There are 3 ways to update the firmware on your TinyG.  The first two methods is to use the bootloader on TinyG along with avrdude.  This method requires no special hardware programming devices, only the TinyG USB cable.
<br>
The third method is using an AVR Programmer (such as an AVRISP MKII programmer).  This method requires more work and is not described here as of yet.  It is also not needed if you have a bootloader loaded on your TinyG.  If you would like to read more about how to tell if you have a bootloader on your TinyG you can go [here](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader).  

###Updating TinyG With Avrdude
To update the TinyG firmware run avrdude from a directory that has the tinyg.hex file you want to load.  You can get the avrdude prebuilt for your platform here

* https://github.com/arduino/arduino-flash-tools/archive/master.zip

####**Step0**
Again, if you have not already acquired the `tinyg.hex` from the code branch your choose (TinyG Firmware File) you will need to do so. In 99.9% of cases you will want the hex file located in the master branch. The tinyg.hex file is found here: [Synthetos Download Page](http://synthetos.github.io/)

Save the hex file in some known directory that you can get back to.

Notes: 
- The download hex is compiled using AVR Studio 4.19, which is the dev environment we test with. If you compile using Atmel Studio 6.x, your hex will have a different size.

####**Step1**
Navigate to the directory that has the tinyg---.hex file you want.

####**Step2**
Find your serial port. You will need to enter the USB port you are actually using. To find your serial port in Mac/Linux you can run `ls /dev` and look for the tty.usbserial-XXXXXXX port<br>

####**Step3**
To update your firmware we must enter your TinyG into bootloader mode.  To enter bootloader mode on the TinyG it is as easy as hitting the `reset` button on the TinyG board.  (You can locate the reset button on this [diagram](https://github.com/synthetos/TinyG/wiki/TinyG-Start#getting-started-with-tinyg---what-you-need)).  You can tell that your TinyG is in bootloader mode by the flashing red Spindle CW/CCW light.  You can also enter bootloader mode by sending `$boot=1` or `{"boot":1}` from the command line interface via console program.  You can read more about the [command line interface](https://github.com/synthetos/TinyG/wiki/TinyG-Command-Line) if you wish.  You will have 3 seconds to initiate an firmware update via `avrdude`.  However, we will describe how to do this next.  Just remember to recap, this is how you can enter your TinyG into bootloader mode:

* Hit the reset button on the board
* Send a `^x` (control X) to the board (software reset)
* Send the command `$boot=1`
* Send the JSON command `{"boot":1}`


To transfer the firmware update to your TinyG you need to use a program called avrdude.  avrdude is open-source and runs on all major operating systems.  The quickest way to get a binary version of avrdude for your system is to download the latest version of the [Arduino IDE](http://arduino.cc/en/Main/Software#toc1).  

Inside of the Arduino IDE directory you will find avrdude or averdude.exe. Below is the command that you need to issue to send and updated firmware to TinyG. 
`avrdude -p x192a3 -c avr109 -b 115200 -P COM1 -U flash:w:tinyg----.hex`  (use the correct hex file name)

**Notes:**<br> 
1. This assumes that the file (tinyg----.hex) that you downloaded above, is in the same directory as the avrdude.exe is.<br>
2.  While trying to update your TinyG you can have only 1 connection open to your serial port.  If you have coolterm, tgFX or any other program that is using your TinyG's serial port connection you will first need to disconnect before attempting to update your TinyG.<br>
3.  COM1 was the port that I used on my system.  This could be different on your system.   For instance this is an example of an OSX avrdude commandline.  `avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg-master-380.08.hex`<br>
4.  On Windows, avrdude is found in \Arduino\hardware\tools\avr\bin . Open a command window, navigate to the avrdude directory. Issue the avrdude command from there. Your com port is the same one that CoolTerm uses. CoolTerm and avrdude can't both use the com port at the same time. Mine was COM9.<br>
5.  In some case, avrdude will respond with an error:<br>
<pre>    avrdude: can't open config file "": Invalid argument
    avrdude: error reading system wide configuration file ""
</pre>
This occurs when avrdude can't find it's configuration file, and you'll need to tell it where to look to get the command line update to work. The avrdude configuration file is usually found in \Arduino\hardware\tools\avr\etc, so add the "-C ..\etc\avrdude.conf" option to your command line string. 
`avrdude -C ../etc/avrdude.conf -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg-master-380.08.hex`<br>
6.  When entering command line options with directory structures, use a backslash `\` to separate directories in Windows, and use a forward slash `/` in MacOS and Linux.<br>

If all worked correctly, you should see the following dialog if the loader works correctly
<pre>
macintosh-3:default username$ avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg----.hex

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
avrdude: reading input file "tinyg----.hex"
avrdude: input file tinyg----.hex auto detected as Intel Hex
avrdude: writing flash (96706 bytes):

Writing | ################################################## | 100% 12.09s



avrdude: 96706 bytes of flash written
avrdude: verifying flash memory against tinyg----.hex:
avrdude: load data flash data from input file tinyg----.hex:
avrdude: input file tinyg----.hex auto detected as Intel Hex
avrdude: input file tinyg----.hex contains 96706 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 11.51s



avrdude: verifying ...
avrdude: 96706 bytes of flash verified

avrdude done.  Thank you.

macintosh-3:default username$ 
</pre>

###Troubleshooting
This section should help you sift though any errors you might encounter during the firmware updating procedure.  

####Firmware Verification Error:
This error occurs when the `fuse bits` are incorrectly set on your TinyG's XMega CPU.  This error was something we ran into in early 2012 and should not continue to be an issue. However, if you do get this error or you have an older TinyG you should read [this page](https://github.com/synthetos/TinyG/wiki/Firmware-Update-Verification-Failure) for help solving this issue.

####Writing to Flash emits lots of "***failed;" lines:
Try adding `-e` option to avrdude:

```
avrdude -p x192a3 -c avr109 -b 115200 -P /dev/ttyUSB0 -e -U flash:w:tinyg----.hex
```

This option erases a Flash page before writing it and in some cases it's required.

If you've previously used the -e option as a separate command, are now trying to flash the TinyG, and are getting the "***failed;" lines; try using the -D option when trying to flash to disable the page erases.


####Never Ending Flashing Red Light After Update
So you did everything above and you flashed your TinyG with tinyg.hex file from github.  Now your TinyG just continues to flash a red light on the spindle CW/CCW led.  This most likely was an invalid tinyg.hex file.  99% of the time this occurs when someone tries to save the tinyg.hex file from github and actually just pulls down the HTML vs the raw Intel Hex file format that TinyG needs.  You can verify your tinyg.hex file is valid by opening the file with a text editor.

Your hex file must contain many of lines like this:
<br>
`:100000000C9402190C9423190C94F1990C94231953`
<br>
If your hex file does not look similar to the format above, your tinyg.hex file is not valid and is corrupt.  Most often if your tinyg.hex file look like this below:
<br>
`
<!DOCTYPE html>
`
<br>
Then somehow you did not get the tinyg.hex file correctly.  Go back to [Synthetos Download Page](http://synthetos.github.io/). The file should download with a single click. Once you re-download this file you should again verify with a text editor that it does indeed now start with something similar to this:
<br>
`:100000000C9402190C9423190C94F1990C94231953`
<br>
If so go ahead and try to reflash.