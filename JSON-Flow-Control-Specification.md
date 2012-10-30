    Revised by: Estee - 10/26/12 - 12:10 AM PST
    Revised by: Alden - 10/27/12 - 9:00 PM EST - started a section on command types
    Revised by: Alden - 10/29/12 - 6:36 PM EST - took a crack at revised Ack and Qr packet formats before the power went out

#Requirements

Paraphrasing Mike: _We want a high speed, reliable, fail safe transport and control protocol for tinyG so that it can control large machines._ (Edit: because that's awesome. --mikest)

1. Provide flow control to eliminate the buffer overflows: w/packet acknowledgement
1. Provide a reliable way to detect and report that an error was encountered, and not change system 
state on corrupt input data
1. Provide a retransmission mechanism in case of communications problems or other command corruption
1. Provide ordered delivery: sequence numbers needed to track packets
1. Provide a way for the UI to be aware of and therefore manage the number of blocks in the planner buffer
1. Offer a protocol that will work from a variety of transports including USB serial, TCP/IP, and direct file reads (SD cards)
1. Find the balance between minimizing communications overhead and providing human readable (debuggable) and easily parsable data structures
1. Take advantage of full duplex communications (i.e. avoid half duplex solutions) (Edit: wireless? many transport layers are half-duplex).
1. Structure the solution such that it works naturally within [REST](http://en.wikipedia.org/wiki/Representational_state_transfer) constraints (excluding code-on-demand) 
1. Always maintain control of the machine while streaming. _halt_ & _feedhold_

#Design

##Command Types
Most commands sent to TinyG fall into one of two broad types:

	Type  | Examples    | Notes
	-------|-------------|-------------------------
	Cycle  | G0, G1, G2, G3, G4, G20, G21, G90, G91 ... | Cycle commands are executed in a Gcode machine cycle. Most cycle commands are queued to the planner buffer (e.g. G0 or G1), but some are not (e.g. G20 or G21). Cycle commands are accepted at any time during machine operation (see Cycle Start and Auto-Cycle Start). _Note: Whether a cycle command is queued or not, the order-of-arrival is preserved. For example, a G91 to set incremental mode will only affect the current and later Gcode block - it does not affect commands already in the queue._
	Config  | "$" cmds, e.g: $xvm=10000, {"xvm":10000}  | Config commands set the machine configuration. Config commands are not accepted during a cycle and will return an error if attempted in-cycle.

Additional command Types and some exceptions are noted below:

	Type  | Examples    | Notes
	-------|-------------|-------------------------
	Off-cycle | G28.1 | Some machine commands are not executed in cycle. Homing is the notable example. Currently homing is mapped to G28.1, which makes it appear as a Cycle command. Technically this is not correct. Homing is a "front panel function" that actually has no Gcode equivalent. This function should be broken out so that it cannot occur in a Gcode cycle - and therefore should not be in a Gcode "file".
	Async | `!`, `~`,`^x` | Feedholds, cycle starts and aborts are asynchronous commands that can arrive at any time (in cycle or out).  See their respective definitions for details.
	Startup | RESET,`^x` | System startup will return two startup responses. (1) the initialization response, and (2) the system ready response. See Startup Messages for details.
	G10s | G10 L2 | G10 commands are "Official" gcode commands that set machine parameters. G10 L2 is the only G10 command implemented in TinyG, and it updates the Gcode coordinate system offsets for the G54 - G59 coordinate systems. In this regard it is redundant with the $G54x=100.... series of commands. Technically the G10s should be Config commands that cannot be executed while in cycle, but Gcode defines these commands and they are valid in Gcode files and therefore valid in cycles. G10s are handled specially. They are accepted as Cycle commands and take effect immediately but the Non-Volatile-Memory (NVM) update is deferred until after the cycle is complete. The update is silent and does not return an ACK when it occurs.

_(Note: To insert that initial TAB for the table (at least on a mac) requires CTRL OPTION TAB). But this only works on my laptop and not on my iMac_

##JSON Mode Protocol
### Commands
Commands in JSON mode are sent as JSON packets which may unwrapped or wrapped.

Unwrapped form presents the command as-is, with no body or footer. Examples:

    {"x":""}<lf>          get the X resource
    {"xvm":12000}<lf>     set the X maximum velocity to 12000 mm/min (assuming the system is in G21 mode)
    {"gc":"g0 x100"}<lf>  execute the Gcode block "G0 X100"

Wrapped form presents the command in a body and footer wrapper. This form is useful for noisy environments to ensure proper command transfer. The above examples become:

    {"b":{"x":""},"f":[1,0,255,1234]}<lf>
    {"b":{"xvm":12000},"f":[1,0,255,1234]}<lf>
    {"b":{"gc":"g0 x100"},"f":[1,0,255,1234]}<lf>
    
    where  "f":[1,0,255,1234]  
       is  "f":[<protocol_version>, <status_code>, <input_available>, <checksum>]
 
The footer contains a 4 element array of packet flow control information as per the following:

	Element  | Notes
	-------|-------------------------
	protocol_version | Initially set to 1. This number will be incremented as protocol changes are made that would affect UI clients
	status_code | 0 is OK. All others are exceptions. See tinyg.h for details
	input_available | Indicates how many characters are available in the input buffer. Do not use this for stuffing the queue, however - See Command Synchronization. (Technically this element is superfluous in this incarnation, but may not be for more advanced transports) 
	checksum | A 4 digit checksum for the line. The checksum is generated for the JSON line up to but not including the comma preceding the checksum itself. I.e, the comma is where the nul termination would exist. The checksum is computed as a [Java hashcode](http://en.wikipedia.org/wiki/Java_hashCode()) from which a modulo 9999 is taken to fix the length to 4 characters. The modulus result is left zero padded to ensure all results are 4 digits. See compute_checksum() in util.c for C code.

### Ack Responses
Every JSON command returns an acknowledgement response (Ack). Acks are returned according to the command type.

	Command Type  | Notes
	-------|-------------------------
	Cycle | An ack is returned when the command is successfully accepted - either executed or put on the planner queue. A negative acknowledgement (NAK) is an Ack with a non-zero status code, and may be returned at any time during processing.
	Config | An ack is returned when the command has been successfully executed or has failed (NAK).
	Off-Cycle | An ack is returned when the command is accepted for processing. Off cycle commands may also generate queue reports and status reports depending on the command and configuration settings.
	Async | Async commands do not generate Acks. The results of feedhold and cycle start are apparent only by queue reports or status reports. Aborts will send the system through the startup messages.

####Ack format is:

    {"b":<body>,"f":[<protocol_version>,<status_code>,<input_available>,<checksum>]}<lf>

The body echos the command that was sent, in normalized form (all caps, no whitespace). If echo is disabled (ee=0) the body returns null in this format: "b":""

The footer is the same structure as described for the Command.

### Queue Reports
Queue reports are generated asynchronously if queue reports are enabled. If enabled, each command in the planner queue will be reported when it has finished execution (completed). Format is:

    {"b":{"qr":{"lx":0,"pb":24}},"f":[<protocol_version>,<status_code>,<input_available>,<checksum>]}<lf>

Where 'lx' is the line index and 'pb' is the available blocks in the planner buffer

### Status Reports
Status reports may be enabled to report machine position and state during movement. These are designed for display and feedback only and should not be used for flow control purposes.

### Startup Messages
There are 3 startup messages.

Loading configs from EEPROM is the normal initialization message. A status code of 15 (Initializing) means the system is initializing and is not ready for use. Do not send any commands yet.

    {"b":{"fv":0.950,"fb":343.020,"msg":"Loading configs from EEPROM"},"f":[1,15,255,3594]}

Initializing configs from a profile occurs if EEPROM is not initialized, is detected to be corrupted, or if a new firmware build is encountered. Status code is 15, so don't send commands.

    {"b":{"fv":0.950,"fb":343.020,"msg":"Initializing configs to Shapeoko 375mm profile"},"f":[1,15,255,9350]}

System ready is sent following either of the above messages. Status code 0 (OK) means it's OK to send commands.

    {"b":{"fv":0.950,"fb":343.020,"msg":"SYSTEM READY"},"f":[1,0,255,6586]}

### Command Synchronization
The basic rule is: **Only one command should be sent at a time.** 

The client should wait for an Ack response before sending the next command - i.e. commands should not be buffered. Given that Cycle commands Ack when they are queued to the planner buffer this mode of operation still supports filling the planner queue.

By following this behavior the client can expect Ack responses to be returned for all commands within 100 milliseconds or less - with the following exception: Cycle commands that queue to the planner will block until there is space in the planner queue and will not return an Ack until that command has been placed on the queue (or failed to do so). This behavior allows the client to handle non-responses in a coherent manner using compensating transactions (See Command Replay and Idempotency, below)

_(Note: some of this has to do with working around the severe non-volatile memory errata to the xmega family that requires you to shut down interrupts during writes to NVM. This type of operation ensures that no characters are lost during these intervals)_. 

### Command Replay and Idempotency
Idempotency is the behavior that a command sent twice will not change the state of the system - i.e. it has the effect of only sending the command once. Understanding the idempotency of a system enables a set of "compensating transactions" to manage transmission errors and non-response cases.

In TinyG, all GET commands are idempotent. That is - any read operation can be performed multiple times without fear of changing system state.

In TinyG, most write commands are idempotent, but not all (due to Gcode operation), and only if you obey certain rules. Rule #1: Only one command should be sent at a time. If this is true then a wide variety of compensating transactions are possible. 

_NB: In keeping with REST thinking, any idempotent write command is considered a PUT, and non-idempotent write command is considered a POST_

The thought process for deciding which Gcode commands are idempotent, and under what circumstances is to ask "If I sent this command twice would it cause the system to be in a different state than if I only sent it once?"

_Need more details of idempotentcy here. Or do we?_

###Async Commands

* Async Feedhold (`!`) Feedholds are accepted at any point in a cycle and ere executed immediately (i.e. not synchronized to the planning queue). The response to a feedhold will be a Q report for the move that was halted.

* Cycle Start (`~`) Cycle start begins a cycle or resumes a cycle from a feedhold. There is no response to a cycle start. Note that cycle starts may also be generated automatically by the system under certain circumstances - i.e. when the planner begins to fill it will automatically start a cycle at some point unless it has been already started by an explicit cycle start command. 

* Abort (`^x`) An abort performs a software reset of the machine. All position and state are lost. Response to a abort is a pair of system startup messages - an init message and a ready message.








#Historical Notes
##Design Considerations

1. Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues. Instead use a token based flow control algorithm.

1. Use a datagram packetized design: JSON blocks and gcode blocks are a natural packet. The <LF> is the packet (line) terminator 

1. Use a checksum for packet verification. Using the Java Hashcode algorithm. See calculate_hash() in util.c

1. Implement idepempotency for GETs and PUTs. For GETs this means that requesting the same data element or resource (data group) multiple times will not change the state of the firmware. for PUTs this means that writing the same packet to the system more than once will not change the state beyond the first PUT. While PUT idempotency is possible for most commands, there are some Gcode modes where this is not possible - e.g. motion in relative mode, and some cycle starts like a homing cycle start (interesting - we could MAKE it behave idempotently). These exception cases should be documented and in true REST fashion they should be invoked using POST, not PUT.  How this is done is the subject of other design features.

1. Use a token based flow control algorithm to manage both input buffer size and planner buffer size. Note that managing input buffer size is _insufficient_ to managing planner buffer size in that the input buffers blocking when space is not available in the planner buffer _would cause a loss of machine control on planing buffer overflow._

###Matt's comments on one way to do this
Here's one approach, essentially stolen from tried-and-true network 
protocols: 

Add a sequence number on the TinyG end.  It might already exist to count 
the input lines. 
Add a means of synchronizing sequence numbers on both sides as part of 
initialization. 
Add a wrapper for host messages similar to "r" and "bd" which contains a 
sequence number and checksum.  For every line received, TinyG: 
1.  Checks checksum.  If fail, discard line and transmit FAIL message 
with sequence number. 
2.  Checks received seq number.  If not == to current seq number, 
discard line and transmit FAIL message with desired seq number. 
3.  Increment local seq number. 

On completion of a line, send qr containing the line's sequence number 
along with current stuff (this might replace lix and pbr). 

On the host side, you need to deal with essentially a sliding window 
with slow start - you don't know how big a window to use. So you might 
start with only 2-3 lines being sent at first, until you get an ACK.   
Then you keep growing the window until you start getting 
retransmissions, and then you make the window a little smaller. 

###Mike's proposal

i would propose a packetized tinyG protocol: 

// 8 byte header + N bytes for json / gcode block 
struct tinyG_block_packet { 
        uint8  packet_type; // always zero for now, never know when you'll need to expand... 
        uint24 sequence_number;                // at 32bits we base64 will add another 3 bytes to our header. need to detect roll-over, could probably be reduced to 8bit then. 
        uint16 checksum; 
        uint8 whitespace;                // always a space, so parser knows where end of header is. 
        char *packet;                // newline/null terminated string 
} 

but! since we want to preserve the plain text readability, the header should be base64 encoded. this creates a 9 byte header after encoding the first 6 bytes (8+1 for whitespace delimiter). not bad for a sequence number *and* a checksum. transmit/receive line blocks might look like

        MGJGZmZm {"gc":"g00x150y100"}}\n                              (31 chars)
        aGVhZGVy {"r":{"bd":{"qr":{"lix":2513,"pba":24}},"sc":0}}\n 
        YW5vdGg= {"r":{"ack":0,"sc":0}}\n 

a little more compact than: 

        {"r":{"gc":"g00x150y100"},"sn":"234923","cks":"23049898234"}\n  (61 chars) 
        {"r":{"bd":{"qr":{"lix":2513,"pba":24}},"sn":"234923","sc":0,"cks":"2853327449"}} 
        {"r":{"ack":0,"sn":"234923","sc":0,"cks":"2853327449"}} 

if the header is missing, (because it was typed by hand) tinyG should be able to recover. 

Alden's comments: How about making it so the whole thing is parsable by off-the-shelf JSON parsers, even if the base64 block is opaque and requires further handling. Also, shorten the tags to 1 character, since parsing is always context-dependent we only need to worry about collisions in the context of what can co-exist in any given packet. "q" = reQuest, "a" = Ack, "s" = Status, "h" = Header.

        {"q":{"gc":"g00x150y100"}},"h":"MGJGZmZm"}\n                   (44 chars) 
        {"r":{"b":{"q":{"x":2513,"a":24,"h":"aGVhZGVy"}},"sc":0}}\n
        {"r":{"a":0,"s":0,"h":"YW5vdGg="}}\n 


###Mike's Packet Format Notes:
All messages sent from TinyG are of the format:

    {"b":<body>,"f":"<b64-footer><space><b64-hashcode>"}<lf>

The footer contains packet flow control information. As its only useful for machine comms, it doesn't need to be expanded out into plain text, and as such is a base 64 encoded structure. A client can pretty print it if that's useful. As the checksum can be part of the packet footer (it can't include itself) its added after the first base4 block. The checksum is computed as a Java hashcode.

The structure looks like:

    typedef struct {
       uint8 protocol_version;   // zero for now
       uint8 status_code;        // success, fail?
       uint8 input_available;    // number of free bytes in tinyG's input buffer. client is free to send up to this many until it has been told otherwise.
    } tinyg_packet_footer_t;

(Alden: the "r" wrapper is redundant, every message from tinyG should be a response. Echo is not useful for the json stream.) Understood --Alden

A line would go from looking like so:

    {"r":{"bd":{"qr":{"lix":0,"pba":24}},"sc":0,"cks":"2895404244"}}

to so:

    {"b":{"qr":{"lix":0,"pba":24}},"f":"ZmZm ZmY="}

The `lix` element is the line index. It is incremented for every command that is inserted to the planner buffer. It is returned when that command has completed executing. The `pba` element indicates the number of buffers available in the planner. These 2 elements can be used by the host to manage the depth of the planner buffer.


##References
* [RFC1122](http://tools.ietf.org/html/rfc1122)
* [Wikipedia OSI Transport Comparison](http://en.wikipedia.org/wiki/Transport_layer#Comparison_of_OSI_transport_protocols)
* [TCP's use of sequence numbers](http://packetlife.net/blog/2010/jun/7/understanding-tcp-sequence-acknowledgment-numbers/)
* [Sequence Number SN for 32 bits](http://mike.passwall.com/networking/tcppacket.html): In a sliding window protocol like TCP, the sequence number allows both TCP stacks to know what packets have been received and which ones have not. Say for instance I get mail messages 1,2,3,5,6,7,8,9, and 10 from you when I know you are sending 10 messages. If you numbered each of your messages, I can look through and see that I do not have message number 4, and I can tell you to send me another copy of that.
* [RTP RFC](http://tools.ietf.org/html/rfc1889)
* [Wikipedia Sliding Window](http://en.wikipedia.org/wiki/Sliding_window) See the "Go Back N" approach