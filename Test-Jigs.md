# Test Jigs and Procedures
The following are links to  all information related to test jigs and test procedures

* [TinyGv8 Production Test Instructions for Revision 1 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-1-Tester)
* [TinyGv8 Production Test Instructions for Revision 2 Tester](https://github.com/synthetos/TinyG/wiki/TinyGv8-Production-Test-Instructions-for-Revision-2-Tester)
* [Beaglebone Black AVR Programmer page](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-AVR-Programmer)
* [BeagleBone Black Test Rig Operation](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation)
* [TinyGv9j Manual Programming and Test Instructions](https://github.com/synthetos/TinyG/wiki/TinyGv9j-Manual-Programming-and-Test-Instructions)

# Building Test Jigs
The rest of this page is a collection of random notes on building test jigs. Mostly it's a record of tricks learned the hard way and mistakes to be avoided.

### Parts and Tools Needed
* Parts
  * Pogo board (tester board)
  * Pogo pins and receptacles (see below)
  * 1/16" plexiglass (spacer plate)
  * 4-40 socket-head screws, 1/4" and 3/8"
  * 4-40 1/4" hex standoffs, 3/4" length, female-female
* Tools
  * Numbered drill set
  * Milling machine or drill press
  * Laser cutter

### Pogo Board
* Design the pogo board
  * Use the layout of the UUT to line up mounting holes and test points
    * Account for all test points and mounting holes (for alignment)
    * Use same components as UUT to manage BOM (where possible)
    * Provide indicator LEDs for all voltages, including on-board ones
    * Leave 1/4" or more from any through-hole part to the edge for easier mounting
    * Run as many traces as possible on the bottom of the board. At least try to make the trace that connects to the pogo pin socket be on the bottom (this simplifies assembly and re-work).
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

###Spacer Plate
* Lay out a spacer plate using the pogo board layout file (Diptrace)
  * Holes for 0.125" pogos are 0.093" ID (clears barrel; stops on annular ring)
  * Holes for 0.100" pogos are 0.067" ID (ditto)
  * NOTE: Test the laser cutter for exact ID - record the following:
    * Actual hole size used
    * Plastic used
    * Power and speed
  * Holes for 4-40 mounting HW are 0.125"
* Export Drill_Top as a DXF from Diptrace. This will require adding a border to the DXF
* Laser cut a spacer plate of 1/16" clear plexi to line up the pogo sockets
* Locate 1/4" diameter X 3/4" length 4-40 female-female aluminum hex spacers

###Assembly
* First assemble all electronics before attaching pogo pins
  * e.g. regulators, connectors, LEDs, switches...
  * Test as much as possible before assembling pogos
* Attach pogo receptacles to tester board (tricky)
  * Insert receptacles into the spacer plate. Should be a tight fit
  * Position spacer plate with receptacles over holes in tester board
  * Maneuver receptacles through holes in tester board
  * WARNING: Don't push too hard on the plate or you will crack it
  * Screw in at least 2 of the 3/4" standoffs to get pogos to 90 degrees
    * IMPORTANT - ensure pogos are at right angles to the board
  * Solder pogos
    * Be sure each receptacle is all the way inserted before soldering
    * Solder only on the side with the trace to make removal easier (if you eventually need it)
    * Do not flex the sockets once they are soldered. They will break off at the collar

###Mounting and Clamping
* What you will need
  * Base: 6" x 12" x 3/4" thick white HDPE, food service finish (rough, not smooth)
  * Plate: 1/4" clear plexiglass, cut to size approximately the size of the UUT dimensions
  * Spacer: 10-24 thick nylon spacers, 1-3/4" (typically 1/2" wide)
  * Clamp: De-Sta-Co 202UL clamp
  * Nylon 1-1/4" 4-40 socket head screws (about 6 or 8, depending) (McMaster Carr)
  * 10-24 1" bolts, washers and nuts (2x)
* Base
  * Mark and drill holes to self tap into the HDPE.
    * Drill #43 (0.089") for 4-40 self tapping
    * Drill #25 (0.149") for 10-24 self tapping
* Pressure Plate
  * Option 1: Cut the plate using the laser cutter
  * Option 2: cut the plate on a milling machine
    * Cut 1/4" clear plexiglass to size approximately the size of the UUT dimensions
    * Locate and mark De-Sta-Co clamp holes for 10/24 (use drill #9)
    * Locate and mark pressure points - for 4-40 nylon screws (use drill #43)
    * Drill holes and tap 4-40 while still in mill
  * Ream 4-40 holes with metal screws before use
