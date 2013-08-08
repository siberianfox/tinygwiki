## Background and Shorthand
The following shorthand is used in these instructions:

	Term | Description
	-----|--------------
	DUT | Device Under Test. They TinyGv8 board that is being programmed and tested
	BBB | Beaglebone Black. The single-board computer located on the left of the test rig. See details below
	AVRISP | The blue Atmel programmer connected to the BBB. It has a USB connector and a programming header
	TESTER | The large blue board with four mounting standoffs, 18 pogo pins, and the wired motor and power connectors
	DUT POWER | The switch that supplies power to the DUT is located on the right hand side of the test rig


**BeagleBone Black (BBB) Details**
* The BBB is connected to 5v power via the wall power supply and the barrel jack on the front right corner of the BBB. Unlike DUT POWER which is cycled for each board, the BBB power remains on for the entire test run.
* The BBB has 4 indicator lights located on the left front of the board. These are used as status indicators.
* The BBB reset button is the small button at the left front labeled RESET and S1. It is located to the left of the indicator LEDs and directly below the mounting nut. 
* When the BB is reset it takes 30 - 40 seconds to boot and become available. During this time the 4 indicator lights blink. When reset is complete the right-most indicator light should be ON and the other three OFF.

## Program and Test Instructions Using Laptop Based Tester
### Setup Test Rig 

These steps only need to be completed once at the start of a test run. 

![Tester](http://farm6.staticflickr.com/5329/9458736352_65e87354f9_b_d.jpg)

* **SETUP STEP 1** Inspect the test rig and verify against the picture above
 1. Verify there is one BBB board on the test rig
 1. Verify there is one TESTER board with 18 serrated head pogo pins loaded into the pogo sockets
 1. Verify there is an Atmel AVRISP programmer plugged into the USB jack in the rear of the BBB
 1. Verify that you have at least two 1 inch 4/40 hex standoffs available to secure the DUT to the tester
* **SETUP STEP 2** Turn off DUT POWER using switch on right side of test jig and connect the AC power cord
* **SETUP STEP 3** Plug in 5v wall unit and apply power to BBB board via the barrel jack located on the front, right side. The blue PWR LED next to the barrel connector should be lit.
* **SETUP STEP 4** Hit reset on the BBB. Reset will take 30-40 seconds to complete. After a brief pause the 4 indicator lights should flash. Reset is complete when the right-most blue indicator LED (of the 4) is lit and the other three are not lit.
 1. Verify the green LED inside the AVRISP is lit (not flashing). This verifies USB connection between the BBB and the AVRISP.
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
* **STEP 9** Type the following into the terminal window: **$test=1** followed by RETURN. The motors should become active for about 60 seconds, during which the following should be verified.
 1. Motor 1 turns clockwise at high speed for about 2 seconds, CCW for about 2 seconds and stops with the flag in the starting position (12:00). All four green LEDs D9, D10, D11, D12 should be lit and/or flashing during this and other motor movement operations. Also, the terminal should be displaying position information during motor movements.
 1. Motor 2 does the same
 1. Motor 3 does the same
 1. Motor 4 does the same
 1. All four motors do the same simultaneously
 1. All four motors turn clockwise at low speed for about 2 seconds, reverse for about 2 seconds and stop with the flags in the 12:00 starting positions
 1. An LED sequence will follow, verifying operation of LEDs D5, D6 and D8. D7 should already be dimly lit after programming and throughout the test sequence.
 1. A final short move on motor 1 indicates that the test sequence is complete (not all green LEDs will light)
* **STEP 10** Turn off DUT POWER and wait until the blue power LED (D2) is completely off before proceeding to Step 11.
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