= Notes =
== Setting Feed Rate and Maximum Velocity (Traverse Rate)  ==

The velocity maximum - aka traverse rate - is the upper limit of the machine axis under no cutting load. Traverses are executed as G0 commands and generally don't change from job to job. The feed rate is the maximum cutting speed the axis can sustain. This can vary depending on the material and job. Feed rates should be less than traverse rates. Set traverse rates before feed rates. The following example discusses setting the max velocity ($xvm) to the maximum speed of reliable travel, or the "top speed" of the machine.&nbsp;A good maximum velocity will drive the motor reliably at high speed and allow for a little headroom where the motor is still running well. Attempting to set this rate above this speed may cause the motor to operate erratically, drop steps, or stall. 

Notes: 

*Values in this example are in inches. MM values are also provided in square backets for comparison [mm]. MM values may be approximate but accurate enough for these purposes. 
*Setting the machine is inches mode is done by issuing a G20 command either at the command line or in a file. Issues a G21 for mm mode. 
*Settings strings in this example, such as $xvm show the x axis. Other axes are similar, such as $yvm, $zvm,&nbsp;$avm, $bvm, $cvm. 
*The example shows motor 1 mapped to the X axis. Other motors mapped to other axes are similar.

Steps 

#Make sure motor setting such as step angle ($1sa), microsteps ($1mi), and polarity ($1po) are correct for the motor and the setup. Make sure the travel per revolution ($1tr) is set correctly for your machine. Typical values are $1sa = 1.8 degrees per step, $1mi = 8 microsteps, $1po = 0 (not inverted), and $1tr = 0.100 as the reciprocal of lead screw pitch, e.g. 1/(10 TPI). [or 2.54 in mm]. 
#Set a maximum jerk value ($xjm) where you can audibly hear the motor come up to speed. A value of 20,000,000 [50,000,000] is good. Note: commas are not accepted by config. 
#Test traverse rate with a G0, such as G0 X5 [G0 X100]. The motor should accelerate, cruise at speed, then decelerate to a stop. The motor should not stall or fail to start. Lower the velocity if this is the case. 
#If the motor hums but doesn't start it's probably not getting enough current. Alternately, if the motor stops and starts; or stutters; and the driver chips are excessively hot the motor is getting too much current.&nbsp; 
#If the the motor more or less works but seems to be dropping steps it could be any of the mechanical system (too much friction), the current setting, or the velocity max being too high. Probably some combination of all three. Experimentation is required. It's best to try to fix them in that order. 
#It's worth noting that the mechanics of the axes may not be identical, and the achieveable traverse rates may differ for each axis. You can set them optimally for each axis and moves in more than one dimension will takes the individual settings into account.&nbsp; 
#It's also worth noting that on some machines mechanical resistance is greater in some parts of the travel than others (e.g. more resistance at the ends of travel due to shaft coupler runout or other mechanical factors). Be sure to test the entire travel for each axis before finalizing the seek rate.

Set feed rates ($xfr) similarly. These often require adjustment for a given job or material as the cutting loads may vary. The traverse rates should not require job-by-job adjustment.

== Rotational Axis Settings and Modes  ==

'''Rotational Axis Settings''' 

*The rotational axes (ABC) run natively in degrees mode. All gcode values for the ABC axes are in degrees. The configuration settings of the rotational axes are also all in degrees (with the exception of the Radius setting). 
*Velocity max and feed rate are in degrees per minute and behave as per RS274NGCv3 feed rate definitions. 
*Travel per revolution means the number of degrees the machine moves per motor revolution - it expresses gearing. For example a rotary table has a 90:1 gear ratio. The travel per revolution should be set to 4. (360 / 90). 
*Travel hard and soft limits are in degrees. Most of the time rotational axes are "wrapped" axes that have no limits. In this case the limits should be set to -1. 
*All homing cycle values (rates and distances) are also in degrees, although the meanings may vary depending on homing modes.

<br> '''Slaved Extruder Example''' 

Take the case of a stepper controlled extruder for 3d printing. The stepper motor is driven from the C axis. 

*The C axis max feed rate is initially set to a degrees per minute that is somewhat lower than the stepper can deliver. 1000 steps per second for a 1.8 degree stepper equals 108,000 degrees per second. If this rate is faster than the extruder and/or stepper can handle it should be lowered - as it is used to limit the maximum rate an extrusion move can be executed. 
*The travel_per_revolution setting accounts for the gearing of the stepper to the extrusion mechanism. Let's say the gearing is 50:1. Travel per revolution is set to 360/50 = 7.2 
*Once these 2 settings are established they need not be changed. 
*The C axis radius is used to set the extrusion rate. This is experimental will probably change depending on the part being built, and can change dynamically during a build by issuing a $araNNN command (or MXXX command once we get that linked). 
*Now when there is movement in the XY plane the extruder will feed at a rate proportional to the movement. The extrusion rate will also obey the same acceleration/deceleration as the extruder head. Movement in Z will not affect the extrusion rate. 
*So why is there a separate EXTRUDER mode? Extruder mode works as above, but additionally it only extrudes for G1, G2 and G3. It does not extrude during seeks (G0).
