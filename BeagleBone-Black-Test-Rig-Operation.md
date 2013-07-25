## Board Programming Instructions
These must be done before operating the tester
Make sure the programmer is properly set up. See [Programmer Setup](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation#programmer-setup)

* Connect power to TinyG board. *** BE SURE TO OBSERVE CORRECT POLARITY ***
 * +24v line (yellow) is next to corner of board and board mounting hole
 * Ground (black) is next to capacitor
* Place jumper on left position of J12 - labeled +12v
* Connect PDI programming header to J10. Red wire should be on the side with the white dot

## Board Test Instructions
Make sure the tester is properly set up. See [Tester Setup](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation#tester-setup)

### One Time Steps

* Turn off the 24 volt power from the switch if not already off
* Plug in the 5volt power supply for the BeagleBone Black. 
 * This connects to the barrel connector on BeagleBone on the the left-side front of the tester.
 * Wait at least 30 seconds for the BBB to boot
* Confirm that the short USB connector connects from the A connector on the BBB to the B connector on the tester board.
 
### Steps For Each TinyG Board to Test


* Turn off board power using switch on tester.
 * Blue power LED on tester (next to power input terminal) should fade off, indicating it is safe to remove any test board if there is one already on the tester.
 * Do not unplug or turn off BeagleBone 5v power wall unit.
* Carefully align TinyG to test on threaded standoffs. Make sure all pogo pins make contact.
* tighten board using threaded standoffs

# Setup Instructions
## Tester Setup
Checklist. Perform in order.

* Turn power switch off first
* Unplug 5v wall power supply
* Place Beaglebone Black on mounting headers with Ethernet jack in cutaway
* Connect BBB USB host (A connector) to USB B connector on tester board (NOT on Tiny board)

## Programmer Setup