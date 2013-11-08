If you are on this page we can assume you want to write a program that talks to TinyG to send Gcode files, and possibly also to read and set configuration variables, report machine status, control jogging and homing, and other functions.

If you are just looking for an off-the-shelf way to drive TinyG please see these other links:
* [Sending Files with CoolTerm](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-CoolTerm)<br>
* [Sending Files with tgFX](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-tgFX)<br>

This page attempts to lay out the issues and approaches to writing a good programatic interface to TinyG. It may also be useful to review the [introduction to the tinyg code base](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base).

If you are writing a programmatic interface we highly recommend that you use the [JSON syntax](https://github.com/synthetos/TinyG/wiki/JSON-Operation) and avoid the command line (plain text) form. Text mode is really just a convenience for driving the interface from a command line for debugging and system discovery. All examples are provide in JSON form.

##Theory of Operation
TinyG communicates over a single USB serial channel. The default baud rate is 115,200 baud, but can be set to values between 9600 and 230,400 using the {"baud":N} command. See [Configuring TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#system-group)

TinyG has a 254 byte serial buffer that receives raw ASCII commands. A "command" is a single line of ASCII text ending with a CR or LF; or one or the other depending on the {"ic":N} setting. The "controller" pulls serial lines off the serial buffer and passes them to the correct parser for that type of command. There are 4 general classes of commands that can be pulled from the serial buffer:

	Command Type | Example(s)
	---------|-------------------------
	Gcode blocks (commands) | g0x10, m7, g17, {"gc":"g0x10} (both JSON wrapped and unwrapped forms are supported)
	Configuration commands | {"xvm":16000}
	Action commands | {"defa":1} reset config values to default, {"sr":null} request a status report 
	Front-Panel commands | ! (feedhold), ~ (cycle start)

Commands from the serial buffer are handled as per below:

#### Gcode Blocks
When a Gcode block is encountered it is passed to the Gcode parser. Depending on the gcode command it is either executed immediately or queued to the motion planner. All motion commands, dwells and most M commands are queued to the planner - i.e. "synchronized with motion". Examples of commands that are executed immediately are G20 and G21 - which set inches and millimeter units, respectively. These are executed immediately as the next gcode block that arrives needs to be interpreted in the correct units. 

In machinist-speak Gcode commands are "cycle" commands. This means that Gcode commands execute after a cycle start. (In fact, the firmware performs an automatic cycle start when it receives a Gcode command if it's not already in a machining cycle. This is all kept in the internal [system state model](https://github.com/synthetos/TinyG/wiki/TinyG-State-Model)). When the Gcode "file" is done it is supposed to be terminated with a Program End (M2 or M30), indicating that the machining cycle is over. 

This in-cycle / out-of-cycle is an important distinction because (notwithstanding some exceptions) configuration and actions should only occur out-of-cycle or errors and undesirable behaviors can creep in. This is discussed later.

Another way to look at Gcode commands is that the in-cycle commands manipulate the "dynamic model" of the machine. The dynamic model is typically what is returned from [status reports](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Reports). In RESTful terms, the dynamic model is a stateful Resource.

_Note that Gcode blocks are the exception to JSON mode. Gcode can be sent either wrapped in JSON or as native ASCII. Both of the following are acceptable forms:_
<pre>
g0x10
{"gc":"g0x10}
</pre>

#### Configuration Commands
Configuration commands are all the motor settings, axis settings, PWM settings, system settings, and other things that should be set up **before** you enter a cycle. This is the "static model" for the machine, and is represented by a series of stateful Resources (e.g. {"x":null}, {"1":null}, {"sys":null} )

Reading a configuration command (e.g. {"xvm":null} or {"x":null} ) is a no-brainer as it's just a retrieval. Setting one (e.g.  {"xvm":16000} ) is more complicated as this usually implies that the new value is persisted to non-volatile memory (aka NVM, EEPROM). On the Xmega, at least, this persistence operation is painful in that it must disable all interrupts while the NVM write occurs. This means 2 things:

1. You cannot do a configuration write during a machining cycle as the steppers will stop
2. There can be no serial activity the duration of the write (something < 30 ms)

The simplest way to deal with this is to (1) don't issue config commands during a cycle, and (2) always run configuration commands synchronously. In other words, always wait until you receive the response from a command before sending the next one. Do not just blast them down to the serial buffer as you would a gcode command.

#### Action Commands
There are a small number of commands that look like configs but actually perform actions or return 'reports". These are:

	Command  | Description
	---------|-------------------------
	{"sr":null} | Request a status report
	{"defa":1} | Reset configuration to default
	{"test":N} | Run self test N
	{"boot":1} | Enter boot loader
	{"help":null} | Request help screen

Obviously these commands should neo be run during a machining cycle.

#### Front-Panel Commands
These commands are the types of things that you might find on the front panel of a CNC machine. They effect the dynamic model (i.e. are not config or action commands), and are not found in the gcode "file". Currently there are only two front panel commands, but at some point we expect there will be more.

	Command  | Description
	---------|-------------------------
	! | Feedhold (pause)
	~ | Cycle start (resume)


## Host Programming Considerations

### Flow Control Options

So the serial buffer is the first queue that needs to be managed. If you overflow the serial buffer you will get erratic results. More on this under [Flow Control Options](https://github.com/synthetos/TinyG/wiki/Tinyg-Communications-Programming#flow-control-options).

So the planner is the second queue that needs to be managed. The planner has 24 buffers. As long as there is space in the planner the controller will continue to pull commands from the serial buffer. Once the planner fills up the serial buffer will start filling up. See Flow Control for more info on this. 

Commands are not pulled from the serial buffer until the firmware knows it has the resources (time and space) to process them. 
 one at a time from the serial buffer.
the designated termination character set by  with a  (aka a Gcode "block", if it's Gcode).



### EEPROM Handling
Yikes!