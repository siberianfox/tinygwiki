**EXPERIMENTAL**
** The instructions on this page are experimental so please be warned ** 

## Theory of Operation
TinyG can operate in a Master/Slave configuration to control up to 8 motors. The motors can control any of the XYZABC axes, and axes can be doubled (or more) for dual gantry and other configurations. Use the $1ma... configs to map motors to axes.

How it works: The master board accepts commands over USB as usual. It sends the exact bytes it receives to the slave board over an RS-485 link with minimal delay (small # of microseconds). Both the slave and the master execute the entire (identical) command stream. The axis configurations are identical - except that the axes handled by the master are enabled (e.g. $xam=AXIS_STANDARD), and the axes not handled by that board are set to AXIS_INHIBITED (*NOT* AXIS_DISABLED). The opposite is true on the slave. Inhibiting an axis causes the axis to be computed - and therefore affect the move dynamics - but no steps are generated.

## Connecting the Boards
Make the following connections
* Connect the master to a USB port. This is used to stream commands and report back responses and status.
* Connect the two boards together via RS-485. On V6 and earlier boards plug in a short, straight-through Ethernet cable to both boards. On V7 boards connect the 3 position terminal block straight through (1 to 1, 2 tp 2, 3 to 3). 
* Jumper the RS-485-Bias on the master. Jumper the RS-485 termination on the slave.
* You can also optionally connect USB to the slave. This will not accept any input but will report back results

* Note: at the current time there is no way to configure the slave settings independently. We need to find a way to override the network connection so that the slave settings can be set via the USB port.



## Configuring Settings

You can slave 2 boards together to get the 5th and 6th axis


You will find build 371.01 In the tinyg/dev branch. It has 2 settings profiles:
/settings/settings_pocketcnc_linear.h
/settings/settings_pocketcnc_linear.h

You will want to modify these to your actual settings.  Do you have the ability to compile the project? If not just mod those files the best you can and send them back to me. Please note the the motor settings are specific to the board (linear board, rotary board). 

The axis settings must be identical in both profiles, with the exception of the axis mode. The linear has XYZ enabled and ABC inhibited. The Rotary is the opposite. "Inhibited" means that the Gcode model computes the axis but does not actually generate steps for it. The axis modes must be identical for the Gcode model in both boards to track each other and maintain coordination.

You will notice that in the linear settings the NETWORK_MODE = NETWORK_MASTER, in the rotary it's NETWORK_SLAVE