This page records the items and progress needed to get TinyG to 1.0 status. It is divided into two sections: major items and cleanup / validation / bug fixes. There is also a section for things that need to happen on the GUI side. Some of these items may become github Issues, but I thought it would be easier to track it all in one place. This page will be periodically updated as progress is made or new items are identified.

##Major Items
* [1] Merge Otherlab ESC spindle controller code and define final settings

* [2] Add boot loader. This also may require host-side code to be written as AVRdude may be too flakey for 1.0 status.

* [3] Feed and rapid override.

##Cleanup, Validation, and Bug Fixes

* [1] Validate homing cycles

* [2] Finalize and document JSON operations. This includes finalizing flow control, echo modes, and possibly some other functionality.

* [3] Cleanup in-cycle / out-of-cycle command handling

##GUI Functions
There may be more than one GUI program. These items refer to all GUIs unless otherwise noted
 
* [1] validated end-to-end test of GUI driver

* [2] Jog functionality supported by GUI. This item may require specialized command support from the firmware - TBD.