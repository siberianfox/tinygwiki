This page describes how to set up configuration for a Dual gantry setup. The example given is a Shapeoko with a Dual Y gantry.

## Overview
A dual gantry setup uses 2 motors to drive a single axis. The 4 motors on TinyG can be used like so:
   
	Motor | Axis 
	------|------
	Motor1 | X Axis
	Motor2 | Y1 Axis
	Motor3 | Y2 Axis
	Motor4 | Z Axis

The Y2 axis has reverse polarity from Y1 as the 2 motors are facing each other and therefore need to turn in the opposite direction from each other.

## Settings
Here are some example settings for a Shapeoko dual Y gantry setup. You will probably need to experiment to get exact settings for your machine, but this is a good starting point. The maximum velocities and feed rates are set intentionally low and can probably be set higher with tuning. 

#### Motor 1 (X Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$1ma=0 | Map to X axis| 0-3 will map motors X,Y,Z,A, respectively
	$1sa=1.8 | Step angle in degrees | Set 1.8 for 200 steps/revolution motors, 0.9 for 400 steps/rev
	$1tr=36.54 | Travel per rev in mm | This value should be calibrated to your setup
	$1mi=8 | Microsteps to 8 | Experiment with 4 for more power
	$1po=0 | Polarity normal | Set polarity so X travels to the right for positive values (G0 X10)
	$1pm=0 | Power management mode | 0=axis remains powered when idle

#### Motor 2 (Y1 Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$2ma=1 | Map to Y axis| 
	$2sa=1.8 | Step angle in degrees |
	$2tr=36.54 | Travel per rev in mm | This value should be calibrated to your setup
	$2mi=8 | Microsteps to 8 | Experiment with 4 for more power
	$2po=0 | Polarity normal | Set polarity so Y travels to the rear of the machine for positive values (G0 Y10)
	$2pm=0 | Power management mode | 0=axis remains powered when idle

#### Motor 3 (Y2 Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$3ma=1 | Map to Y axis| Same as Motor 2
	$3sa=1.8 | Step angle in degrees |
	$3tr=36.54 | Travel per rev in mm | MUST BE THE SAME AS MOTOR 2
	$3mi=8 | Microsteps to 8 | Should be the same as motor 2
	$3po=0 | Polarity normal | MUST BE OPPOSITE MOTOR 2
	$3pm=0 | Power management mode | 0=axis remains powered when idle

#### Motor 4 (Z Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$4ma=2 | Map to Z axis| 
	$4sa=1.8 | Step angle in degrees |
	$4tr=1.25 | Travel per rev in mm | The stock Shapeoko Z screw is 1.25 mm / rev
	$4mi=4 | Microsteps to 8 | use 4 for more power 
	$4po=0 | Polarity normal | Set polarity so Z travels up for positive values (G0 Z10)
	$4pm=1 | Power management mode | 1=axis shuts off when idle. Can also use $4pm=0


## Axis Groups
Settings specific to a given axis. There are 6 axis groups, one for each of X,Y,Z,A,B,C. Not all axes have all parameters.

	Setting | Description | Notes
	--------|-------------|-------
	[$xam](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xam---axis-mode) | Axis mode | See details for setting. Normally this is =1 "normal" 
	[$xvm](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xvm---velocity-maximum) | Velocity maximum | Max velocity for axis, aka "traverse rate" or "seek" 
	[$xfr](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xfr---feed-rate-maximum) | Feed rate maximum | Sets maximum feed rate for that axis. Does NOT set the F word
	[$xtm](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xtm---travel-maximum) | Travel maximum | Used by homing to know when to give up
	[$xjm](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xjm---jerk-maximum) | Jerk maximum | main parameter for acceleration management (Note: takes the place of a max acceleration value)
	[$xjh](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xjh---jerk-homing) | Jerk homing | jerk used during homing operations. On axes XYZA only
	[$xjd](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#xjd---junction-deviation) | Junction deviation | For cornering control
	[$ara](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#ara---radius-value) | Radius setting | Rotational axes only (ABC only)
	[$xsn](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#homing-settings) | Minimum switch mode | 0=disabled, 1=homing-only, 2=limit-only, 3=homing-and-limit (XYZA only)
	[$xsx](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#homing-settings) | Maximum switch mode | 0=disabled, 1=homing-only, 2=limit-only, 3=homing-and-limit (XYZA only)
	[$xsv](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#homing-settings) | Search velocity | Homing speed during search phase (drive to switch) (XYZA only)
	[$xlv](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#homing-settings) | Latch velocity | Homing speed during latch phase (drive off switch) (XYZA only)
	[$xzb](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration#homing-settings) | Zero backoff | offset from switch for zero in absolute coordinate system (XYZA only)