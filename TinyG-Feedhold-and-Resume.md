_updated 6/8/13 - ash_

**NOTE: This page describes feedholds supported in release 0.95 / build 377.04 and later**

	Character | Command | AKA | Description
	----------|---------|-----|-------------
	! | Feedhold | Pause | Stop movement immediately. Maintains positional accuracy
	~ | Cycle Start | Resume | Resume movement from stopping point
	% | Queue Flush | | Empty planner buffer. Used for jogging and other cycles

To pause motion send a feedhold character ('!') character during motion. Movement will decelerate to a stop while maintaining positional accuracy. To resume motion send a cycle start tilde character ('~').

If you wish to dump the remainder of the move after the feedhold send a Queue Flush character after the feedhold character. Motion will start with the next Gcode command entered. A cycle start is not necessary after a Queue Flush.
