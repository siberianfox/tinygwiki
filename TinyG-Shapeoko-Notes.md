## Shapeoko and TinyG
Shopeoko and TinyG are a great fit. The combination make a good upgrade to support very smooth, fast motor operation, built-in support for dual Y axis configurations, and other enhancements. 

###What Does It Do?
TinyG's motion control is very smooth due to precise timing and constant jerk acceleration. This means a number of things to the Shapeoko user. TinyG has an optimized, low jitter step generation coupled with constant jerk acceleration management. As a result TinyG gets a lot out of your motors. If you think you need to upgrade from the stock NEMA17 motors to something larger you may find that more precise control offered by TinyG is really all you need.

The constant jerk acceleration management also makes for extremely fast rapids (traverses), which helps cut down job times. It does all this with minimal shaking of the machine and toolhead, making for smoother cuts with less change of skipping, chattering, or other artifacts.

<Insert video here>

###Tgfx
TinyG works with tgfx, a cross-platform control program available for Mac, Windows and Linux.

###Setup

Setting up Shapeoko to use TinyG is pretty straightforward. This page provides all the settings needed
https://github.com/synthetos/TinyG/wiki/TinyG-Shapeoko-Setup

A few things to keep in mind.

* TinyG treats motors independently from the axes. So it natively supports dual-y configurations. 2 motors map to the Y axis - and both are driven by Y axis controls. But they must be going in opposite directions (i.e. have reverse polarity settings) for the gantry to move as a unit. Polarity can be handled electrically by reversing one of the coil pairs on one of the motors, or under software control by setting the polarity motor parameter. I prefer firmware as this way all the motors are wired the same and are interchangeable.

