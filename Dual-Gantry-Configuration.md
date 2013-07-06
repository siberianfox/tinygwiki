This page describes how to set up configuration for a Dual gantry setup. The example given is a Shapeoko with a Dual Y gantry.

# Theory
A dual gantry setup uses 2 motors to drive a single axis. The 4 motors on TinyG can be used like so:
   
	Motor | Axis 
	------|------
	Motor1 | X Axis
	Motor2 | Y1 Axis
	Motor3 | Y2 Axis
	Motor4 | Z Axis

The Y2 axis has reverse polarity from Y1 as the 2 motors are facing each other and therefore need to turn in the opposite direction from each other.