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
I should have started this earlier. I'll keep a record of the tings that affect UIs and usage. I'm not trying to keep a complete record of changes in the internals.


Changes in 353.xx builds
* Changed JSON body name from "b" to "r" to avoid collision with "b" axis groups
* Changed $je to $jv for JSON vebosity setting
* Added $tv for text verbosity setting
* Added some new self tests and made all tests work from and return to home position. Type $test for a list

Changes in 352 builds
* Removed $xsm switch mode setting
* Added $xsn and $xsx switch mode settings
