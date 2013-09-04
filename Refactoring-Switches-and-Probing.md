This page is a design page for a major refactoring of the way switches (or more generally, sensors) are handled. This has become necessary to properly support probing cycles, and should be useful in the future for adding encoders and other feedback mechanisms.

##Problem Statement
Currently switches are defined by their homing / limit function. They are bound to an axis, and min or max on that axis. With the introduction of probing cycles this no longer makes sense. What axis is a probe bound to for touch offs and center finding? Further, in the current design all switches are of the same type (NO or NC), and have the same (fixed) deglitch and debounce characteristics. This makes is difficult to configure a system that has both homing/limit switches on XYZ axes and uses the tool as a touch-off probe.

##Discussion of Requirements and Design Options
###General Requirements

* Support various types of switches including (but not limited to)
 * "microswitch" style button and leaf actuated switches
 * probe "switches" where electrical closure is made by contact between the probe (or tool) and the work or a conductive surface
 * "raw" switches which are just 2 conductive surfaces in contact (a degenerate case of a probe) 
 * Opto switches
 * Hall effect sensors
* Have a structure that's also capable of supporting other types of positional and state input including:
 * rotary / linear encoders
 * image recognition systems (e.g. edge or centroid reporting via data inputs)
 * "buttons" and other user-activated inputs
 * firmware generated edge conditions such as soft limits
* Be as resistant to switch mis-configuration as possible without sacrificing flexibility

###Use Cases
We can envision:

End-Stop Cases
* UC_01: Limit Switches: End-stop switch closure resets machine
* UC_: Homing Switches: End-stop switch closure used for referencing an axis to zero

Probe Cases
* UC_: Edge Finder Probe: Find an edge using a probe and a touch-off operation
* UC_: Corner Finder Probe: Find a corner using a probe and a touch-off operation
* UC_: Center Finder Probe: Find a center position using a conductive ring and a probe

Other Cases
* UC_: Soft Limits: Programmatically detected boundary conditions fire a processing exception
* UC_: Rotary Indexing: A shaft or other device fires a "switch" at a defined spot in a revolution
* UC_: Wire Deflection Sensor: Detects when wire un-bends and breaks contact with an electrical post
* UC_: Image Recognition Sensor: An image recognizer sends position information or sets state (e.g. for pick and place)

###Switch Resource Model

_I'm really just throwing stuff at the wall for now in this pert. Just saying._

'S' objects (sensor objects) holds an array of switches and possibly other sensors. These get bound to use cases, which get bound to specific types of cycles. That way the sensor can be used differently in different modes of operation. I don't really know yet.

Use the 'sN' prefix for groups, e.g. s1 - s8 for the current switch array 

	Setting | Description | Notes
	--------|-------------|-------
	$s1mo | Switch mode | 0=disabled, 1=NO, 2=NC
	$s1ty | Switch type | 1=mechanical, 2=opto, 3=probe... Sets defaults for switch behaviors. Can be overridden below
	$s1deb | Switch debounce | -1=use default for switch type, 0-N = samples to debounce (mSec)
	$s1deg | Switch deglitch | -1=use default for switch type, 0-N = lockout time after firing (mSec)




## Use Case Detail
###UC_01: Limit Switches
Currently when a limit switch is hit all bets are off. This is how my machinist friends tell me it's supposed to work. Hitting a limit is a catastrophic event that immediately shuts down the machine and stops all work. 

In grbl, however, position is preserved when a limit switch is hit; something we've not worried about in TinyG. I see this (position preserved) being used more as compensation for not having a more general probe function. ALso, introducing soft limits should remove many of the cases where a limit will actually get hit. 

Lastly, there are some users who don't use switches at all and instead use probing to zero the machine