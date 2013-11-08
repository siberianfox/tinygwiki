If you are on this page we can assume you want to write a program that talks to TinyG to send Gcode files, and possibly also to read and set configuration variables, report machine status, control jogging and homing, and other functions.

If you are just looking for an off-the-shelf way to drive TinyG please see these other links:
* [Sending Files with CoolTerm](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-CoolTerm)<br>
* [Sending Files with tgFX](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-tgFX)<br>

This page attempts to lay out the issues and approaches to writing a good programatic interface to TinyG. It's may also be useful to review the [introduction to the tinyg code base](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base) 

If you are writing a programmatic interface we highly recommend that you use the JSON syntax and avoid the command line (plain text) form. Text mode is really just a convenience for driving the interface from a command line for debugging and system discovery. All examples are provide in JSON form.

##Theory of Operation
TinyG communicates over USB serial. The default baud rate is 115,200 baud, but can be set to values between 9600 and 230,400 using the {"baud":N} command. See [Configuring TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#system-group)

TinyG has a 254 byte serial buffer that receives raw ASCII commands. A "command" is a single line of ASCII text ending with a CR or LF; or one or the other depending on the {"ic":N} setting. The "controller" pulls serial lines off the serial buffer and passes them to the correct parser for that type of command. There are 4 general classes of commands that can be pulled from the serial buffer:

1. Gcode blocks (commands) such as g0x10, m7, or g17
1. Configuration commands such as {"xvm":16000}
1. Actions, such as {"defa":1} (reset all configuration values to default)
1. In-cycle commands such as ! (feedhold) and ~ (cycle start) (These have special handling - [see below](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#in-cycle-commands))

So the serial buffer is the first queue that needs to be managed. If you overflow the serial buffer you will get erratic results. More on this under [Flow Control Options](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#flow-control-options).

Commands from the serial buffer are handled as per below:

#### Gcode Blocks
When a Gcode block is encountered it is passed to the Gcode parser. Depending on the gcode command it is either executed immediately or queued to the motion planner. All motion commands, dwells and most M commands are queued to the planner - i.e. "synchronized with motion". Examples of commands that are executed immediately are G20 and G21 - which set inches and millimeter units, respectively. These are executed immediately as the next gcode block that arrives needs to be interpreted in the correct units. 

In machinist-speak Gcode commands are delivered "in-cycle". This means that Gcode commands execute after a cycle start. When the Gcode commands are done they are supposed to be terminated with a Program End (M2 or M30), indicating that the cycle is over. This in-cycle / out-of-cycle is an important distinction because configuration and actions should only occur out-of-cycle of various errors and undesiraable behaviors can creep in. There are some exceptions. This is discussed later.

_Note that Gcode blocks are the exception to JSON mode. Gcode can be sent either wrapped in JSON or as native ASCII. Both of the following are acceptable forms:_
<pre>
g0x10
{"gc":"g0x10}
</pre>

So the planner is the second queue that needs to be managed. The planner has 24 buffers. As long as there is space in the planner the controller will continue to pull commands from the serial buffer. Once the planner fills up the serial buffer will start filling up. See Flow Control for more info on this. 

#### Configuration Commands
Configuration commands are all the motor settings, axis settings, PWM settings, system settings, and other things that 

For a configuration or action command this means that the values are applied and (usually) the EEPROM is updated. See [EEPROM Handling](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#eeprom-handling) - this is important.

Commands are not pulled from the serial buffer until the firmware knows it has the resources (time and space) to process them. 
 one at a time from the serial buffer.
the designated termination character set by  with a  (aka a Gcode "block", if it's Gcode).

#### Action Commands

#### In-Cycle Commands / Out-Of-Cycle Commands


## Flow Control Options


### EEPROM Handling
Yikes!