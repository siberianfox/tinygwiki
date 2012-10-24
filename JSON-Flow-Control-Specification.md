_These sections will probably move to separate pages once they are big enough. For now, let's just start typing._

Last updated by: Alden - 10/24/12 - 8:55 EST

##Requirements (problem domain)

Provide a failsafe protocol reliable enough to drive big, dangerous machines

1. Provide flow control to eliminate the buffer overflows: w/packet acknowledgement
1. Provide a reliable way to detect and report that an error was encountered, and not change system 
state on corrupt input data
1. Provide a retransmission mechanism in case of communications problems or other command corruption
1. Provide ordered delivery: sequence numbers needed to track packets
1. Provide a way for the UI to be aware of and therefore manage the number of blocks in the planner buffer
1. Offer a protocol that will work from a variety of transports including USB serial, TCP/IP, and direct file reads (SD cards)
1. Find the balance between minimizing communications overhead and providing human readable (debuggable) and easily parsable data structures
1. Take advantage of full duplex communications (i.e. avoid half duplex solutions)
1. Structure the solution such that it works naturally with REST principles

##Design (solution domain)

1. Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues

1. Use a datagram packetized design: JSON blocks and gcode blocks are a natural packet. The <LF> is the packet (line) terminator 

1. Use a checksum for packet verification. Using the Java Hashcode algorithm. See calculate_hash() in util.c

1. Implement idepempotency for GETs and PUTs. For GETs this means that requesting the same data element or resource (data group) multiple times will not change the state of the firmware. for PUTs this means that writing the same packet to the system more than once will not change the state beyond the first PUT. While PUT idempotency is possible for most commands, there are some Gcode modes where this is not possible - e.g. motion in relative mode, and some cycle starts like a homing cycle start (interesting - we could MAKE it behave idempotently). These exception cases should be documented and in true REST fashion they should be invoked using POST, not PUT.  How this is done is the subject of other design features.

1. Use a host-generated sequence number and sliding window protocol to manage both input buffer size and planner buffer size. Note that managing input buffer size is sufficient to manage planner buffer size in that the input buffers block when space is not available in the planner buffer

##References
* [RFC1122](http://tools.ietf.org/html/rfc1122)
* [Wikipedia OSI Transport Comparison](http://en.wikipedia.org/wiki/Transport_layer#Comparison_of_OSI_transport_protocols)
* 

