If you are on this page we can assume you want to write a program that talks to TinyG to send Gcode files, and possibly also to read and set configuration variables, report machine status, control jogging and homing, and other functions. It is intended for GUI developers and other low-level access. 

**We strongly recommend using the [node-g2core-api](https://github.com/synthetos/node-g2core-api) nodeJS module, which already handles the communications**. This page is useful if for some reason you need to write your own communications handler, or just want to know how it works._

If you are just looking for an off-the-shelf way to drive TinyG please see these other links:
* [Sending Files with CoolTerm](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-CoolTerm)<br>

# Introduction

This page discusses issues and approaches to writing a good programatic interface to TinyG/g2core. It may also be useful to review the [introduction to the tinyg code base](https://github.com/synthetos/TinyG/wiki/Introduction-to-the-TinyG-Code-Base). This page on [flow control and footers](https://github.com/synthetos/TinyG/wiki/Flow-Control-and-Footers) also has some notes that were used in developing the system communications.

If you are writing a programmatic interface we highly recommend that you use the [JSON syntax](https://github.com/synthetos/TinyG/wiki/JSON-Operation) and avoid the command line (plain text) form. Text mode is really just a convenience for driving the interface from a command line for debugging and system discovery. All examples here are provide in JSON form.

## Vocabulary
A **_host_** is any computer that talks to a g2core board. Typically this is an OSX, Linux, or Windows laptop or desktop computer communicating over USB. A **_microhost_** is a tiny linux system like a Beaglebone, Raspberry Pi, or NextThingCo CHIP. These usually talk to g2core over a direct serial UART - although they may alternately communicate over USB.

A **_command_** is a single line of text sent from the host to the board. A command can be either **_data_** or **_control_**.

Gcode lines are always **_data_** commands. Generally, unless a line is identified as a control, it's considered data.

JSON commands are always _**controls**_. So are single-character commands such as `!` feedhold, `%` queue flush, `~` resume

**_Multiplexed Channels_**: Even though there is only a single physical host-to-board connection (USB or serial) the communications channel distinguishes between control and data lines and keeps separate logical queues for each. Controls are always executed before data - so they "jump the queue".

## Theory of Operation
TinyG communicates over a single USB serial channel terminated by an FTDI chip (USB serial emulation). The default baud rate is 115,200 baud, but can be set to values between 9600 and 230,400 using the {"baud":N} command. See [Configuring TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#system-group). 

g2core is configured similarly, but uses a native USB channel that will transmit at 12 Mbps or 480Mbps, depending on the exact board and host used. It can alternately be connected to a using a 4-wire serial port (rx/tx/rts/cts).

The board has a 1000 byte serial buffer that can buffer up to 12 lines of ASCII text. A "command" is a single line of ASCII text ending with a CR or LF; or one or the other depending on the {"ic":N} setting. The "controller" pulls serial lines from the buffer and passes them to the correct parser for that type of command. There are 4 general classes of commands that can be pulled from the serial buffer:

Command Type | Example(s)
---------|-------------------------
Gcode blocks (data) | g0x10, m7, g17, {"gc":"g0x10"} (both JSON wrapped and unwrapped forms are supported)
Configuration commands | {"xvm":16000} (set X maximum velocity to 16000), {"1mi":8} (set motor 1 microsteps to 8)
Action commands | {"defa":1} (reset config values to default), {"sr":null} (request a status report)
Front-Panel commands | ! (feedhold), ~ (cycle start)

Commands from the serial buffer are handled as per below:

### Gcode Blocks
When a Gcode block is encountered it is passed to the Gcode parser. Depending on the gcode command it is either executed immediately or queued to the motion planner. All motion commands, dwells and most M commands are queued to the planner - i.e. "synchronized with motion". Examples of commands that are executed immediately are G20 and G21 - which set inches and millimeter units, respectively. These are executed immediately as the next gcode block that arrives needs to be interpreted in the correct units.

In machinist-speak Gcode commands are "cycle" commands. This means that Gcode commands execute after a cycle start. (In fact, the firmware performs an automatic cycle start when it receives a Gcode command if it's not already in a machining cycle). Gcode cycle commands manipulate the internal [system state model](https://github.com/synthetos/TinyG/wiki/TinyG-State-Model). When the Gcode "file" is done it is supposed to be terminated with a Program End (M2 or M30), indicating that the machining cycle is over.

This in-cycle / out-of-cycle is an important distinction because (notwithstanding some exceptions) configuration and actions should only occur out-of-cycle or errors and undesirable behaviors can creep in. This is discussed later.

Another way to look at Gcode commands is that the in-cycle commands manipulate the "dynamic model" of the machine. The dynamic model is typically what is returned from [status reports](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Reports). In RESTful terms, the dynamic model is a stateful Resource.

_Note that Gcode blocks are the exception to JSON mode. Gcode can be sent either wrapped in JSON or as native ASCII. Both of the following are acceptable forms: `g0x10` or `{"gc":"g0x10"}`._

### Configuration Commands
Configuration commands are all the motor settings, axis settings, PWM settings, system settings, and other things that should be set up **before** you enter a cycle. This is the "static model" for the machine, and is represented by a series of stateful Resources (e.g. {"x":null}, {"1":null}, {"sys":null} )

Reading a configuration command (e.g. {"xvm":null} or {"x":null} ) is a no-brainer as it's just a retrieval.

Writing a config value (e.g.  {"xvm":16000} ) is more complicated. Writing usually means that the new value is persisted to non-volatile memory (aka NVM, EEPROM). On the Xmega, at least, this persistence operation is painful in that it must disable all interrupts while the NVM write occurs. This means 2 things:

1. You cannot do a configuration write during a machining cycle as the steppers will stop
2. There can be no serial activity the duration of the write (something < 30 ms)

The simplest way to deal with this is to (1) don't issue config commands during a cycle, and (2) always run configuration commands synchronously. In other words, always wait until you receive the response from a command before sending the next one. Do not just blast them down to the serial buffer as you would a Gcode file.

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

# Driving the Board from a Host Computer
Now that we've discussed how commands work let's talk about how to feed commands to TinyG for optimal program execution.

We recommended using Line Mode protocol for host-to-board communications. In line mode the board works on complete command lines, instead of characters. The host sends a few command lines to prime the board's serial receive queue, then the host sends a new line each time it receives a response from a processed line. That's it. So if you are building a sender that's all you need to do. 

We recommend using the [node-g2core-api](https://github.com/synthetos/node-g2core-api) nodeJS module, which already handles line mode communications. Or just use it as a worked example if you are writing your own.

TinyG and g2core also support character mode (byte streaming) which is deprecated and may be removed at a later time. 

##Overview of Communications Model


## Linemode Protocol

We designed the _linemode protocol_ to help prevent the serial buffer from either filling completely (preventing time-critical commands from getting through) while keeping the serial buffer full enough in order to prevent degradation to motion quality due to the motion commands not making it to the machine in a timely manner.

The protocol is simple - "blast" 4 lines to the board without waiting for responses. From that point on send a single line for every `{r:...}` response received. Every command will return one and only one response. **The exceptions are single character commands, such as !, ~, or ENQ, which do not consume line buffers and do not generate responses.**

In implementation it's actually rather simple:

1. Prepare or start reading the list of _data_ lines to send to the g2core. We'll call this list `line_queue`.
2. Set `lines_to_send` to `4`.
  * `4` has been determined to be a good starting point. This is subject to tuning, and might be adjusted based on your results.
3. Send the first `lines_to_send` lines of `line_queue` to the g2core, decrementing `lines_to_send` by one for _each line sent_.
  * If you need to read more lines into `line_queue`, do so as soon as `lines_to_send` is zero.
  * If `line_queue` is being filled by dynamically generated commands then you can send up to `lines_to_send` lines immedately.
  * Don't forget to decrement `lines_to_send` for _each line sent_! And don't send more than `lines_to_send` lines!
4. When a `{r:...}` response comes back from the g2core, add one to `lines_to_send`.
  * It's **vital** that when any `{r:...}` comes back that `lines_to_send`. If one is lost or ignored then the system will get out of sync and sending will stall.
5. Loop back to 3. (Better yet, have 3 and 4 each loop in their own thread or context.)

Notes:
* Steps 3 and 4 are best to run in their own threads or context. (In node we have each in event handlers, for example.) If that's not practical, it's vital that when a `{r:...}` comes in that `lines_to_send` is incremented and that lines can be sent as quickly after that as possible.
* It is possible (and common) to get two or more `{r:...}` responses before you send another line. This is why it's vital to keep track of `lines_to_send`.
* Note that only _data_ (gcode) lines go into `line_queue`! For configuration JSON or single-line commands, they are sent immediately.
  * It's important to maintain `lines_to_send` even when sending past the `line_queue`.
    * Single-character commands will *not* generate a `{r:...}` response (they may generate other output, however), so there's nothing to do (see following notes). 
    * JSON commands (`{` being the first character on the line) **will** have a `{r:...}` response, so when you send one of those past the queue you should still subtract one from `lines_to_send`, or `lines_to_send` will get out of sync and the sender will eventually stall waiting for responses. This is the *only* case where `lines_to_send` may go negative.
  * Note that control commands, like dta commands, must start at the beginning of a line, so you should always send whole lines. IOW, don't interrupt a line being sent from the `line_queue` to send a JSON command or feedhold `!`.
* **All** communications to the g2core **must** go through this protocol. It's not acceptable to occasionally send a JSON command or gcode line directly past this protocol, or `lines_to_send` will get out of sync and the sender will eventually stall waiting for responses.

# Backgrounder: Problem Description - the "Bucket Brigade"

[![Bucket Brigade](http://www.stepneyct.org/history/ht/images/stop_17e_430x195.jpg)](http://www.stepneyct.org/history/ht/stop17.html)

With a single channel of communication, either UART or USB, we have two single-file lines of bytes: one going to the g2core, and one coming from the g2core.

In both cases, there are often several "players" involved between the two endpoints of the channel. For example, for USB, there is:

  * Your UI application →
  * The language's serial buffers →
  * The OS serial USB driver buffers →
  * The OS USB buffers →
  * The g2core USB peripheral buffers →
  * The g2core serial buffers, then, finally →
  * The g2core line parser.

This amounts to a "bucket brigade," where once you pass the line into the serial system, it has to pass through several "hidden" layers (there may be more or less than listed above) before it gets to the g2core, and then there's a buffer before it can be parsed and handled.

This is generally fine, as long as you don't need to stop the flow of commands. For example, to execute a feed-hold you send a `!` character at the beginning of a new line. It has to get through all of those buffers before it is even seen by the g2core. So, if there are too many lines in the buffer before the g2core, the feedhold may take a noticeable (and unacceptable) amount of time to go into effect.

To complicate matters, the g2core cannot blindly read lines, but only reads lines as there is room for the parsed results. This generally means that as a line is read and parsed, room is made in the internal serial buffer. If the motion buffer is full, however, there is no room for new moves to be stored, so g2core will stop reading and parsing _data_ lines until room is made in the motion buffer. It will continue to read _control_ lines in any case.

To help with this the g2core serial system will "pre-parse" the incoming serial buffer, even when data lines are no longer being read (due to the motion buffer being full, a feedhold in place, etc), and look for lines that begin with "{" or one of the single-character commands (`!`, `~`, `%`, `^D`, `^X`, etc.). If it locates one of these lines, it will mark it as a control line and the next control-line-read will see it and execute it immediately.

So, as long as the serial buffer is not completely filled with data lines, there is at least room for a single-line command and generally room for a JSON command as well.

There are other issues that are dealt with in other ways, such as if the serial buffer _does_ fill completely (even if very briefly) we need to ensure that we don't lose data. This is mostly a concern with UART, and in that case we require some form of _flow control_. Currently we only support hardware flow-control, using the additional `RTS`/`CTS` lines. Linemode is _not_ a replacement for flow-control, and will not prevent lost data due to buffer overflow on it's own.

One further note: Due to the way g2core plans motion in real-time, if the gcode commands request moves of very brief duration, and the serial buffer isn't kept full enough of moves, then there will be a noticeable degradation in velocity and in some configurations the machine will exhibit a noticeable "stutter" as it executes each move on it's own or in small groups.

# OLD (BUT NEWER), DEPRECATED. REMOVE WHEN DONE

Linemode allocates 8 "line slots". The board can hold 8 lines of text before declaring that it has no more room in the receive buffer. Lines are processed in the order the command lines were received, but any control commands take precedence over data commands and are processed first. 

The number of line buffers available is returned as the third (last) number in the `{r:...}` response footer, and can also be queried by sending an `{rx:n}` command. You will never see more than 7, because the command being processed is one of the 8. However, in normal operation the host will not use the available lines count, as this is only necessary to handle some exceptional cases.

The protocol is simple - "blast" 4 lines to the board without waiting for responses. From that point on send a single line for every `{r:...}` response received. Every command will return one and only one response. **The exceptions are single character commands, such as !, ~, or ENQ, which do not consume line buffers and do not generate responses.**

The above protocol works for all cases we have seen. A belt and suspenders approach is for the host to time out responses, and examine the last received buffer count to re-sync. We have not seen this 


# OLD, DEPRECATED. REMOVE WHEN DONE

Not all communications modes are supported by all of the above use cases
 
- Single Endpoint / Packet Mode
  - Ctrl+Data channel, probably 8 packets or 128 chars each
  - One packet is reserved for each control channel, the rest are pooled for data
  - Free packet count is returned in the footer
    - This is not counting partially written or "reserved for control" packets.
  - Selected by {rxm:1} (this is the default)

- Single Endpoint / Character mode
  - This is a simulation of character mode that is actually running packet mode. It's in here for people that have already coded character counting and don't want to recode
  - Simulates character mode by returning the bytes consumed by the command in the footer
  - {rx:n} returns something less than the total remaining free bytes in available packets, e.g. 1/2 of the available bytes left in the packet. 
  - Selected by {rxm:0)
  - Not recommended for new implementations

- Dual Endpoint / Packet Mode
  - As packet mode above, but the endpoints are separate

- Dual Endpoint / Character mode
  - As character mode above, but the endpoints are separate

###Host APIs and Support
There are 3 packages made available (planned) to the host as APIs

####USB Port Discovery Tool
Cross-platform C/C++ tool to examine the host's USB subsystem and return a JSON object per TinyG/G2, with at least the following information:
- OS device names for the ports associated with this device as an array of one or more strings.
  - Examples: `COM10`, `/dev/ttyACM1`, `/dev/usbserial-ABCDEF1123`
  - They are in no particular order -- order does NOT imply function.
- Type of protocol supported
  - The only options now are USB Serial / Dual USB Serial

Other information we will have that we could also return (perhaps in a "Verbose mode"):
- Manufacturer (Vendor ID)
- Device (Device ID)
- Serial number

####Low-Level Communications API
Cross platform C/C++ library / process performs the following
- Port discovery (above)
- Transparent port binding for single or dual channel mode -- instead they simply talk to a G-device. They don't need to know or care how many or what type of ports, beyond the minimum ability to select from multiple G-devices.
- Hiding and handling of underlying comm protocol (e.g. USB, serial, SPI)
- Streams data (jobs) to the CNCcore

Questions:
- How is this exposed to the caller? Separate control and data channels? Here's the work in this option.
  - *RG:* The user should be able to open the TinyG/G2 Core connection as a process, and pipe STDIN, STDOUT, and maybe STDERR. All communications can happen via standard OS IPC, including signals. (Windows is always the odd duck here, but I'm not going to spend to many cycles there. There are decades of solutions for that problem, we just need to pick one.)

####High-Level Communications API

Placeholder for job control and config API

###Drivers
The APIs are exposed as drivers for the major platforms

###Backlog and Options
How do we get John where he needs to be?

- His requirements (let's treat these as constraints)
  - The user is responsible for establishing connection (root of the problem, I think)
  - Need to support multiple g2's running from the same CP
  - SPJS cannot deal with a single upstream channel from 2 ports
  - We need to solve the "slow feedhold" problem (and no starving)

- Immediate Solution (near term)
  - Force system into 1 port mode somehow. Options:
    - Release a version that only has 1 port
    - Have SPJS force close a data only port 
      - Able to interrogate a port to find out if it's ctrl, data, or ctrl+data
    - Have the second port only appear as a pro-active command from SPJS
  - We need g2 to manage ctrl / data channels internally (packet mode). 
    - Appears as streaming to the sender
    - Sender is still responsible for application level flow control based on byte counts 
  - What we do:
    - Port discovery tool
    - We will re-write the dual port hiding
    - Streaming / packet mode

- Middle Term Solution
  - Low-level communications API
  - Exposed as a driver (?)
  - Capable of presenting 1 or 2 channel models
  - Does it buffer and send, or only route?
    - Buffering: buffering and file handling, etc.

- Long Term Solution
  - High-level, Object Oriented API supporting:
    - Jobs
    - In-Cycle controls
    - Configuration 
    - Machine State
