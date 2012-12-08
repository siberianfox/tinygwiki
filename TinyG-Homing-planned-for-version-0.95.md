The following describes the function of homing cycles and related Gcode for version 0.95. 

There seems to be no standard way to do homing, and machine variations complicate matters. TinyG's homing behaviors are adapted from [Peter Smid's CNC Programming Handbook, version 2](http://books.google.com/books?id=JNnQ8r5merMC&lpg=PA444&ots=PYOFKP-WtL&dq=Smid%20version3&pg=PA447#v=onepage&q=Smid%20version3&f=false) and [LinuxCNC](http://www.linuxcnc.org/docview/html/config_ini_homing.html).

Return to Home can be carried out by using the G28 and G28.1 commands: 
* G28 - Return to Zero: Return to machine zero at the traverse rate through an intermediate point
* G28.1 - Reference Axes: Reset machine coordinates to the homing switches

Some limitations / constraints in TinyG homing as currently implemented:
* The homing sequence is fixed and always starts with the Z axis (if requested). The sequence runs ZXYABC (but skipping all axes that are not specified in the G28.1 command)
* Supports a single home position. I.e. it does not support multiple-homes such as used by dual pallet machines and other complex machining centers

## Switches
### Switch Port
TinyG has 8 switch pin pairs and a 3.3v take-off pair located on the J13 jumper next to the reset button. The switch pairs are labeled:
* Xmin
* Xmax
* Ymin
* Ymax
* Zmin
* Zmax
* Amin
* Amax

For each pair, the pin closest to the board edge is the ground, the pin next to it (labeled in silkscreen) is the switch input. The inputs are 3.3v logic inputs and **must not have 5v applied to them or you will burn out the inputs**. The inputs are tied high - with strong pullups for v7 boards and on-chip weak pullups for earlier boards. 

Two 3.3v output pins are made available for opto-coupled and other powered switch options, but TinyG does not currently support this. It should work but you will need to be careful not to damage the inputs. If you draw the 3.3v do not pull more than 30 ma.

### Switch Wiring
To connect a switch to an input pin simply wire the switch across the ground and the input. This applies to both normally open (NO) and normally closed (NC) switches. Either NO or NC switches may be used, but all switches must be of the same type. We recommend using NC switches for better noise immunity. 

Wire a single switch to each axis that will be part of homing. The following configuration is typical for most milling machines and 3D printers:
* Connect to:
*     Xmin - X homing switch - on the left of the machine
*     Ymin - Y homing switch - at the front of the machine
*     Zmax - Z homing switch - at the top of the Z axis travel

The unused inputs may be wired as axis limit switches (kill) or left unused. If you wire limits you should connect the switch to its proper axis, and not connect multiple switches to an input. Adding limit switches would add these three switches to the example above:
* Xmax - X limit switch - on the right of the machine
* Ymax - Y limit switch - at the back of the machine
* Zmin - Z limit switch - at the bottom of the Z axis travel

This is necessary to proper function if one of the switches is closed during startup. The A inputs (if otherwise unused) can also be used as a machine kill.

### Switch Configuration
The following settings are used for switch configuration.

* **$ST** Switch Type - sets the type of switch used by the entire machine - 0=NO, 1=NC.
* **$XSN** X Minimum Switch Mode
* **$XSX** X Maximum Switch Mode
* **$YSN** Y Minimum Switch Mode
* **$YSX** Y Maximum Switch Mode
* **$ZSN** Z Minimum Switch Mode
* **$ZSX** Z Maximum Switch Mode
* **$ASN** A Minimum Switch Mode
* **$ASX** A Maximum Switch Mode

Modes:
* 0=disabled - Switch closures will have no effect. Unused switch pins must be disabled.
* 1=homing only - Switch is active during homing but has no effect otherwise
* 2=homing and limits - Switch is active during homing and acts as kill switch during normal operation. 
* 3=limits only - Switch is not active in homing but will act as a kill switch during normal operation.

It is important to configure all switch pins (all 8) even if you are not using them. Configure all unused pins as Disabled. Otherwise NC configurations will not work.

## Homing Configuration Settings

The following per-axis settings are used by homing. Substitute any of XYZABC for the 'x', below. The use of the settings is described in G28.1, below. See [Configuring Homing Settings](http://www.synthetos.com/wiki/index.php?title=TinyG:Configuring#Homing_Settings) for how to set these. 

* **$xTM** Travel Maximum - travel limit for search phase
* **$xSV** Homing Search Velocity - velocity for initially finding the homing switch. Set negative for travel in negative direction (towards minimum switch), positive for travel in positive direction
* **$xLV** Homing Latch Velocity - velocity for homing second pass (latching phase). The same positive and negative rules apply
* **$xLB** Homing Latch Backoff -amount to back off switch prior to latch operation
* **$xZB** Homing Zero Backoff - machine coordinate system zero position defined as backoff (offset) from homing switch 

## G28 - Return to Home
G28 will move the machine to the home coordinates through an intermediate point, with the home position detemined by the latest G28.1 cycle. Movement will occur at the traverse rate (G0 rate). Format is: 
<pre>G28 X0 Y0 Z0 A0 B0 C0</pre> 
G28 will move to coordinates for any specified axis: axes that are not specified are ignored (not moved). The axis value is the intermediate point for that axis. 

For example, G28 X10 Y0 will move to zero in the XY plane without affecting the Z or ABC axes. The movement will pass through point (10,0). Set all values to 0 for a homing move without an intermediate way point. A G28 command must have at least one axis word and is only valid if the system has been previously homed using a G30.

## G28.1 - Reference Axes (Homing Cycle)
G28.1 is used to home to physical home switches. G28.1 will home to a switch then set the machine zero for that axis (absolute zero) at an offset from that switch location. Format is: 
<pre>G28.1 X0 Y0 Z0 A0 B0 C0</pre> 
If an axis is not present in the G28.1 command then that axis is ignored and it's zero value is not changed.

For example. G28.1 X0 Y0 will home the X and Y axes only. The values provided for X and Y don't matter, but something must be present.

### Homing Operation
* The homing sequence progresses through each axis provided in the G28.1 block in turn - i.e. it does not home on multiple axes simultaneously. 
** Axes are always executed in order of ZXYABC. The order they occur in the G28.1 block has no effect.<br> 
* Homing begins by checking the pre-conditions for homing: 
** $xSV homing-search-velocity must be non-zero for the axis to be processed. Set negative to travel to the minimum switch, positive to find the maximum switch.<br> 
* Homing begins by testing the homing switch for that axis. If a switch is tripped the axis will back off a distance from the switch as defines in the Latch Backoff value ($xLB). Backoff will always be in the opposite direction of the search.
* Once the switch is clear a homing move of length travel max ($xTM) is executed in the negative direction of the search velocity ($xSV). The move executes until the homing switch is hit. For example: A homing move in X with X-axis velocity of -1200mm/min and 400mm travel limit would move G1 F1200 X-400 in relative coordinates. To move to the opposite end of the machine a positive velocity would be configured (and presumably the limit-max switch would be hit). 
* Once the switch is hit a backoff is performed as described above. 
* Once backoff occurs the latch cycle is initiated if the homing-latch-velocity is non-zero. The latch-velocity must have the same sign as the homing-search-velocity. The axis moves onto the switch at the homing-latch-rate and a backoff to $xHZ is performed at the latch rate. The homing cycle advances to the next axis to be processed. 
* Once all axes are processed the affected axes are moved to the absolute home location (machine zero).&nbsp;
* At this point the homing state will indicate that the machine has been homed.&nbsp;<br>

## Dual Gantry Homing (Not Yet)
Dual gantry operation differs somewhat from single gantry homing as specified above. Dual gantry operation assumes each of the two motors in the gantry has its own limit switch that can be read independently. It also assumes that the axis is not racked so much that it cannot move. If this is the case the machine must be manually squared so that the axis can move before starting the homing operation. &nbsp;&nbsp; 

* The homing sequence progresses normally through each axis but executes the following if a dual gantry axis is detected. Detection is performed by looking at the motor mapping configs: $1MA - $4MA&nbsp; 
* The standard pre-conditions apply<br> 
* Dual gantry homing begins by testing the homing switches for the dual axis. if either switch is tripped the other motor is advanced at the latch rate until that switch is also hit. "Advance" is actually the negative of the homing-travel for that axis (i.e. movement will be in the opposite direction specified by the latch rate sign). Once the second switch is hit a backoff is executed to the machine zero offset value. 
* A search move is initated for both motors. The search ends when one of the switches is hit. 
* The second switch is closed as per above, and a backoff is executed.<br> 
* A latch operation similar to the single gantry case is performed. 
* The dual axis is now homed. Control is returned to the standard homing operation, above<br>

## G27 - Verify Home
[NOT IMPLEMENTED - INCLUDED FOR FUTURE REFERENCE ONLY] <br>G27 is used to verify table position and perform a Reset if table position tolerances are beyond specification. G27 will Home to a switch, record the switch location, and calculate the difference from this measured Home Location to the set Home Position. G27 should only be used after the machine has been Homed using G30. No Home Offset is performed during the G27 call.<br>Syntax of G27 is... G27 X0.001 Y0.002 Z0.003 F10<br>Where the axis values define the acceptable tolerance for table position error. The example above will Home all 3 axes simultaneously to the Home Switches. G27 is only available when doing a Hard Home to physical switches. If an axis is not called out in the G27 command, then that axis is ignored during execution. For example, G27 X0.001 Z0.005 will only home/verify the XZ axes. The axis value defines the acceptable tolerance. In the example, the X axis tolerance is set to 0.001. If the table position is off be more than 0.001 then the G27 command will put the machine in Reset.<br>