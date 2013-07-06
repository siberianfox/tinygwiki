# Dual Gantry Configuration
This page describes how to set up configuration for a Dual gantry setup. The example given is a Shapeoko with a Dual Y gantry.

## Theory
A dual gantry setup uses 2 motors to drive a single axis. The 4 motors on TinyG can be used like so:
   
	Motor | Axis | Notes
	------|------|-------------
	Motor1 | X Axis | 
	Motor2 | Y1 Axis | 
	Motor3 | Y2 Axis | Y2 axis has reverse polarity from Y1 
	Motor4 | Z Axis | 
