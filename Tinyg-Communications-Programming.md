If you are on this page we can assume you want to write a program that talks to TinyG to send Gcode files, and possibly also to read and set configuration variables, report machine status, control jogging and homing, and other functions.

If you are just looking to use an off-the-shelf solution please see these other links
* [Sending Files with CoolTerm](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-CoolTerm)<br>
* [Sending Files with tgFX](https://github.com/synthetos/TinyG/wiki/TinyG-Sending-Files-with-tgFX)<br>

#Communications Basics
##Theory of Operation
TinyG communicates over USB serial. The default baud rate is 115,200 baud, but can be set to values between 9600 and 230,400. 



##Flow Control Options