###Tuning
Once you are set up you can tune the Shapeoko/TinyG system for optimal performance. There is a page on the TinyG wiki about [tuning](https://github.com/synthetos/TinyG/wiki/TinyG-Tuning) that the following was adapted from. What follows are tuning instructions and guidance specifically for the Shapeoko/TinyG combination.

Mechanical
A well functioning mechanical system is the heart of tuning. The electrical system can at best compensate for the mechanical system, but can never fundamentally improve it. Here are a number of points to make sure the Shapeoko itself is tuned up.

_Bart - perhaps you can tweak this part. I'm sure you know 10x what I do in this area_

* Make sure the machine is in perfect alignment and bel tension is correct. All parts should be square and the belt axes (X and Y) should move with almost no resistance. It's a good idea to test this with no motors on the system to look for any rough spots in the slide, or any points where resistance is greater than others. If the motors are mounted  at least make sure they are electrically disconnected and their winding leads are not shorted. 

* The Z axis should turn as smoothly as possible with no binding. Many people upgrade to an Acme screw for this reason. 

Once the mechanical system is working well you can start in on the settings. Do one axis at a time then in combination. Here's a page that describes a tuning procedure.
https://github.com/synthetos/TinyG/wiki/TinyG-Tuning

* The velocity maximum settings determine how fast traverses (G0's) will move. We usually set these to 16000 mm/min (267 mm/sec for 3dp types), but these can often be set higher


This page covers things you may want to do once the wiring and physical setup is complete.

##Tuning TinyG for Shapeoko

Tuning involves setting these to good values:
* Velocity Maximum 
* Feed Rate Maximum
* Jerk
* Motor Currents (potentiometer settings)

Then these:
* JA
* 

### Background
The **velocity maximum** - aka **traverse rate** - is the top speed of a machine axis under no cutting load. Traverses (G0's) move the machine at the maximum velocity and generally don't change from job to job. A good maximum velocity will drive the motor reliably at high speed and allow for a little headroom where the motor is still running well. Attempting to set this rate above this speed may cause the motor to operate erratically, drop steps, or stall.<br><br>
Bear in mind that with traverses (G0) the actual speed of movement may well be above any of the traverse rates of the individual axes as it's the cartesian sum. For example, if xvm and yvm are set to 10,000 mm/min a G0 from (0,0) to (100,100) will actually run at 14,142 mm/min (assuming it has room to accelerate to the target velocity). 

The **feed rate** is the maximum cutting speed the axis can sustain for a given tooling, material and type of cut and may change considerably from job to job. The max feed rates set here are just an upper limit that a Gcode file cannot exceed. The actual control of feed rate should be done from the F words in the Gcode file itself. The max feed rates should be set lower than the maximum velocity and generally set after these have been set.

### Tuning Procedure
The following procedure can be used to set the max velocities ($xvm, $yvm...) and feed rates. Some notes:

* Values shown are in inches. Millimeter values are also provided in square brackets for comparison [mm]. MM values may be approximate but accurate enough for these purposes. 
* Setting the machine is inches mode is done by issuing a G20 command at the command line (or in a file). Issue a G21 for mm mode.
* Settings strings in this example, such as $xvm show the x axis. Other axes are similar, such as $yvm, $zvm, $avm, $bvm, $cvm. 
* The example shows motor 1 mapped to the X axis. Other motors mapped to other axes are similar.

**Steps**<br>
Do these steps for each axis in turn.

1. Ensure the step angle ($1sa), microsteps ($1mi), and polarity ($1po) settings are correct for the motor and wiring. In general, positive X moves to the right, positive Y moves away from you (towards the rear of the machine), and positive Z moves upwards. Typical values are $1sa = 1.8 degrees per step, $1mi = 8 microsteps, $1po = 0 (not inverted).
1. Make sure the travel per revolution ($1tr) is set correctly. Example: $1tr = 0.100 is a value for a 10 thread-per-inch lead screw [or 2.54 in mm]. Some machines have different travel per revolution for different axes. 
1. Start with the current potentiometer at the middle position - 12:00. Never exceed the potentiometers' range of motion, which is 270 degrees, or from about 8:00 to about 4:00. 
1. Set a maximum jerk value ($xjm) where you can audibly hear the motor come up to speed. A value of 20,000,000 [50,000,000] is a good starting point. _Note: commas are accepted by the configuration routines in text mode, but not in JSON mode._
1. Test traverse rate with a G0, such as G0 X5 [G0 X100]. The motor should accelerate, cruise at speed, then decelerate to a stop. The motor should not stall or fail to start. Lower the velocity if this is the case. 
1. If the motor hums but doesn't start it's probably not getting enough current. Turn it up to 1:00 or 2:00. Alternately, if the motor stops and starts; or stutters; and the driver chips are excessively hot the motor is getting too much current. Turn the pot down a bit.
1. If the the motor more or less works but seems to be dropping steps it could be any of the mechanical system (too much friction), the current setting, or the velocity max being too high; or possibly some combination of all three. Experimentation is required. It's best to try to fix them in that order. Start with the mechanical system, then the current, then the setting.
1. Once you have the axis working you can see if raising the jerk ($xjm) and providing more current can increase the top speed. This is a case of experimenting. Try to avoid excessive current, however, as after a point the current setting provides diminishing benefits and only heats up the motors and risks thermal shutdown. 

It's worth noting that the mechanics of the axes may not be identical, and the achieveable traverse rates may differ for each axis. You can set them optimally for each axis and moves in more than one dimension will takes the individual settings into account.&nbsp; 

It's also worth noting that on some machines mechanical resistance is greater in some parts of the travel than others (e.g. more resistance at the ends of travel due to shaft coupler runout or other mechanical factors). Be sure to test the entire travel for each axis before finalizing the settings.

Set feed rates ($xfr) similarly. These often require adjustment for a given job or material as the cutting loads may vary. The traverse rates should not require job-by-job adjustment.


I think the blog post should start with 
A brief overview of the general advantages of the TinyG.
A bit on how that can help a Shapeoko user.
Setup instructions for a Shapeoko user
Tuning tips.