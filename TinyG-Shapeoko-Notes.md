## Shapeoko and TinyG
Shopeoko and TinyG are a great fit. The combination make a good upgrade to support very smooth, fast motor operation, built-in support for dual Y axis configurations, and other enhancements. 

###What Does It Do?
TinyG's motion control is very smooth due to precise timing and constant jerk acceleration. This means a number of things to the Shapeoko user. TinyG has an optimized, low jitter step generation coupled with constant jerk acceleration management. As a result TinyG gets a lot out of your motors. If you think you need to upgrade from NEMA17 motors to something larger you may find that more precise control offered by TinyG is really all you need.

The constant jerk acceleration management also makes for extremely fast rapids (traverses), which helps cut down job times. It does all this with minimal shaking of the machine and toolhead, making for smoother cuts, better surface finish, and less change of skipping, chattering, or other artifacts.

<Insert video here>

###Tgfx
TinyG works with tgfx, a cross-platform control program available for Mac, Windows and Linux. 
_Need more text_

###Setup

Setting up Shapeoko to use TinyG is pretty straightforward. This page provides all the settings needed
https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup

A few things to keep in mind.

* TinyG treats motors independently from the axes. So it natively supports dual-y configurations. 2 motors map to the Y axis - and both are driven by Y axis controls. But they must be going in opposite directions (i.e. have reverse polarity settings) for the gantry to move as a unit. Polarity can be handled electrically by reversing one of the coil pairs on one of the motors, or under software control by setting the polarity motor parameter. I prefer firmware as this way all the motors are wired the same and are interchangeable.

##Tuning Shapeoko and TinyG
Once you are set up you can tune the Shapeoko/TinyG system for optimal performance. There is a page on the TinyG wiki about [tuning](https://github.com/synthetos/TinyG/wiki/TinyG-Tuning) that the following was adapted from. What follows are tuning instructions and guidance specifically for the Shapeoko/TinyG combination.

Tuning the machine is about getting the maximum performance from the system while setting the "envelope" in which the machine can work. The envelope defines the reliable limits on all parameters. TinyG is written such that if Gcode asks for more than the machine can deliver (e.g. too high a feed rate) the system will execute the Gcode to the best of its ability while not exceeding the performance envelope. So it's important to tune the machine so you avoid over-specified Gcode files causing jobs to fail.

###Mechanical
A well functioning mechanical system is the heart of tuning. The electrical system can at best compensate for the mechanical system, but can never fundamentally improve it. Here are a number of points to make sure the Shapeoko itself is tuned up.

_Bart - perhaps you can tweak this part. I'm sure you know 10x what I do in this area_

* Make sure the machine is in perfect alignment and belt tension is correct. All parts should be square and the belt axes (X and Y) should be tight but move with almost no resistance. Test that pulleys and wheels rotate freely and do not bind or wobble. Test that shaft couplers are well seated and tightened with minimum eccentric wobble ("runout").

It's a good idea to test slide resistance with no motors on the system to look for any rough spots in the slide, or any points where resistance is greater than others. If the motors are mounted at least make sure they are electrically disconnected and their winding leads are not shorted (as this will cause mechanical resistance to go way up) . 

* The Z axis should turn as smoothly as possible with no binding. Many people upgrade to an Acme screw for this reason. 

###Settings
Once the mechanical system is working well you can start in on the settings. Do these one axis at a time then in combination. All values are in millimeters using the X axis as an example. Other axes are similar.

The **velocity maximum** - aka **traverse rate** - is the top speed of a machine axis under no cutting load. Traverses (G0's) move the machine at the maximum velocity and generally don't change from job to job. A good maximum velocity will drive the motor reliably at high speed and allow for a little headroom where the motor is still running well. Attempting to set this rate above this speed may cause the motor to operate erratically, drop steps, or stall.<br><br>
Bear in mind that with traverses (G0) the actual speed of movement may well be above any of the traverse rates of the individual axes as it's the cartesian sum. For example, if xvm and yvm are set to 10,000 mm/min a G0 from (0,0) to (100,100) will actually run at 14,142 mm/min (assuming it has room to accelerate to the target velocity). 

The **feed rate** is the maximum cutting speed the axis can sustain for a given tooling, material and type of cut and may change considerably from job to job. The max feed rates set here are just an upper limit that a Gcode file cannot exceed. The actual control of feed rate should be done from the F words in the Gcode file itself. The max feed rates should be set lower than the maximum velocity and generally set after these have been set.

<pre>
CAVEAT: Be sure your machine is in mm distance mode before starting. 
The distance mode should be obvious from the command prompt:
  tinyg [mm] ok>      not:
  tinyg [inch] ok>

Enter G21 to change to mm mode (G20 to change to inches)
</pre>

###Axis Tuning
Axis tuning starts with getting good values for the following:

* Velocity Maximum ($xvm)
* Feed Rate Maximum ($xfr)
* Jerk Maximum ($xjm)
* Motor Currents (potentiometer settings)

**Steps**<br>
Do these steps for each axis in turn.

1. Ensure the Motor settings for step angle ($1sa), travel per revolution ($1tr), microsteps ($1mi), and polarity ($1po) are correct for each motor. Verify that the Axis settings for velocity maximum ($xvm) and jerk maximum $(xjm) are set correctly. If in doubt, go back to the [TinyG Shapeoko Setup page](https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup)

1. Locate the X axis current setting potentiometer on the TinyG board and start with it at the middle position - 12:00. Never force the potentiometer or exceed the potentiometers' range of motion, which is 270 degrees, or from about 8:00 to about 4:00. 

1. Test a traverse with a long G0 move, such as G0 X100 (Be sure you have 100 mm of clearance on the X axis before you do this!) The motor should accelerate, cruise at speed, then decelerate to a stop - all in less than 1 second. The motor should not stall or fail to start. Lower the velocity in increments of 1000 mm/min if this is the case. You may also need to back off the jerk max - try increments of 1 billion (e.g. 5000 --> 4000).

1. If the motor hums but doesn't start it's probably not getting enough current. Turn it up to 1:00 or 2:00. Alternately, if the motor stops and starts; or stutters; and the driver chips are excessively hot the motor is getting too much current. Turn the pot down a bit. Too much current will give just as bad results as too little. These drivers do not go to "11".

1. If the the motor more or less works but seems to be dropping steps it could be any of the mechanical system (too much friction), the current setting, or the velocity max being too high; or possibly some combination of all three. Experimentation is required. It's best to try to fix them in that order. Start with the mechanical system, then the current, then the setting.

1. Once you have the axis working you can see if raising the velocity ($xvm), the jerk ($xjm) and providing more current can increase the top speed. This is a case of experimenting. Try to avoid excessive current, however, as after a point the current setting provides diminishing benefits and only heats up the motors and risks thermal shutdown. 

It's worth noting that the mechanics of the axes may not be identical, and the achievable traverse rates may differ for each axis. You can set them optimally for each axis and moves in more than one dimension will takes the individual settings into account.

It's also worth noting that on some machines mechanical resistance is greater in some parts of the travel than others (e.g. more resistance at the ends of travel due to shaft coupler runout or other mechanical factors). Be sure to test the entire travel for each axis before finalizing the settings.

1. Once you have the velocity working, set the feed rates to the max velocity value or somewhat lower. The max feed rates often require adjustment for a given job or material as the cutting loads may vary. The traverse rates should not require job-by-job adjustment.
