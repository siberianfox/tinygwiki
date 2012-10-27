_These sections will probably move to separate pages once they are big enough. For now, let's just start typing._

Last updated by: Estee - 10/26/12 - 12:10 PST

##Requirements (problem domain)

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

##Design (solution domain)
###Packet format
All messages sent from TinyG are of the format:

    {"b":<body>,"f":"<b64-footer><space><b64-hashcode>"}<lf>

The footer contains packet flow control information. As its only useful for machine comms, it doesn't need to be expanded out into plain text, and as such is a base 64 encoded structure. A client can pretty print it if that's useful. As the checksum can be part of the packet footer (it can't include itself) its added after the first base4 block. The checksum is computed as a Java hashcode.

The structure looks like:

    typedef struct {
       uint8 protocol_version;   // zero for now
       uint8 status_code;        // success, fail?
       uint8 input_available;    // number of free bytes in tinyG's input buffer. client is free to send up to this many until it has been told otherwise.
    } tinyg_packet_footer_t;

(Alden: the "r" wrapper is redundant, every message from tinyG should be a response. Echo is not useful for the json stream.)

A line would go from looking like so:

    {"r":{"bd":{"qr":{"lix":0,"pba":24}},"sc":0,"cks":"2895404244"}}

to so:

    {"b":{"qr":{"lix":0,"pba":24}},"f":"ZmZm ZmY="}

--- Insert Design Here ---

1. Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues. Instead use a token based flow control algorithm.

1. Use a datagram packetized design: JSON blocks and gcode blocks are a natural packet. The <LF> is the packet (line) terminator 

1. Use a checksum for packet verification. Using the Java Hashcode algorithm. See calculate_hash() in util.c

1. Implement idepempotency for GETs and PUTs. For GETs this means that requesting the same data element or resource (data group) multiple times will not change the state of the firmware. for PUTs this means that writing the same packet to the system more than once will not change the state beyond the first PUT. While PUT idempotency is possible for most commands, there are some Gcode modes where this is not possible - e.g. motion in relative mode, and some cycle starts like a homing cycle start (interesting - we could MAKE it behave idempotently). These exception cases should be documented and in true REST fashion they should be invoked using POST, not PUT.  How this is done is the subject of other design features.

1. Use a token based flow control algorithm to manage both input buffer size and planner buffer size. Note that managing input buffer size is _insufficient_ to managing planner buffer size in that the input buffers blocking when space is not available in the planner buffer _would cause a loss of machine control on planing buffer overflow._


##Historical Notes
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


##References
* [RFC1122](http://tools.ietf.org/html/rfc1122)
* [Wikipedia OSI Transport Comparison](http://en.wikipedia.org/wiki/Transport_layer#Comparison_of_OSI_transport_protocols)
* [TCP's use of sequence numbers](http://packetlife.net/blog/2010/jun/7/understanding-tcp-sequence-acknowledgment-numbers/)
* [Sequence Number SN for 32 bits](http://mike.passwall.com/networking/tcppacket.html): In a sliding window protocol like TCP, the sequence number allows both TCP stacks to know what packets have been received and which ones have not. Say for instance I get mail messages 1,2,3,5,6,7,8,9, and 10 from you when I know you are sending 10 messages. If you numbered each of your messages, I can look through and see that I do not have message number 4, and I can tell you to send me another copy of that.
* [RTP RFC](http://tools.ietf.org/html/rfc1889)
* [Wikipedia Sliding Window](http://en.wikipedia.org/wiki/Sliding_window) See the "Go Back N" approach
