This page describes how to set up configuration for a Dual gantry setup. The example given is a Shapeoko with a Dual Y gantry.

## Overview
A dual gantry setup uses 2 motors to drive a single axis. Commonly the dual gantry is the Y axis, but not always. The 4 motors on TinyG can be used to support a dual Y gantry like so:
   
	Motor | Axis 
	------|------
	Motor1 | X Axis
	Motor2 | Y1 Axis
	Motor3 | Y2 Axis
	Motor4 | Z Axis

The Y2 axis has reverse polarity from Y1 as the 2 motors are facing each other and therefore need to turn in the opposite direction from each other.

## Settings
Here are some example settings for a Shapeoko dual Y gantry setup. You will probably need to experiment to get exact settings for your machine, but this is a good starting point. The maximum velocities and feed rates are set intentionally low and can probably be set higher with tuning. 

Note that the 'A' axis is not used. Only the 4 motors and the X, Y and Z axes.

#### Motor 1 (X Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$1ma=0 | Map to X axis| 0=X axis, 1=Y axis, 2=Z axis
	$1sa=1.8 | Step angle in degrees | Set 1.8 for 200 step / revolution motors, 0.9 for 400 step / revolution
	$1tr=36.54 | Travel per rev in mm | This value should be calibrated to your setup
	$1mi=8 | Microsteps to 8 | Experiment with 4x microstepping for more power
	$1po=0 | Polarity normal | Set polarity so X travels to the right for positive values (G0 X10)
	$1pm=0 | Power management mode | 0=axis remains powered when idle

#### Motor 2 (Y1 Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$2ma=1 | Map to Y axis| 
	$2sa=1.8 | Step angle in degrees |
	$2tr=36.54 | Travel per rev in mm | This value should be calibrated to your setup
	$2mi=8 | Microsteps to 8 | Try 4x for more power
	$2po=0 | Polarity normal | Set polarity so Y travels to the rear of the machine for positive values (G0 Y10)
	$2pm=0 | Power management mode | 0=axis remains powered when idle

#### Motor 3 (Y2 Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$3ma=1 | Map to Y axis| Same as Motor 2
	$3sa=1.8 | Step angle in degrees |
	$3tr=36.54 | Travel per rev in mm | MUST BE THE SAME AS MOTOR 2
	$3mi=8 | Microsteps to 8 | Should be the same as motor 2
	$3po=1 | Polarity reversed | MUST BE OPPOSITE MOTOR 2
	$3pm=0 | Power management mode | Should be the same as motor 2

#### Motor 4 (Z Axis)

	Setting | Description | Notes
	--------|-------------|-------
	$4ma=2 | Map to Z axis| 
	$4sa=1.8 | Step angle in degrees |
	$4tr=1.25 | Travel per rev in mm | The stock Shapeoko Z screw is 1.25 mm / rev
	$4mi=4 | Microsteps to 4 |
	$4po=0 | Polarity normal | Set polarity so Z travels up for positive values (G0 Z10)
	$4pm=1 | Power management mode | 1=axis shuts off when idle. Can also use $4pm=0

#### X Axis

	Setting | Description | Notes
	--------|-------------|-------
	$xam=1 | Axis mode normal |  
	$xvm=10000 | Velocity maximum | Max velocity for axis, aka "traverse rate" or "seek" 
	$xfr=10000 | Feed rate maximum | Maximum feed rate for that axis. Does NOT set the F word
	$xtm=220 | Travel maximum 220mm | Used by homing to know when to give up
	$xjm=5000000000 | Jerk maximum | That's 5 billion
	$xjh=10000000000 | Jerk homing | That's 10 billion - jerk used during homing operations
	$xjd=0.01 | Junction deviation | For cornering control
	$xsn=1 | Minimum switch mode | 1=homing-only
	$xsx=0 | Maximum switch mode | 0=disabled
	$xsv=3000 | Search velocity | Homing speed during search phase (drive to switch)
	$xlv=100 | Latch velocity | Homing speed during latch phase (drive off switch)
	$xzb=3 | Zero backoff | offset from switch for zero in absolute coordinate system

#### Y Axis
Y axis commands will drive both Y motors.

	Setting | Description | Notes
	--------|-------------|-------
	$yam=1 | Axis mode normal |  
	$yvm=10000 | Velocity maximum |
	$yfr=10000 | Feed rate maximum | 
	$ytm=220 | Travel maximum 220mm | 
	$yjm=5000000000 | Jerk maximum | That's 5 billion
	$yjh=10000000000 | Jerk homing | That's 10 billion - jerk used during homing operations
	$yjd=0.01 | Junction deviation | For cornering control
	$ysn=1 | Minimum switch mode | 1=homing-only
	$ysx=0 | Maximum switch mode | 0=disabled
	$ysv=3000 | Search velocity | Homing speed during search phase (drive to switch)
	$ylv=100 | Latch velocity | Homing speed during latch phase (drive off switch)
	$yzb=3 | Zero backoff | offset from switch for zero in absolute coordinate system

#### Z Axis

	Setting | Description | Notes
	--------|-------------|-------
	$zam=1 | Axis mode normal |  
	$zvm=600 | Velocity maximum |
	$zfr=600 | Feed rate maximum | 
	$ztm=100 | Travel maximum 220mm | 
	$zjm=50000000 | Jerk maximum | That's 50 million
	$zjh=100000000 | Jerk homing | That's 100 million - jerk used during homing operations
	$zjd=0.01 | Junction deviation | For cornering control
	$zsn=0 | Minimum switch mode | 0=disabled
	$zsx=1 | Maximum switch mode | 1=homing-only
	$zsv=600 | Search velocity | Homing speed during search phase (drive to switch)
	$zlv=100 | Latch velocity | Homing speed during latch phase (drive off switch)
	$zzb=3 | Zero backoff | offset from switch for zero in absolute coordinate system

#### Additional Settings

	Setting | Description | Notes
	--------|-------------|-------
	$ja=2000000 | Junction acceleration mode normal | max centripetal acceleration around corners
