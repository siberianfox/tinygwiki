If you are on this page we can assume you want to write a program that talks to TinyG to send Gcode files, and possibly also to read and set configuration variables, report machine status, control jogging and homing, and other functions.

This page attempts to lay out the issues and approaches to writing a good programatic interface to TinyG. It's may also be useful to review the [introduction to the tinyg code base](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base) 

If you are writing a programmatic interface we highly recommend that you use the JSON syntax and avoid the command line (plain text) form. Text mode is really just a convenience for driving the interface from a command line for debugging and system discovery. All examples are provide in JSON form.

If you are just looking for an off-the-shelf way to drive TinyG please see these other links:
* [Sending Files with CoolTerm](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-CoolTerm)<br>
* [Sending Files with tgFX](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-tgFX)<br>

#Communications Basics
##Theory of Operation
TinyG communicates over USB serial. The default baud rate is 115,200 baud, but can be set to values between 9600 and 230,400 using the $baud=N command; {"baud":N} in JSON. See [Configuring TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#system-group)

TinyG has a 254 byte serial buffer that receives raw ASCII commands. A "command" is a single line of ASCII text ending with a CR or LF; or one or the other depending on the $ic setting. So this is the first queue that needs to be managed. If you overflow the serial buffer you will get erratic results. More on this under Flow Control.

There are 4 general classes of commands that can be pulled from the serial buffer:

1. Gcode commands (blocks) such as g0x10, m7, or g17
1. Configuration commands such as {"xvm":16000}
1. Actions, such as $defa=1 (reset all configuration values to default)
1. In-cycle commands such as ! (feedhold) and ~ (cycle start) (These have special handling - [see below](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#in-cycle-commands))

As commands are pulled from the serial buffer (one by one) they are executed immediately. For a configuration or action command this means that the values are applied and (usually) the EEPROM is updated. See [EEPROM Handling](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#eeprom-handling) - this is important.

Commands are not pulled from the serial buffer until the firmware knows it has the resources (time and space) to process them. 
 one at a time from the serial buffer.
the designated termination character set by  with a  (aka a Gcode "block", if it's Gcode).

### Gcode Blocks

### Configuration Commands

### Action Commands

### In-Cycle Commands


## Flow Control Options


### EEPROM Handling
Yikes!