**EXPERIMENTAL**
** The instructions on this page are experimental so please be warned ** 

## Theory of Operation
TinyG can operate in a Master/Slave configuration to control up to 8 motors. The motors can control any of the XYZABC axes, and axes can be doubled (or more) for dual gantry and other configurations. Use the $1ma... configs to map motors to axes.

How it works: The master board accepts commands over USB as usual. It sends the exact bytes it receives to the slave board over an RS-485 link with minimal delay (small # of microseconds). Both the slave and the master execute the entire (identical) command stream. The axis configurations are identical - except that the axes handled by the master are enabled (e.g. $xam=AXIS_STANDARD), and the axes not handled by that board are set to AXIS_INHIBITED (*NOT* AXIS_DISABLED). The opposite is true on the slave. Inhibiting an axis causes the axis to be computed - and therefore affect the move dynamics - but no steps are generated.

## Connecting the Boards
Make the following connections
* Connect the master to a USB port. This is used to stream commands and report back responses and status.
* Connect the two boards together via RS-485. On V6 and earlier boards plug in a short, straight-through Ethernet cable to both boards. On V7 boards connect the 3 position terminal block straight through (1 to 1, 2 to 2, 3 to 3). 
* Jumper the RS-485-Bias on the master. Jumper the RS-485 termination on the slave.
* You can also optionally connect USB to the slave. This will output any command executed on the slave board. As of build 371.03 this will also accept input from USB so that the slave board can be independently configured from the master board. 

## Configuring Settings
Settings are best done via the default profiles. The following profiles are useful examples:
/settings/settings_pocketcnc_linear.h
/settings/settings_pocketcnc_linear.h

Warning: This is a little quirky. Any setting set on the master will also be sent to the salve and set on the slave. Any setting set on the slave via the USB port will affect only the slave. So set the master first, then go to the slave and reset anything that needs to be different.
 
The axis settings must be identical in both profiles, with the exception of the axis mode. In the example the master (linear) has XYZ enabled and ABC inhibited. The slave is the opposite. The axis mode settings must be identical for the Gcode model in both boards to track each other and maintain coordination.

THe motor settings on both board are probably quite different.

You will notice that in the master settings the NETWORK_MODE = NETWORK_MASTER, in the slave it's NETWORK_SLAVE

## Gothcas (Cautions and Unresolved Issues)

* Be mindful of that settings sent to the master will also be set on the slave. Don;t forget to set the slave independently if it needs to be different.

* When one board is reset, both boards should be reset. This ensures that the Gcode models remain in sync. A software reset sent to the master (^x) will also reset the slave.
 
