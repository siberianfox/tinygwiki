This page describes the function of homing cycles and related Gcode for version 0.95 and later versions. If you are new to this it's best to read these in the order listed:

* [Homing Commands and Operation](https://github.com/synthetos/TinyG/wiki/TinyG-Homing#homing-commands-and-operation)
* [Homing and Limits Setup](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting)
* [Homing and Limits Troubleshooting](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting)

#Homing Commands and Operation
##Overview
The term "homing" can have multiple, confusing meanings in CNC. In DIY and small CNC machines "homing the axes" often means setting the absolute machine coordinates (the G53 coordinate system) to a known zero location. Typically is done by running a "homing cycle" that locates Z maximum, X minimum, then Y minimum - in that order. Z is done first so that X and Y moves will clear any obstacles that might be on the work surface. 

In TinyG this is performed by running a G28.2 X0 Y0 Z0 command (The 0's are not used, but the X Y and Z words must have some arbitrary value). 

Other machine configurations may be set up to run differently, including not homing all axes, or setting an axis to an arbitrary coordinate location (see G28.3).  

In high-end CNC machines there is often no user-accessible homing cycle as machine zero is set at the factory and does not need to be set by the end user. See [Peter Smid's CNC Programming Handbook, version 2](http://books.google.com/books?id=JNnQ8r5merMC&lpg=PA444&ots=PYOFKP-WtL&dq=Smid%20version3&pg=PA447#v=onepage&q=Smid%20version3&f=false) for details. 

TinyG's homing cycle is modeled after [LinuxCNC](http://www.linuxcnc.org/docs/html/config/ini_homing.html). There are differences, so please rely on the TinyG documentation for setup.

TinyG homing uses these "non standard" Gcode functions: 

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G28.2 | _axes_ | Homing Sequence | Homes all axes present in command. At least one axis letter must be present. The value (number) must be provided but is ignored.
	G28.3 | _axes_ | Set Position | Set machine origins for axes specified. In this case the values are meaningful. This command is useful for zeroing in cases where axes cannot otherwise be homed (e.g. no switches, infinite axis, etc.) (See also G92 Offsets)

Some limitations / constraints in TinyG homing as currently implemented:
* The homing sequence is fixed and always starts with the Z axis (if requested). The sequence runs ZXYA (but skipping all axes that are not specified in the G28.2 command)
* Supports a single home position. I.e. it does not support multiple-homes such as used by dual pallet machines and other complex machining centers

###Return To Home Position
In addition to 

	Gcode | Parameters | Command | Description
	------|------------|---------|-------------
	G28 | _axes_ | Go to G28.1 position | Goes through intermediate point if _axes_ are present
	G28.1 | | Set position for G28 | Axes are not used and are ignored if present
	G30 | _axes_ | Go to G30.1 position | Goes through intermediate point if _axes_ are present
	G30.1 | | Set position for G30 | Axes are not used and are ignored if present

Example of use: 
* Go to an arbitrary position, e.g. G0 x100 y100 
* Send G28.1  - This will "remember" the absolute position. This position remains constant regardless of what coordinate system is in effect. 
* Then go to a gifferent place, e.g. G0 x50 y50
* Send G28  - The machine will return to x100 y100



## Switches
### Switch Port
TinyG has 8 switch pin pairs and a 3.3v take-off pair located on the J13 jumper next to the reset button.  The switch pairs are labeled:

	Pair  | Notes
	-----|-------------
	3.3v | This pair is located on the J13 connector closest to the corner of the board
	Xmin | Corresponding switch is typically positioned at left-most travel of the machine
	Xmax | Switch typically at right-most travel
	Ymin | Switch typically at front of machine 
	Ymax | Switch typically at rear of machine 
	Zmin | Switch typically at maximum height of Z travel
	Zmax | Switch typically at minimum height of Z travel or omitted
	Amin | Most of the time A is infinite and not homed. This position can be used for a machine kill
	Amax | Ditto


For each switch pair the pin closest to the board edge is the ground, the pin next to it is the switch input as labeled on the silkscreen. The inputs are 3.3v logic inputs and **must not have 5v applied to them or you will burn out the inputs**. The inputs are tied high - with strong pullups for v7 boards and on-chip weak pullups for earlier boards. 

Two 3.3v output pins are made available for opto-coupled and other powered switch options, but TinyG does not currently support this. It should work but you will need to be careful not to damage the inputs. If you draw the 3.3v do not pull more than 30 ma.

### Switch Wiring
To connect a switch to an input pin simply wire the switch across the ground and the input. This applies to both normally open (NO) and normally closed (NC) switches. Either NO or NC switches may be used, but all switches must be of the same type. We recommend using NC switches for better noise immunity. 

Wire a single switch to each axis that will be part of homing. The following configuration is typical for most milling machines and 3D printers:

	Pin  | Function    | Position on machine
	-----|-------------|-------------------------
	Xmin | X homing switch | on the left of the machine
	Ymin | Y homing switch | at the front of the machine
	Zmax | Z homing switch | at the top of the Z axis travel

####Limit Switches
The unused inputs may be wired as axis limit switches (kill) or left unused. If you wire limits you should connect the switch to its proper axis, and not connect multiple switches to an input. Adding limit switches would add these three switches to the example above:

	Pin  | Function    | Position on machine
	-----|-------------|-------------------------
	Xmax | X limit switch | on the right of the machine
	Ymax | Y limit switch | at the back of the machine
	Zmin | Z limit switch | at the bottom of the Z axis travel

The A inputs (if otherwise unused) can also be used as a limit.

When a limit switch is tripped the board is reset and will not exit until either a manual reset is pushed or a soft reset is sent via the serial interface. A soft reset is the <ctrl>x character.

### Switch Configuration
It is mandatory that the switch configuration settings match the physical switch configuration otherwise homing simply won't work. In the case of NC switches the entire machine may be rendered inoperative if these settings are not in alignment.

The following switch settings are supported:
* 0=Disabled - Switch closures will have no effect. Unused switch pins must be disabled.
* 1=Homing-only - Switch is active during homing but has no effect otherwise
* 2=Limits-only - Switch is not active in homing but will act as a kill switch during normal operation.
* 3=Homing-and-limits - Switch is active during homing and acts as kill switch during normal operation. 

The following settings are used for switch configuration.

	Setting | Description | Setting Example
	--------|-------------|------------------
	**$ST** | Switch Type | sets the type of switch used by the entire machine - 0=NO, 1=NC.
	**$XSN** | X Minimum Switch Mode | 3=limit-and-homing (See Modes, below)
	**$XSX** | X Maximum Switch Mode | 2=limit-only
	**$YSN** | Y Minimum Switch Mode | 3=limit-and-homing
	**$YSX** | Y Maximum Switch Mode | 2=limit-only
	**$ZSN** | Z Minimum Switch Mode | 0=disabled
	**$ZSX** | Z Maximum Switch Mode | 3=limit-and-homing
	**$ASN** | A Minimum Switch Mode | 0=disabled
	**$ASX** | A Maximum Switch Mode | 0=disabled

**IMPORTANT**
* It is important to configure all switch pins (all 8) even if you are not using them. Configure all unused switches as Disabled. Otherwise NC configurations will not work.

* An axis should only be configured for one homing switch - it should not have two. The homing switch position (min or max) may be configured as homing-only or homing-and-limit. If there is a second switch on the opposite end it should be either disabled or configured as limit-only.

## Homing Configuration Settings

The following per-axis settings are used by homing. Substitute any of XYZA for the 'x' in the table. The use of the settings is described in G28.1, below.

	Setting | Description | Notes
	--------|-------------|--------------
	**$xTM** | Travel Maximum | This axis parameter is used to limit travel during the search phase
	**$xJH** | Homing Jerk | Set this to stop quickly on switches. May need to be larger than the $xJM
	**$xSV** | Homing Search Velocity | Velocity for initially finding the homing switch
	**$xLV** | Homing Latch Velocity | Velocity for latching phase
	**$xLB** | Homing Latch Backoff | Distance to back off switch during latch and for clears
	**$xZB** | Homing Zero Backoff | Distance to back off switch before setting machine coordinate system zero 

## G28 / G28.1 - Return to Home
G28 will move the machine to the absolute coordinates set in G28.1, optionally through an intermediate point.  Movement will occur at the traverse rate (G0 rate). Format is: 
<pre>G28 X0 Y0 Z0 A0 B0 C0</pre> 
G28 will move to coordinates for any specified axis: axes that are not specified are ignored (not moved). The axis value is the intermediate point for that axis. 

For example, G91 G28 Z10 will move to a pre-set point in the XY plane. The tool will initially lift z by 10 mm (or inches); G91 is used to set relative mode for this command. 

G28.1 sets the G28 position to the current absolute coordinates. On reset this position is zeroed, so G28's will return to machine home.

## G30 / G30.1 - Return to Home
Works identically to G28 / G28.1

## G28.2 - Homing Sequence (Homing Cycle)
G28.2 is used to home to physical home switches. G28.2 will find the home switch for an axis then set machine zero for that axis at an offset from the switch location. Format is: 
<pre>G28.2 X0 Y0 Z0 A0 B0 C0</pre>
Axes not present are ignored and zero values are not changed.

For example. G28.2 X0 Y0 will home the X and Y axes only. The values provided for X and Y don't matter, but something must be present.

### Homing Operation
* G28.2 homes all axes present in the command 
 * The homing sequence progresses through each axis provided in the G28.2 block in turn - i.e. it does not home on multiple axes simultaneously. 
 * Axes are always executed in order of ZXYABC. The order the axis words occur in the G28.2 block has no effect. 
* Homing begins by checking the pre-conditions for homing
 * $xSV homing-search-velocity must be non-zero for the axis to be processed
 * Switches must be configured correctly - one and only one homing switch per axis
* The following is performed for each specified axis:
 * Homing begins by testing homing and limit switches for the currently homing axis. If a switch is tripped the axis will back off the switch by the Latch Backoff ($xLB) distance.
 * Once the switches are cleared a search move is executed. The search will travel at the Search Velocity ($xSV) for Travel Maximum ($xTM) distance towards the homing switch. The search runs until the homing switch is hit or the total travel is performed.
 * Once the switch is hit a Latch Backoff move is performed. This backs off the switch until the switch opens again. 
 * Once the switch is cleared the axis moves further off the sthe switch by the the Zero Backoff amount and sets zero for that axis.
* Once all axes are processed the affected axes are moved to the absolute home location (machine zero). At this point the homing state will indicate that the machine has been homed. Homing state can also be read using the homing group: $hom. This returns 0 or 1 for each axis to indicate homing state for each axis

## G28.3 - Set Zero 
G28.3 allows you to set a zero (or other value) for any axes. Some axes cannot be homed. They either don't have switches, are infinite axes like rollers on the Othercutter, or some other reason. Do the following to set a zero - for example:
<pre>
g28.3 y0
</pre> 

G28.3 also supports setting to non-zero values, if that's useful. G28.3 affects the $hom group - any axis set by g28.3 is considered set for $hom