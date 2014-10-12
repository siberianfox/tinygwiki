#Current Test Jigs
This is a jump page for all information related to test jigs and test procedures

* [TinyGv8 Production Test Instructions for Revision 1 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-1-Tester)
* [TinyGv8 Production Test Instructions for Revision 2 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-2-Tester)
* [Beaglebone Black AVR Programmer page](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-AVR-Programmer)
* [BeagleBone Black Test Rig Operation](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation)
* [TinyGv9j Manual Programming and Test Instructions](https://github.com/synthetos/TinyG/wiki/TinyGv9j-Manual-Programming-and-Test-Instructions)

#Building Test Jigs
The rest of this page is a collection of random notes on building test jigs. Mostly it's a record of mistakes to be avoided.

## Making the pogo board
* Design and manufacture tester PCB from 0.93" FR4
  * Use socketed pogo pins (exclusively). IDI on Mouser. Expensive but worth it
  * Use 4/40 hw for standoffs - 0.125 ID holes, 0.250" clearance
  * Pogo holes 0.063" (see below)
* Laser cut a spacer plate of 1/16" clear plexi to line up the pogo sockets
* Use IDI Size 3 preferentially - 0.125" centers
  * Tester board holes 0.063"
  * Spacer plate holes 0.095"
  * Parts
    * R-3-SC receptacles (sockets)
    * S-3-H-4-G waffle head
    * S-3-A-4-G cup head
    * S-3-B-7-G spear
* Use 0.100" only for those things that require tighter spacing
  * Tester board holes 0.063"
  * Spacer plate holes 0.067"
  * Parts
    * R-100-SC receptacles (sockets)
    * S-100-WO-8-G-S crown head
    * S-100-A-6.7-G cup head
    * S-100-J-6.7-G headless radius
    * S-100-B-6.7-G headless spear
    * S-100-AP-6.7-G-S headless sharp chisel
* Assemble all electronics first before attaching pogo pins 
  * e.g. regulators, connectors...
* Attaching pogo receptacles to tester board
  * Insert receptacles into all holes to be filled
  * Position spacer plate roughly over holes
  * Insert pogos through plate to line up pogo receptacles with spacer plate holes
  * Carefully work the plate onto the pogos
    * Stop at the annular detent ring
    * Push too hard and will crack the plate
  * Before soldering - ensure first pogo is at right angle to the board!!! Very important!!!
  * Solder it and do another pogo at the far end, also at a right angle
  * Solder only on the side with the trace to make removal easier (if you eventually need it)
  * Do not flex the sockets. They will break off at the collar
