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
	Configuration commands | {"xvm":16000} (set X maximum velocity to 16000), {"1mi":8} (set motor 1 microsteps to 8)
	Action commands | {"defa":1} (reset config values to default), {"sr":null} (request a status report) 
	Front-Panel commands | ! (feedhold), ~ (cycle start)

Commands from the serial buffer are handled as per below:

### Gcode Blocks
When a Gcode block is encountered it is passed to the Gcode parser. Depending on the gcode command it is either executed immediately or queued to the motion planner. All motion commands, dwells and most M commands are queued to the planner - i.e. "synchronized with motion". Examples of commands that are executed immediately are G20 and G21 - which set inches and millimeter units, respectively. These are executed immediately as the next gcode block that arrives needs to be interpreted in the correct units. 

In machinist-speak Gcode commands are "cycle" commands. This means that Gcode commands execute after a cycle start. (In fact, the firmware performs an automatic cycle start when it receives a Gcode command if it's not already in a machining cycle). Gcode cycle commands manipulate the internal [system state model](https://github.com/synthetos/TinyG/wiki/TinyG-State-Model). When the Gcode "file" is done it is supposed to be terminated with a Program End (M2 or M30), indicating that the machining cycle is over. 

This in-cycle / out-of-cycle is an important distinction because (notwithstanding some exceptions) configuration and actions should only occur out-of-cycle or errors and undesirable behaviors can creep in. This is discussed later.

Another way to look at Gcode commands is that the in-cycle commands manipulate the "dynamic model" of the machine. The dynamic model is typically what is returned from [status reports](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Reports). In RESTful terms, the dynamic model is a stateful Resource.

_Note that Gcode blocks are the exception to JSON mode. Gcode can be sent either wrapped in JSON or as native ASCII. Both of the following are acceptable forms: `g0x10` or `{"gc":"g0x10}`._

### Configuration Commands
Configuration commands are all the motor settings, axis settings, PWM settings, system settings, and other things that should be set up **before** you enter a cycle. This is the "static model" for the machine, and is represented by a series of stateful Resources (e.g. {"x":null}, {"1":null}, {"sys":null} )

Reading a configuration command (e.g. {"xvm":null} or {"x":null} ) is a no-brainer as it's just a retrieval. 

Writing a config value (e.g.  {"xvm":16000} ) is more complicated. Writing usually means that the new value is persisted to non-volatile memory (aka NVM, EEPROM). On the Xmega, at least, this persistence operation is painful in that it must disable all interrupts while the NVM write occurs. This means 2 things:

1. You cannot do a configuration write during a machining cycle as the steppers will stop
2. There can be no serial activity the duration of the write (something < 30 ms)

The simplest way to deal with this is to (1) don't issue config commands during a cycle, and (2) always run configuration commands synchronously. In other words, always wait until you receive the response from a command before sending the next one. Do not just blast them down to the serial buffer as you would a gcode file.

### Action Commands
There are a small number of commands that look like configs but actually perform actions or return 'reports". These are:

	Command  | Description
	---------|-------------------------
	{"sr":null} | Request a status report
	{"defa":1} | Reset configuration to default
	{"test":N} | Run self test N
	{"boot":1} | Enter boot loader
	{"help":null} | Request help screen

Obviously these commands should not be run during a machining cycle.

### Front-Panel Commands
These commands are the types of things that you might find on the front panel of a CNC machine. They effect the dynamic model (i.e. are not config or action commands), and are not found in the gcode "file". Currently there are only two front panel commands, but at some point we expect there will be more.

	Command  | Description
	---------|-------------------------
	! | Feedhold (pause)
	~ | Cycle start (resume)

For ease of processing these are single character commands. This is so that they can be removed from the serial stream and acted on immediately - therefore "jumping the queue". (Hopefully this will be clearer after you read about flow control in the next section.)

BUT - This queue-hopping does not work on the newer ARM ports, and we are planning more complex front-panel commands such as feed rate overrides that will not be possible to execute as single character commands.

## Host Programming Considerations
Now that we've described how the commands work, let's talk about how to feed commands to TinyG for optimal program execution.

### Flow Control Theory
The main topic is flow control. Let's review. There are 2 queues to manage (1) the planner buffer that contains the Gcode moves and synchronized commands, and (2) the serial buffer that feeds the controller, and hence the downstream planner queue.

The steady-state you want during movement (i.e. in cycle) is that the planner buffer is close to full, and the serial buffer is empty or close to empty. If the planner is too empty the planner will "starve" and motion will get rough and jerky. This can be heard as a rough or bumpy movement where a smooth movement should be.

To understand planner queue starvation look at how the planner buffer is built. Let's say there are a series of short segments that want to run at some reasonable feed rate (velocity), say 1000 mm/min. (By the way, this is the case for the vast majority of Gcode files - lots of short G1 moves with some target velocity FNNNN set for all of them). Bear in mind that the planner must always plan the last block it knows about to zero velocity (a stop). So when the first Gcode block (move) is presented it plans it up from zero then down to zero. Since this a short move it probably cannot achieve the target velocity of 1000 mm/min. Then the next block comes in. Now the planner can reach a higher velocity by joining the two moves than it could for just the one move, but perhaps still not the target velocity. This continues as new moves are added - each new move allows the planner to achieve a higher target velocity, until there is some critical number of moves that allows the planner to achieve and maintain the target velocity as new moves come in. 

For the velocities and move lengths achievable by TinyG the planner is usually happy with a small number of moves, somewhere between 4 and 12 moves. So as long as you can keep this number in the planner, the file will execute optimally (i.e. at the target velocity). Less than this and it will starve, never reliably achieving the target velocity.

On the other hand, If the planner is always full then the serial buffer will back up. This means you will lose a lot of data in the serial buffer if you need to recover from a soft alarm, or you will introduce delays if want to inject some of the more complex front-panel commands that we have planned. You don't want that, either.

So the optimal way to feed the system is to have about 20 to 24 moves in the planner buffer at all times once movement has started. This overage (relative to 12 moves, for example) is useful to absorb the non-movement synchronized commands such as M commands, etc. This will keep the serial buffer free at all times.

_Note: The planner queue actually has 28 buffers, but the controller will not process a command from the serial buffer unless at least 4 buffers are free, so the effective max queue depth is actually 24, not 28_

### Flow Control Options

The best way to ensure that the planner queue is happy is to use the queue reports. The following variables are available:

	Variable  | Description
	---------|-------------------------
	qr | Buffers remaining in planner queue
	qi | Buffers added to planner queue since last report (in)
	qo | Buffers removed from queue since last report (out)


If you overflow the serial buffer you will get erratic results as characters will be dropped. If the machine takes off with "a mind of its own" this is probably what's happening.

The 

So the planner is the second queue that needs to be managed. The planner has 24 buffers. As long as there is space in the planner the controller will continue to pull commands from the serial buffer. Once the planner fills up the serial buffer will start filling up. See Flow Control for more info on this. 

Commands are not pulled from the serial buffer until the firmware knows it has the resources (time and space) to process them. 
 one at a time from the serial buffer.
the designated termination character set by  with a  (aka a Gcode "block", if it's Gcode).
