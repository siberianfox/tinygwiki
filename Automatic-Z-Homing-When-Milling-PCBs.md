_(This page originated by John Lauer)_

There is a great trick you can use on the TinyG to get to a perfect 0 on the z-axis when milling PCBs. You place your endmill into the collet. Attach an alligator clip to the endmill connected by a wire to one of the pins on the Z Min Switch. Solder or tape a wire connected to the other Z Min Switch pin to your PCB board that you are about to mill. Then issue a G28.2 Z0 command to the TinyG. This will move the Z axis in the negative direction until the circuit is completed. Once the endmill touches the PCB it will be perfectly zeroed out.

Here's a video of the process.

[![Screenshot](http://i1.ytimg.com/vi/Pe79ZVDr0gY/0.jpg?time=1392103493463)](http://youtu.be/Pe79ZVDr0gY)

To compare this technique, some people use the trick of (1) putting their endmill loosely into the collet, (2) hand tightening, (3) jogging the z-axis close to the PCB board, (4) hand loosening, (5) letting the endmill drop down onto the PCB, and (6) then fully tightening. For me this creates too many steps. Moreover, on my CNC machines you can't tighten the collet at the 0 z position, but rather you have to raise it up. On my Shapeoko I run into this issue. So, it adds about 3 or 4 more steps to tighten the collet for a total of 10 steps. With the TinyG trick described above I'm down to about 3 steps instead.

Hope you enjoy the technique.