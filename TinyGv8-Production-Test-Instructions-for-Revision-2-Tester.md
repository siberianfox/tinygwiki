## Background and Shorthand
The following shorthand is used in these instructions:

	Term | Description
	-----|--------------
	UUT | Unit Under Test. They TinyGv8 board that is being programmed and tested
	HOST | Tester's host computer used to program the board and start the tests via USB
	AVRISP | The blue Atmel programmer. It has a USB connector and a programming header
	TESTER | The large blue board with four mounting standoffs, 18 pogo pins, and the wired motor and power connectors
	POWER SUPPLY | The bench power supply providing current-limited 24 volts for testing

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

#Prep for a Test Run
These steps need to be done at the start of each test run

* Boot the host computer
* Connect the AVRISP (blue thing) and the USB cable for the TinyG board to the host USB ports
  * Verify the green LED inside the AVRISP is lit (not flashing). This verifies USB connection between the BBB 

* Start Coolterm
* Select the `OPTIONS` dialog and set up the Serial Port and Terminal windows as shown. You don't have a board plugged in yet so you won't see the usbserial-xxxxx option. Just set the baud rate and flow control.

![Coolterm-connect](https://farm6.staticflickr.com/5489/14632997114_9190193f99_o_d.jpg)

![Coolterm-serial](https://farm6.staticflickr.com/5564/14631893761_69be22d2dc_o_d.jpg)

![Coolterm-terminal](https://farm4.staticflickr.com/3924/14448727147_292950c987_o_d.jpg)

* Open up a command line terminal window (e.g. Terminal on OSX, Command on Win) and navigate to your Avrdude directory.

#Per-Board Instructions
##Mount and Prep Board
* With the bench supply off, affix the UUT board onto the tester. Make sure all pogos connect, and secure with two hold-down standoffs.
* Plug in the programmer (blue thing) and the USB port to the board
![Tester-with-board](https://farm4.staticflickr.com/3855/14654991973_2ace1fbd3d_o_d.jpg)

* Verify that the potentiometers are in the 50% position:
![Board-pots](https://farm3.staticflickr.com/2933/14631844071_55065a3316_o_d.jpg)

* Place a jumper on the +12v position on J12 
![Board-jumper](https://farm4.staticflickr.com/3904/14635093995_0914d6625e_o_d.jpg)

* Turn on bench supply power. 
* Verify that the blue power LED is lit (VERIFY 3.3v POWER)

##Program the Board
* Go to the terminal window. Copy and paste the programming string from the ProgrammingString.txt file of from here: `avrdude -q -c avrisp2 -p atxmega192a3 -P usb -u -U flash:w:tinyg.hex -U boot:w:xboot-boot.hex -U fuse0:w:0xFF:m -U fuse1:w:0x00:m -U fuse2:w:0xBE:m -U fuse4:w:0xFE:m -U fuse5:w:0xEB:m`

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

* At this point the red PWM LED should light, perhaps somewhat dimly, as it's PWMing. (VERIFY PROGRAMMING)

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

* Press the RESET button and verify that the SpDIR LED flashes about 12 times then returns the PWM LED (VERIFY BOOT LOADER). 

  You should see the startup strings below. <pre>
{"r":{"fv":0.970,"fb":435.24,"hp":1,"hv":8,"id":"3X3566-HUR","msg":"Initializing configs to default settings"},"f":[1,15,0,3266]}
{"r":{"fv":0.970,"fb":435.24,"hp":1,"hv":8,"id":"3X3566-HUR","msg":"SYSTEM READY"},"f":[1,0,0,7259]}
</pre>

##Run Board Tests

In Coolterm:
* Enter `$test=1` to start the on-board tests



* **SETUP STEP 1** Inspect the test rig and verify against the picture above
 1. Verify that you have at least two 1 inch 4/40 hex standoffs available to secure the DUT to the tester
* **SETUP STEP 2** Turn off DUT POWER using switch on right side of test jig and connect the AC power cord
* **SETUP STEP 3** Plug in 5v wall unit and apply power to BBB board via the barrel jack located on the front, right side. The blue PWR LED next to the barrel connector should be lit.
* **SETUP STEP 4** Hit reset on the BBB. Reset will take 30-40 seconds to complete. After a brief pause the 4 indicator lights should flash. Reset is complete when the right-most blue indicator LED (of the 4) is lit and the other three are not lit.
and the AVRISP.
* **SETUP STEP 5** Align motor flags so they all point vertically - i.e. the 12:00 position. Note: Do not attempt to position flags if green lights are lit on a DUT, as they are locked.
* **SETUP STEP 6** Setup the laptop for testing using these steps:
 1. Turn on laptop power using the power button at the top left 
 1. Once the computer has booted (Windows started) click on the CoolTerm icon to start CoolTerm. Ignore the warning that there are no serial ports. There will be once you connect a DUT to the USB (later). Click OK to ignore (Perhaps twice).
 1. Connect a USB cable to the laptop (not the DUT, yet)

Setup is now complete. CoolTerm will be used for every DUT tested.

### Instructions for Each TinyGv8 Board
Run these steps for each board to be programmed and tested. Each DUT should take between 2 and 3 minutes to complete.

![DUT](http://farm4.staticflickr.com/3783/9458735036_caa7efb156_b_d.jpg)

* **STEP 1** Turn off DUT POWER using switch on right side of test jig
* **STEP 2** Fasten the DUT under test to top of tester. Make sure POGO pins make contact. Secure with two 4/40 standoffs though the mounting holes on the right-hand side of the DUT
* **STEP 3** Connect the AVRISP programming header to DUT J10 (PDI) located in the upper left hand corner of the board. The red polarity marking should be towards the white dot labeled "PDI"
* **STEP 4** Turn on DUT POWER power using switch on right side of test jig
 1. Verify that the DUT blue LED D2 is lit 
* **STEP 5** Press PROGRAM button on the BBB. Programming takes about 10 seconds, during which time the green LED inside the AVRISP should flash and the LED on the outside of the case should turn orange. If programming is successful the following should occur:
 1. The AVRISP LED inside the case will turn solid green
 1. The AVRISP LED on the case will turn from orange to green
 1. The TinyG board will flash D6 RED LED about 10 times, then light 4 GREEN LEDS D9 - D12 in sequence
 1. LED D7 (red) will be dimly lit
 1. Green LEDs D9-D12 will turn off after about 2 seconds
 1. If the above occurs the DUT is now programmed. If not, execute step 5R as in Alternate Steps
* **STEP 6** Disconnect the AVRISP programming header from J10 PDI 
* **STEP 7** Connect the USB from the laptop to the DUT. An installing message will appear in the lower left. Wait until it says "Your device is ready to use" before continuing.
* **STEP 8** Click on the Options button (if not already open) and follow these steps
 1. Click the "Re-Scan Serial Ports" button. There should be a COMx serial port named.
 1. Click OK to exit the Options window
 1. Click connect. Once connected there may be jibberish ASCII. This is OK.
 1. In the input bar at the bottom of the terminal window type ? followed by Carriage Return [CR] and verify that a status report followed by the "tinyg [mm] ok> " prompt is presented. See **Sample Status Report**, below. You may have to enter ? more than once before getting a response.
* **STEP 9** Type the following into the terminal window: **$test=1** followed by RETURN. (Once you have entered this command you can use the up arrow to "find" it and re-enter on subsequent tests). The motors should become active for about 60 seconds, during which the following should be verified.
 1. Motor 1 turns clockwise at high speed for about 2 seconds, CCW for about 2 seconds and stops with the flag in the starting position (12:00). All four green LEDs D9, D10, D11, D12 should be lit and/or flashing during this and other motor movement operations. Also, the terminal should be displaying position information during motor movements.
 1. Motor 2 does the same
 1. Motor 3 does the same
 1. Motor 4 does the same
 1. All four motors do the same simultaneously
 1. All four motors turn clockwise at low speed for about 2 seconds, reverse for about 2 seconds and stop with the flags in the 12:00 starting positions
 1. An LED sequence will follow, verifying operation of LEDs D5, D6 and D8. D7 should already be dimly lit after programming and throughout the test sequence.
 1. A final short move on motor 1 indicates that the test sequence is complete (not all green LEDs will light)
* **STEP 10** **Turn off DUT POWER** and wait until the blue power LED (D2) is completely off before proceeding to Step 11. A Windows "Serial Port Disconnected Error" is normal. Click OK to ignore. Ignore any other Windows errors that may occur at this point.
* **STEP 11** Verify the blue power LED (D2) is completely off before removing DUT as so:
 1. Disconnect USB cable
 1. Unscrew the 2 hex standoffs
 1. Remove the DUT

You can now go back to Step 1 for the next DUT board.

####Sample Status Report
<pre>
Line number:         0
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
Motion mode:         G0  - linear traverse (seek)
Machine state:       Ready
tinyg [mm] ok> 
tinyg [mm] ok> 
</pre>

#### Alternate Steps

* **STEP 5R** Recover the BBB programmer by performing the following steps
 1. Verify the BBB has power applied. Verify the blue PWR LED next to the barrel connector should is lit
 1. Press the RESET button located at the left of the BBB. Wait 30-40 seconds. The right-most indicator light should light solid. The other 3 indicator LEDs should be off
 1. Verify the USB cable is properly connected between the BBB and the AVRISP. The USB A goes into the rear of the BBB. The USB B goes to the AVRISP
 1. Verify the green LED inside the AVRISP is lit solid. The LED on the AVRISP surface may be red or some other color