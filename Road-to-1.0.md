This page records the items and progress needed to get TinyG to 1.0 status. It is divided into two firmware sections: major items and cleanup / validation / bug fixes. There is also a section for things that need to happen on the GUI side. Some of these items may become github Issues, but I thought it would be easier to track it all in one place. This page will be periodically updated as progress is made or new items are identified.

##Major Items
* [1] Merge Otherlab ESC spindle controller code and define final settings - Done. Needs testing. Probably need to tie this to the S codes.

* [2] Add boot loader. This also may require host-side code to be written as AVRdude may be too flakey for 1.0 status.

* [3] Feed and rapid override.

* [4] Soft limits (maybe). Reject commands that exceed machine dimensions - before they are run.

##Cleanup, Validation, and Bug Fixes

* [1] Finalize homing cycles. This may involve taking homing out-of-cycle (i.e. connecting it from something other than G28.1), validating complete homing function and return-to-home commands (G28, G30). 

* [2] Finalize and document JSON operations. This includes finalizing flow control, echo modes, and possibly some other functionality.

* [3] Cleanup in-cycle / out-of-cycle command handling. This item puts some structure around handling commands that cannot be run while in a machining cycle - like changing the axis or motor setup parameters. 

* [4] Incorporate Otherlab changes to steppers and homing into main branches

##GUI Functions
There may be more than one GUI program. These items refer to all GUIs unless otherwise noted
 
* [1] validated end-to-end test of GUI driver

* [2] Jog functionality supported by GUI. This item may require specialized command support from the firmware - TBD.

## Parking lot
Good ideas that can wait until later

# Changelog
I should have started this earlier. I'll keep a record of the things that affect UIs and usage. I'm not trying to keep a complete record of changes in the internals.

Changes in 358.xx builds
* Added $id string for a unique ID per board. E.g. {"id":""} returns {"r":{"id":"9H3906-SYP"},"f":[1,0,10,5756]}. ID is also returned as part of the system group.

* Made 'pos' and 'mpo' groups for work position and machine position query. try {"pos":""} and {"mpo":""}. Example: {"r":{"pos":{"x":0.000,"y":0.000,"z":0.000,"a":0.000,"b":0.000,"c":0.000}},"f":[1,0,11,3252]}

* Added machine homing status to the homing status group. 'hom' group now returns machine homed status as 'e', plus each homed axis. E.g. {"hom":""} returns {"r":{"hom":{"e":0,"x":0,"y":0,"z":0,"a":0,"b":0,"c":0}},"f":[1,0,11,2155]}  or $hom for text mode equivalent

Changes in 357.xx builds
* Added a deglitching phase to switch closures. This helps a little but you still need to make sure your switch leads are well isolated and shielded.

Changes in 356.xx builds
* Added chordal tolerance variable to arc generation ($ct). This permits the arcs to draw with fewer lines as the arc radius increases, resulting in faster arc drawing particularly on larger arcs.

* New behaviors for G28, G30 and homing. Made these more compatible with LinuxCNC and grbl behaviors. Changes are:
 * G28 [axes] - returns to a preset position in absolute coordinates. Goes through intermediate position specified in the optional axes words. Can be used in incremental mode, e.g. G91 G28 z10  to clear Z obstacles
 * G28.1 - sets the g28 preset position to the current location
 * G28.2 <axes> - perform a homing cycle for any axis specified. The values in the axis words are ignored
 * G28.3 <axes> - set absolute machine coordinates as "zero". Useful for infinite axes or axes that cannot otherwise be homed, such as an infinite Y axis for the Othercutter. Axes words are typically zero, but any coordinate can be entered to set the axis to that position.
 * G30 [axes] - returns to a preset position in absolute coordinates. Goes through intermediate position specified in the optional axes words. Can be used in incremental mode, e.g. G91 G30 z10 to clear Z obstacles
 * G30.1 - sets the g30 preset position to the current location 

	**Note: G28.3 supersedes G92.4 which has been removed**

