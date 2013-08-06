## Background and Shorthand
In order to keep these instruction easier to read the following shorthand is used

	Term | Description
	-----|--------------
	UUT | Unit Under Test. They TinyG board that is being programmed and tested
	BBB | Beaglebone Black. The single-board computer located on the left of the test rig
	AVRISP | The blue Atmel programmer connected to the BBB. It has a USB connector and a programming header
	tester | The large blue board with four mounting standoffs, 18 pogo pins, and the wired motor an power connectors
	UUT power | The UUT power switch is located on the right hand side of the test rig


BeagleBone Black (BBB) Details

* The BBB is connected to 5v power via the wall power supply and the barrel jack on the front right corner of the BBB
* The BBB reset button is the small button at left of the front of the BBB. The reset button us directly below the mounting nut and labeled as RESET and S1. When the BB is reset it takes 30 - 40 seconds to boot and become available.
* The BBB has 4 indicator lights located on the left front of the board. These are used as status indicators.

## Program and Test TinyGv8 Board Using BeagleBone Black Programmer and Laptop Based Tester
### Setup Test Rig 

These steps only need to be completed once at the start of a test run. 

* **SETUP STEP 1** Inspect the test rig and verify against picture ___
 1. Verify there is one BBB board on the test rig
 1. Verify there is one tester board with 18 serrated head pogo pins loaded into the pogo sockets
 1. Verify there is an Atmel AVRISP mkII programmer plugged into the USB jack in the rear of the BBB
 1. Verify that you have at least two 1 inch 4/40 hex standoffs available to secure the UUT to the tester
* **SETUP STEP 2** Turn off UUT power using switch on right side of test jig and connect the AC power cord
* **SETUP STEP 3** Plug in 5v wall unit and apply power to BBB board via the barrel jack located on the front, right side
* **SETUP STEP 4** Hit reset on the BBB. Reset will take 30-40 seconds to complete. Reset is complete when the right-most blue indicator LED (of the 4) is lit and the other three are not lit
* **SETUP STEP 5** Align motor flags so they all point vertically (12:00 position)

Setup is now complete

### Instructions for Each TinyGv8 Board
Run these steps for each board to be programmed and tested

* **STEP 1** Turn off UUT power using switch on right side of test jig
* **STEP 2** Turn off UUT power using switch on right side of test jig

	2 | Fasten board under test to top of tester. Make sure POGO pins make contact. Secure with 2 4/40 standoffs provided
	3 | Connect programming header to J10. Red polarity marking is towards the white dot next to "PDI"
	4 | Turn on board power using switch on right side of test jig
	5 | Press PROGRAM button on Beaglebone. The green LED inside the AVRISP should flash. If programming is successful the LED on the case will turn from orange to green. The TinyG board will flash D6 RED LED about 10 times, then light 4 GREEN LEDS D9 - D12 in sequence. These will turn off after about 2 seconds  
	6 | Disconnect the J9 programming header
	7 | 
	8 | 
	9 | 
	2 | 
	2 | 
	2 | 
	2 | 
	2 | 
	2 | 


## Program and Test TinyGv8 Board Using BeagleBone Black Programmer and Tester
### Prepare Test Rig 
These steps only need to be completed once at the start of a test run. 

	Step | Instruction
	-----|--------------
	1 | Inspect the test rig. Make sure there are two BeagleBone Black (BBB) boards on the tester and one tester base board with 18 serrated head pogo pins,
	2 | Turn off tester power using switch on right side of test jig
	3 | Plug in 5v wall units and apply power to BBB boards on left of tester
	4 | Hit reset on both BBB boards. This will take 30-40 seconds to complete. Reset is complete when the right-most a blue LED in the row of 4 is lit and the other three are not lit
	5 | Align motor flags so they all point vertically (12:00 position)
 

### Instructions for Each TinyGv8 Board
Run these steps for each board to be programmed and tested

	Step | Instructions 
	-----|--------------
	1 | Turn off tester power using switch on right side of test jig
	2 | Fasten board under test to top of tester. Make sure POGO pins make contact. Secure with 2 4/40 standoffs provided
	3 | Connect programming header to J10. Red polarity marking is towards the white dot next to "PDI"
	4 | Turn on board power using switch on right side of test jig
	5 | Press PROGRAM button on Beaglebone. The green LED inside the AVRISP should flash. If programming is successful the LED on the case will turn from orange to green. The TinyG board will flash D6 RED LED about 10 times, then light 4 GREEN LEDS D9 - D12 in sequence. These will turn off after about 2 seconds  
	6 | Disconnect the J9 programming header
	7 | 
	