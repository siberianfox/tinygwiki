**EXPERIMENTAL**
** The instructions on this page are experimental so please be warned ** 

## Theory of Operation
TinyG can operate in a Master/Slave configuration to control up to 8 motors. The motors can control any of the XYZABC axes, and axes can be doubled (or more) for dual gantry and other configurations. Use the $AM config to map motors to axes.

This works as so. The master board accepts commands over USB as normal. It sends the exact bytes it receives to the slave board over an RS-485 link with minimal delay (small # of microseconds). Both the slave and the master execute the entire (identical) command stream. 

## Connecting the Boards

## Configuring Settings

You can slave 2 boards together to get the 5th and 6th axis


You will find build 371.01 In the tinyg/dev branch. It has 2 settings profiles:
/settings/settings_pocketcnc_linear.h
/settings/settings_pocketcnc_linear.h

You will want to modify these to your actual settings.  Do you have the ability to compile the project? If not just mod those files the best you can and send them back to me. Please note the the motor settings are specific to the board (linear board, rotary board). 

The axis settings must be identical in both profiles, with the exception of the axis mode. The linear has XYZ enabled and ABC inhibited. The Rotary is the opposite. "Inhibited" means that the Gcode model computes the axis but does not actually generate steps for it. The axis modes must be identical for the Gcode model in both boards to track each other and maintain coordination.

You will notice that in the linear settings the NETWORK_MODE = NETWORK_MASTER, in the rotary it's NETWORK_SLAVE