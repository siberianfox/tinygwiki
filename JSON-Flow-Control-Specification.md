_These sections will probably move to separate pages once they are big enough. For now, let's just start typing._

Last updated by: Alden - 10/24/12 - 8:55 EST

##Requirements (problem domain)

Provide a failsafe protocol reliable enough to drive big, dangerous machines

1. Provide flow control to eliminate the buffer overflows: w/packet acknowledgement
1. Provide a reliable way to detect and report that an error was encountered
1. Provide a retransmission mechanism in case of communications problems or other command corruption
1. Provide ordered delivery: sequence numbers needed to track packets
1. Provide a way for the UI to be aware of and therefore manage the number of blocks in the planner buffer
1. Offer a protocol that will work from a variety of transports including USB serial, TCP/IP, and direct file reads (SD cards)
1. Find the balance between minimizing communications overhead and providing human readable (debuggable) and easily parsable data structures
1. Take advantage of full duplex communications (i.e. avoid half duplex solutions)

some useful features for our connectionless protocol:
- flow control: packet acknowledgement

##Design (solution domain)

1. Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues
1. Use a datagram packetized design: JSON blocks and gcode blocks are a natural packet
1. Use a checksum for packet verification. Using the Java Hashcode algorithm


##References
* [RFC1122](http://tools.ietf.org/html/rfc1122)
* [Wikipedia OSI Transport Comparison](http://en.wikipedia.org/wiki/Transport_layer#Comparison_of_OSI_transport_protocols)
* 