* Added homing status per axis. query {"homx":""} or {"hom":""} for whole group, e.g.
{"r":{"hom":{"x":1,"y":1,"z":1,"a":1,"b":0,"c":0}},"f":[1,0,11,7294]}. Value is set to one via homing cycle or G28.3 setting

* Eliminated duplicate QR reports in filtered mode.

Changes in 355.xx builds
* Added filtered mode for QRs. QR settings are 
<pre>
0=QR off
1=QR filtered    - QR report only sent when high or low water marks are crossed
2=QR verbose     - QR report sent for every planner buffer transition
</pre>
In filtered mode QRs are returned only when the number of available buffers is less than a low threshold number (low water mark, set as $eql) or greater than a high threshold number ($eqh). Reasonable starting points are $eqh=20, meaning that 20 buffers are available out of 24, and that the planner will therefore starve in 4 more buffers if none are provided. A good $eql lo threshold is 2, meaning that only 2 more buffers can be put on the planner queue before the serial RX buffer starts to back up.

* Raised JSON string value limit from 64 chars to 80 chars. I'm concerned that some verbose Gcode blocks would exceed the 64 character limit. Comments that cross the limit are OK, as they are truncated, but legitimate Gcode that overruns the limit will be trapped and cause the block to be rejected.

Changes in 354.xx builds
* Simplified qr's to read {"qr":1} where '1' is the number of buffers available in the planner queue. Plans are to add a hi/lo water mark filtering capability to further cut down on transmission, but this is not enabled yet.

* (NOTE: This function moved to G28.3 in 356 builds) Added an "unofficial gcode" command to allow arbitrarily setting an axes machine position. This is needed to support the Otherlab infinite Y axis. use: While in a zero-offset coordinate system (presumably G54) and with the axis(or axes) where you want it, issue g92.4 y0 (for example). The Y axis will now be set to 0 (or whatever you said it was). Works for one or more axes. I plan to extend so this works while in any arbitrary coordinate system but for now this is not working. This function still needs testing.

* Found and fixed a problem in pin outputs for M7/M8/M9 (coolant). These worked fine on v6 boards but did not work on v7's. There is a routing change in v7s that requires the firmware to know what HW revision is running. Added $hv (hardware version) to the system group - by default set to 7. Set to 6 if running version 6 HW or earlier.

* Hid some variables from the system group. These should not be changed by the user, and now do not show up in system listings. They are still accessible for display and change via $ml, $ma, $mt
 * $ml - minimum line length
 * $ma - minimum arc segment
 * $mt - nominal segment time

Changes in 353.xx builds
* Changed limit switch shutdown behavior. Limits cause immediate machine shutdown (as before), but now lock out all other activity until a hard reset or a software reset (ctrl-x) is performed. This prevents runaways if the host is still streaming code to the board. Blinks the spindle DIR LED when in shutdown state.
The board sends an 'er' report (in JSON mode) or an EMERGENCY SHUTDOWN message if in text mode.

* Fixed G92 and G54-G59 status reporting so these should be more useful now.
* Changed JSON body name from "b" to "r" to avoid collision with "b" axis groups
* Changed $je to $jv for JSON vebosity setting
* Added $tv for text verbosity setting
* Added some new self tests and made all tests work from and return to home position. Type $test for a list
* Removed axis Slave modes. These "slots" will be used for application specific modes such as extruder, laser hardener, laser cutter, tangential knife, etc.

* Removed auto-increment line numbers. Line numbers are only recorded if they are present in the Gcode file as an N parameter. The line number will remain in effect for multiple gcode blocks until a new line number is provided.

Changes in 352 builds
* Removed $xsm switch mode setting
* Added $xsn and $xsx switch mode settings
* Added commas to text-mode parser - values can be entered as example $xjm=5,000,000,000