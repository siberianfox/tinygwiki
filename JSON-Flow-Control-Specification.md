_These sections will probably move to separate pages once they are big enough. For now, let's just start typing._

Last updated by: Alden - 10/24/12 - 8:55 EST

##Requirements

* Failsafe protocol capable of driving big, dangerous machines
* Eliminate the buffer overflows
* Provide a reliable way to indicate that the error was hit
* Provide a mechanism to detect and retransmit in case of communications problems or other command corruption

##Design

* Do not use XON/XOFF. It's not uniformly supported and is too dependent on host timing issues
*

