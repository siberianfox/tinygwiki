#Test Jigs and Procedures
The following are links to  all information related to test jigs and test procedures

* [TinyGv8 Production Test Instructions for Revision 1 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-1-Tester)
* [TinyGv8 Production Test Instructions for Revision 2 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-2-Tester)
* [Beaglebone Black AVR Programmer page](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-AVR-Programmer)
* [BeagleBone Black Test Rig Operation](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation)
* [TinyGv9j Manual Programming and Test Instructions](https://github.com/synthetos/TinyG/wiki/TinyGv9j-Manual-Programming-and-Test-Instructions)

#Building Test Jigs
The rest of this page is a collection of random notes on building test jigs. Mostly it's a record of mistakes to be avoided.

### Pogo Board
* Design a test board (pogo board)
  * Use the layout of the UUT to line up mounting holes and test points
    * Account for all test points and mounting holes (for alignment)
    * Use same components as UUT to manage BOM (where possible)
    * Provide indicator LEDs for all voltages, including on-board ones
  * Use IDI socketed pogo pins (exclusively). Expensive but worth it
  * Use IDI Size 3 preferentially - 0.125" centers
    * R-3-SC receptacles (sockets)
    * S-3-H-4-G waffle head
    * S-3-A-4-G cup head
    * S-3-B-7-G spear
  * Use 0.100" only for those things that require tighter spacing
    * R-100-SC receptacles (sockets)
    * S-100-WO-8-G-S crown head
    * S-100-A-6.7-G cup head
    * S-100-J-6.7-G headless radius
    * S-100-B-6.7-G headless spear
    * S-100-AP-6.7-G-S headless sharp chisel
  * Pogo holes are 0.063"
  * Mounting holes are 0.125" ID, 0.250" clearance (4/40 hw)
  * Manufacture tester PCB using 0.93" FR4

###Spacer Board
* Lay out a spacer board (Diptrace) using the pogo board layout file
  * Holes for 0.125" pogos are 0.095" (clears barrel; stops on annular ring)
  * Holes for 0.100" pogos are 0.067" (ditto)
  * Holes for 4-40 mounting HW are 0.125"
* Export the drill file as a DXF for laser cutting
* Laser cut a spacer plate of 1/16" clear plexi to line up the pogo sockets
* Grind down 1/4" diameter X 3/4" length 4-40 female-female aluminum hex spacers to 0.700 tall. Ensure there is enough 4-40 thread on the ground end.

###Assembly
* First assemble all electronics before attaching pogo pins 
  * e.g. regulators, connectors, LEDs, switches...
  * Test as much as possible before assembling pogos
* Attach pogo receptacles to tester board (tricky)
  * Insert receptacles into all holes to be filled
  * Position spacer plate roughly over holes
  * Loosely insert pogos through plate holes to line up pogo receptacles
  * Carefully work the plate onto the pogos
    * Stop at the annular detent ring
    * Don't push too hard or you will crack the plate
  * Screw in at least 2 of the 0.700" standoffs to get pogos to 90 degrees
    * IMPORTANT - ensure pogos are at right angles to the board
  * Solder pogos
    * Solder only on the side with the trace to make removal easier (if you eventually need it)
    * Do not flex the sockets once they are soldered. They will break off at the collar
