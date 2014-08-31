## Background and Shorthand
The following shorthand is used in these instructions:

	Term | Description
	-----|--------------
	UUT | Unit Under Test. They TinyGv8 board that is being programmed and tested
	HOST | Tester's host computer used to program the board and start the tests via USB
	AVRISP | The blue Atmel programmer. It has a USB connector and a programming header
	TESTER | The large blue board with four mounting standoffs, 18 pogo pins, and the wired motor and power connectors
	POWER SUPPLY | The bench power supply providing current-limited 24 volts for testing

Verification steps are marked as **[VERIFIED xxxxxx]** for the step

#Initial Setup
##Host Setup
The host computer can be a Mac OSX machine, Linux or a Windows machine. Instructions are provided for OSX, but Linux and Windows should be similar. The host needs to be set up with Avrdude and a terminal application (Coolterm). Setup steps are:
* Download and install [Coolterm](http://freeware.the-meiers.org/) for your platform.
* Set up a directory for programming. Copy in everything that's in this [directory](https://www.dropbox.com/sh/a98g2zxpqbqt6nb/AACiT2CrDZeA-4hcnr1e7wdqa). The Avrdude in the directory is for OSX. Other platforms can be found here:
 * https://github.com/arduino/Arduino/blob/master/build/macosx/dist/tools-universal.zip
 * https://github.com/arduino/Arduino/tree/master/build/linux/dist/tools
 * https://github.com/arduino/Arduino/tree/master/build/windows
* The tinyg.hex file in the directory is the current release. If you need a different hex file replace this one.

##Test Rig Setup
The test rig should look something like this:

![Tester-wide](https://farm4.staticflickr.com/3859/14634630352_4098b8a3e6_o_d.jpg)

![Tester-close](https://farm6.staticflickr.com/5570/14631839291_2021285168_o_d.jpg)

Test kit includes:
* Tester board with 14 pogo pins
* 4 NEMA23 motors
* Atmel AVRISP MkII programmer (blue thing)
* 2 USB cables - one for the AVRISP and 1 for the UUT
* MPJA 9631PS bench power supply
* Extra pogo pins and hold-down standoffs

The large bench supply, oscilloscope and the Ultimaker in the picture are not part of the tester kit.

* Turn on the bench supply and adjust to 24.0 volts, it not already set. Turn off.
* Connect the bench supply and the motors to the tester.
* Verify that the pogo pins are all at the same starting level and have about 1/4" of good travel

**[VERIFIED TEST JIG KIT]**

#Prep for a Test Run
These steps need to be done at the start of each test run

* Turn off the bench supply.
* Inspect the test rig and verify against the picture above
 1. Verify that you have at least two 1 inch 4/40 hex standoffs available to secure the UUT to the tester
* With power off, align motor flags so they all point vertically - i.e. the 12:00 position. 
* Boot the host computer
* Connect the AVRISP (blue thing) and the USB cable for the TinyG board to the host USB ports
  * Verify the green LED inside the AVRISP is lit (not flashing). This verifies USB connection between the BBB 
* Start Coolterm
* Select the `OPTIONS` dialog and set up the Serial Port and Terminal windows as shown. You don't have a board plugged in yet so you won't see the usbserial-xxxxx option. Just set the baud rate and flow control.

![Coolterm-connect](https://farm6.staticflickr.com/5489/14632997114_9190193f99_o_d.jpg)

![Coolterm-serial](https://farm6.staticflickr.com/5564/14631893761_69be22d2dc_o_d.jpg)

![Coolterm-terminal](https://farm4.staticflickr.com/3924/14448727147_292950c987_o_d.jpg)

* Open up a command line terminal window (e.g. Terminal on OSX, Command on Win) and navigate to your Avrdude directory.

**[VERIFIED PREP]**

#Per-Board Instructions
Per-board tests should take about 1 to 2 minutes minute to complete.

## Per Board Summary
**[Mount and Prep Board](#mount-and-prep-board)**

1. With power off, mount board on tester
1. Plug in programmer and USB cable
1. Check pots for 50%
1. Place fan jumper on 12v position
1. Turn on bench supply and verify blue LED **[VERIFIED 3.3v POWER]**

**[Program Board](#program-the-board)**

1. Cut and paste into terminal window: `avrdude -q -c avrisp2 -p atxmega192a3 -P usb -u -U flash:w:tinyg.hex -U boot:w:xboot-boot.hex -U fuse0:w:0xFF:m -U fuse1:w:0x00:m -U fuse2:w:0xBE:m -U fuse4:w:0xFE:m -U fuse5:w:0xEB:m`
   Alterate: `./avrdude -C ./avrdude.conf -q -c avrisp2 -p atxmega192a3 -P usb -u -U flash:w:tinyg.hex -U boot:w:xboot-boot.hex -U fuse0:w:0xFF:m -U fuse1:w:0x00:m -U fuse2:w:0xBE:m -U fuse4:w:0xFE:m -U fuse5:w:0xEB:m`
1. Look for red PWM LED to be dimly lit **[VERIFIED PROGRAMMING]**

**[Connect to Board](#connect-to-board)**

1. Coolterm sequence: `Disconnect`, `Options`, `Re-Scan Serial Ports`, `OK`, `Connect`
1. Enter ? to verify connection **[VERIFIED USB CONNECTION]**
1. Press reset to verify flasshing bot loader and startup strings **[VERIFIED BOOT LOADER]**

**[Run Board Functional Tests](#run-board-functional-tests)**

1. Enter `$test=1`
1. Confirm LED pattern **[VERIFIED OUTPUTS]**
1. Confirm motor movement and current levels **[VERIFIED MOTOR DRIVERS]**
1. Turn off bench power and wait for blue LED to turn off before removing board

##Mount and Prep Board
* With the bench supply off, affix the UUT board onto the tester. Make sure all pogos connect, and secure with two hold-down standoffs as pictured.
* Plug in the programmer (blue thing) and the USB port to the board
![Tester-with-board](https://farm4.staticflickr.com/3855/14654991973_2ace1fbd3d_o_d.jpg)

* Verify that the potentiometers are in the 50% position:
![Board-pots](https://farm3.staticflickr.com/2933/14631844071_55065a3316_o_d.jpg)

* Place a jumper on the +12v position on J12 
![Board-jumper](https://farm4.staticflickr.com/3904/14635093995_0914d6625e_o_d.jpg)

* Turn ON bench power supply. 
* Verify that the blue power LED is lit

**[VERIFIED 3.3v POWER]**

##Program the Board
* Go to the terminal window. Copy and paste the programming string from the ProgrammingString.txt file into the terminal window and hit return: `avrdude -q -c avrisp2 -p atxmega192a3 -P usb -u -U flash:w:tinyg.hex -U boot:w:xboot-boot.hex -U fuse0:w:0xFF:m -U fuse1:w:0x00:m -U fuse2:w:0xBE:m -U fuse4:w:0xFE:m -U fuse5:w:0xEB:m`

* If you don't have AVRdude already installed you can get if from these links:
http://www.ladyada.net/learn/avr/setup-mac.html
http://www.obdev.at/products/crosspack/download.html

  Or just go to the programming directory and enter this command string:
`./avrdude -C ./avrdude.conf -q -c avrisp2 -p atxmega192a3 -P usb -u -U flash:w:tinyg.hex -U boot:w:xboot-boot.hex -U fuse0:w:0xFF:m -U fuse1:w:0x00:m -U fuse2:w:0xBE:m -U fuse4:w:0xFE:m -U fuse5:w:0xEB:m`



<See Synthetos/PRoduction Support/TinyGv8_tester_version_2>
  You should see something like this:
<pre>
avrdude: AVR device initialized and ready to accept instructions
avrdude: Device signature = 0x1e9744
avrdude: NOTE: FLASH memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file "tinyg.hex"
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: writing flash (117856 bytes):
avrdude: 117856 bytes of flash written
avrdude: verifying flash memory against tinyg.hex:
avrdude: load data flash data from input file tinyg.hex:
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: input file tinyg.hex contains 117856 bytes
avrdude: reading on-chip flash data:
avrdude: verifying ...
avrdude: 117856 bytes of flash verified
avrdude: reading input file "xboot-boot.hex"
avrdude: input file xboot-boot.hex auto detected as Intel Hex
avrdude: writing boot (4496 bytes):
avrdude: 4496 bytes of boot written
avrdude: verifying boot memory against xboot-boot.hex:
avrdude: load data boot data from input file xboot-boot.hex:
avrdude: input file xboot-boot.hex auto detected as Intel Hex
avrdude: input file xboot-boot.hex contains 4496 bytes
avrdude: reading on-chip boot data:
avrdude: verifying ...
avrdude: 4496 bytes of boot verified
avrdude: reading input file "0xFF"
avrdude: writing fuse0 (1 bytes):
avrdude: 1 bytes of fuse0 written
avrdude: verifying fuse0 memory against 0xFF:
avrdude: load data fuse0 data from input file 0xFF:
avrdude: input file 0xFF contains 1 bytes
avrdude: reading on-chip fuse0 data:
avrdude: verifying ...
avrdude: 1 bytes of fuse0 verified
avrdude: reading input file "0x00"
avrdude: writing fuse1 (1 bytes):
avrdude: 1 bytes of fuse1 written
avrdude: verifying fuse1 memory against 0x00:
avrdude: load data fuse1 data from input file 0x00:
avrdude: input file 0x00 contains 1 bytes
avrdude: reading on-chip fuse1 data:
avrdude: verifying ...
avrdude: 1 bytes of fuse1 verified
avrdude: reading input file "0xBE"
avrdude: writing fuse2 (1 bytes):
avrdude: 1 bytes of fuse2 written
avrdude: verifying fuse2 memory against 0xBE:
avrdude: load data fuse2 data from input file 0xBE:
avrdude: input file 0xBE contains 1 bytes
avrdude: reading on-chip fuse2 data:
avrdude: verifying ...
avrdude: 1 bytes of fuse2 verified
avrdude: reading input file "0xFE"
avrdude: writing fuse4 (1 bytes):
avrdude: 1 bytes of fuse4 written
avrdude: verifying fuse4 memory against 0xFE:
avrdude: load data fuse4 data from input file 0xFE:
avrdude: input file 0xFE contains 1 bytes
avrdude: reading on-chip fuse4 data:
avrdude: verifying ...
avrdude: 1 bytes of fuse4 verified
avrdude: reading input file "0xEB"
avrdude: writing fuse5 (1 bytes):
avrdude: 1 bytes of fuse5 written
avrdude: verifying fuse5 memory against 0xEB:
avrdude: load data fuse5 data from input file 0xEB:
avrdude: input file 0xEB contains 1 bytes
avrdude: reading on-chip fuse5 data:
avrdude: verifying ...
avrdude: 1 bytes of fuse5 verified
avrdude done.  Thank you.
</pre>

* At this point the red PWM LED should light, perhaps somewhat dimly, as it's PWMing.

**[VERIFIED PROGRAMMING]**

##Connect to Board
To connect Coolterm to the board:
* Hit `Disconnect` if connection to previous board has not already been disconnected.
* In the `Options`, `Serial Port` dialog box hit `Re-Scan Serial Ports`. Select the one that looks like `usbserial-DA00xxxx` on OSX, or a new COM-xx port on Windows. (Note: There is a bug on Windows that prevents the com port from incrementing past 255. We have a fix for that. Ask us if you need it).
* Connect to the board using the `Connect` button. 
![Coolterm-connect](https://farm6.staticflickr.com/5489/14632997114_9190193f99_o_d.jpg)
* You should be able to enter in one or more ?'s and see something like below. The TinyG firmware takes about 4 seconds to start up, so it will be unresponsive until then.

<pre>
X position:          0.000 mm
Y position:          0.000 mm
Z position:          0.000 mm
A position:          0.000 deg
Feed rate:           0.000 mm/min
Velocity:            0.000 mm/min
Units:               G21 - millimeter mode
Coordinate system:   G54 - coordinate system 1
Distance mode:       G90 - absolute distance mode
Feed rate mode:      G94 - units-per-minute mode (i.e. feedrate mode)
Motion mode:         G80 - cancel motion mode (none active)
Machine state:       Ready
tinyg [mm] ok> 
</pre>

**[VERIFIED USB CONNECTION]**

* Press the RESET button and verify that the SpDIR LED flashes about 12 times then returns the PWM LED 

  You should see the startup strings below. <pre>
{"r":{"fv":0.970,"fb":435.24,"hp":1,"hv":8,"id":"3X3566-HUR","msg":"Initializing configs to default settings"},"f":[1,15,0,3266]}
{"r":{"fv":0.970,"fb":435.24,"hp":1,"hv":8,"id":"3X3566-HUR","msg":"SYSTEM READY"},"f":[1,0,0,7259]}
</pre>

**[VERIFIED BOOT LOADER]**

##Run Board Functional Tests

In Coolterm:
* Enter `$test=1` followed by carriage return to start the on-board tests. _Please note that (at least on the mac) Coolterm may not always accept the first character ($) and you may need to type it twice._

* The first phase of the test lights the output bits for about 1 second each. Sequence is:
 * Spindle ON LED lit (I need to fill this in when I'm in front of the board. I might fix this to make is easier to watch, too)
 * Spindle DIR LED lit
 * Spindle ON and DIR LED off
 *....
**[VERIFIED OUTPUTS]**

* Next the motors should become active for about 60 seconds, during which the following should be verified.
 1. Motor 1 turns clockwise at high speed for about 2 seconds, CCW for about 2 seconds and stops with the flag in the starting position (12:00). One or more four green LEDs D9, D10, D11, D12 should be lit and/or flashing during this and other motor movement operations. Also, the terminal should be displaying position information during motor movements. The values in [brackets] indicate the current draw that should not be exceeded during each move. [500 ma] _Note: I will adjust these numbers when I can sit down in front of the tester_
 1. Motor 2 does the same [500 ma]
 1. Motor 3 does the same [500 ma]
 1. Motor 4 does the same [500 ma]
 1. All four motors do the same simultaneously [800 ma]
 1. All four motors turn clockwise at low speed for about 2 seconds, reverse for about 2 seconds and stop with the flags in the 12:00 starting positions [800 ma]
 1. A final short move on motor 1 indicates that the test sequence is complete (not all green LEDs will light)

**[VERIFIED MOTOR DRIVERS]**

* **Turn off bench power** and wait until the blue power LED (D2) is completely off before removing the board. A Windows "Serial Port Disconnected Error" is normal. Click OK to ignore. Ignore any other Windows errors that may occur at this point.
* Verify the blue power LED (D2) is completely off before removing UUT as so:
 1. Disconnect USB cable
 1. Unscrew the 2 hex standoffs
 1. Remove the UUT

You can now go back to the [per board instructions](#per-board-instructions) for the next board.