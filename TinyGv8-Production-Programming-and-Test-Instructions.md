## Background and Shorthand
The following shorthand is used in these instructions:

	Term | Description
	-----|--------------
	DUT | Device Under Test. They TinyGv8 board that is being programmed and tested
	BBB | Beaglebone Black. The single-board computer located on the left of the test rig. See details below
	AVRISP | The blue Atmel programmer connected to the BBB. It has a USB connector and a programming header
	TESTER | The large blue board with four mounting standoffs, 18 pogo pins, and the wired motor and power connectors
	DUT POWER | The switch that supplies power to the DUT is located on the right hand side of the test rig


BeagleBone Black (BBB) Details

* The BBB is connected to 5v power via the wall power supply and the barrel jack on the front right corner of the BBB. This is not DUT POWER and remains on for the entire test run.
* The BBB reset button is the small button at left of the front of the BBB. The reset button us directly below the mounting nut and labeled as RESET and S1. When the BB is reset it takes 30 - 40 seconds to boot and become available.
* The BBB has 4 indicator lights located on the left front of the board. These are used as status indicators.

## Program and Test Instructions Using Laptop Based Tester
### Setup Test Rig 

These steps only need to be completed once at the start of a test run. 

* **SETUP STEP 1** Inspect the test rig and verify against picture ___
 1. Verify there is one BBB board on the test rig
 1. Verify there is one TESTER board with 18 serrated head pogo pins loaded into the pogo sockets
 1. Verify there is an Atmel AVRISP programmer plugged into the USB jack in the rear of the BBB
 1. Verify that you have at least two 1 inch 4/40 hex standoffs available to secure the DUT to the tester
* **SETUP STEP 2** Turn off DUT POWER using switch on right side of test jig and connect the AC power cord
* **SETUP STEP 3** Plug in 5v wall unit and apply power to BBB board via the barrel jack located on the front, right side. The blue PWR LED next to the barrel connector should be lit.
* **SETUP STEP 4** Hit reset on the BBB. Reset will take 30-40 seconds to complete, during which time the 4 indicator lights should flash. Reset is complete when the right-most blue indicator LED (of the 4) is lit and the other three are not lit.
 1. Verify that the green LED inside the AVRISP is lit and solid (not flashing)
* **SETUP STEP 5** Align motor flags so they all point vertically (12:00 position)
* **SETUP STEP 6** Setup the laptop for testing using these steps:
 1. Turn on laptop power... (++++++ RILEY ++++++)
 1. connect a USB cable to the laptop (not the DUT, yet)
 1. start CoolTerm
 1. In the Options window select Baudrate to 115200. All other options are OK as defaulted. Hit OK to exit


Setup is now complete

### Instructions for Each TinyGv8 Board
Run these steps for each board to be programmed and tested. Each DUT should take between 2 and 3 minutes to complete.

* **STEP 1** Turn off DUT POWER using switch on right side of test jig
* **STEP 2** Fasten the DUT under test to top of tester. Make sure POGO pins make contact. Secure with 2 4/40 standoffs
* **STEP 3** Connect the AVRISP programming header to DUT J10. The red polarity marking should be towards the white dot labeled "PDI"
* **STEP 4** Turn on DUT POWER power using switch on right side of test jig
 1. Verify that the DUT blue LED D2 is lit (D7 red LED does not light, unlike in the photograph) 
* **STEP 5** Press PROGRAM button on the BBB. Programming takes about 10 seconds, during which time the green LED inside the AVRISP should flash and the LED on the outside of the case should turn orange. If programming is successful the following should occur:
 1. The AVRISP LED inside the case will turn solid green
 1. The AVRISP LED on the case will turn from orange to green
 1. The TinyG board will flash D6 RED LED about 10 times, then light 4 GREEN LEDS D9 - D12 in sequence
 1. LED D7 (red) will be dimly lit
 1. Green LEDs D9-D12 will turn off after about 2 seconds These will turn off after about 2 seconds
 1. If the above occurs the DUT is now programmed. If not, execute step 5R as in Alternate Steps
* **STEP 6** Disconnect the AVRISP programming header from J9 
* **STEP 7** Connect the USB from the laptop to the DUT
* **STEP 8** Connect the terminal emulator (CoolTerm) to the DUT by performing the following steps
 1. In the Options window hit "Re-Scan Serial Ports" and select ++++++++. Hit OK to exit
 1. Hit "Connect"
 1. In the terminal window hit Carriage Return [CR] a few times and verify the "tinyg [mm] ok> " prompt
* **STEP 9** Type the following into the terminal window: _$test=1_ followed by RETURN. The motors should become active for about 60 seconds, during which the following should be verified.
 1. Motor 1 turns clockwise at high speed for about 2 seconds, CCW for about 2 seconds and stops with the flag in the starting position (12:00). All four green LEDs D9, D10, D11, D12 should be lit during this and other motor movement operations.
 1. Motor 2 does the same
 1. Motor 3 does the same
 1. Motor 4 does the same
 1. All four motors do the same simultaneously
 1. All four motors turns clockwise at low speed for about 2 seconds, reverse for about 2 seconds and stop with the flags in the starting positions (12:00)
 1. An LED sequence will follow, during with the operation of LEDs D5, D6 and D8 should be verified. D7 should already be dimly lit after programming.
 1. A final short move on motor 1 indicates that the test sequence is complete
* **STEP 10** Turn off DUT POWER and wait until the blue power LED (D2) is completely off before proceeding to Step 11.
* **STEP 11** Verify the blue power LED (D2) is completely off before removing DUT as so:
 1. Disconnect USB cable
 1. Unscrew the 2 hex standoffs
 1. Remove the DUT

You can now go back to Step 1 for the next DUT board.

#### Alternate Steps

* **STEP 5R** Recover the BBB programmer by performing the following steps
 1. Verify the BBB has power applied. Verify the blue PWR LED next to the barrel connector should is lit
 1. Press the RESET button located at the left of the BBB. Wait 30-40 seconds. The right-most indicator light should light solid. The other 3 indicator LEDs should be off
 1. Verify the USB cable is properly connected between the BBB and the AVRISP. The USB A goes into the rear of the BBB. The USB B goes to the AVRISP
 1. Verify the green LED inside the AVRISP is lit solid. The LED on the AVRISP surface may be red or some other color