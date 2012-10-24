_These sections will probably move to separate pages once they are big enough. For now, let's just start typing._

Last updated by: Alden - 10/24/12 - 8:55 EST

##Requirements (problem domain)

1. Failsafe protocol capable of driving big, dangerous machines
1. Eliminate the buffer overflows
1. Provide a reliable way to indicate that the error was hit
1. Provide a mechanism to detect and retransmit in case of communications problems or other command corruption
1. Provide a way for the UI to be aware of and therefore manage the number of blocks in the planner buffer
1. Offer a protocol that will work from a variety of transports, including USB serial, TCP/IP, and direct file reads (SD cards)
1. Find the balance between minimizing communications overhead and providing human readable (debuggable) and easily parsable data structures
1. Take advantage of full duplex communications (i.e. avoid half duplex solutions)

##Design (solution domain)

1. Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues
1. 

##References
*[RFC1122](http://tools.ietf.org/html/rfc1122)

