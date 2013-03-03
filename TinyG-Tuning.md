This page covers things you may want to do once the wiring and physical setup is complete.

## Setting Motor Current

## Setting Feed Rate and Maximum Velocity (Traverse Rate)
The **velocity maximum** - aka **traverse rate** - is the top speed of a machine axis under no cutting load.<br>
Traverses are executed as G0 commands and generally don't change from job to job. A good maximum velocity will drive the motor reliably at high speed and allow for a little headroom where the motor is still running well. Attempting to set this rate above this speed may cause the motor to operate erratically, drop steps, or stall.<br>
Bear in mind that with traverses (G0) the actual speed of movement may well be above any of the traverse rates of the individual axes as it's the cartesian sum. For example, if xvm and yvm are set to 10,000 mm/min a G0 from (0,0) to (100,100) will actually run at 14,142 mm/min (assuming it has room to accelerate to the target velocity). 

The **feed rate** is the maximum cutting speed the axis can sustain. This can vary depending on the material and job.<br>

Feed rates should be set lower than traverse rates. Set traverse rates before feed rates. The following example discusses setting the max velocity ($xvm) to the maximum speed of reliable travel, or the "top speed" of the machine.&nbsp;

Notes: 

* Values in this example are in inches. MM values are also provided in square backets for comparison [mm]. MM values may be approximate but accurate enough for these purposes. 
* Setting the machine is inches mode is done by issuing a G20 command either at the command line or in a file. Issues a G21 for mm mode. 
* Settings strings in this example, such as $xvm show the x axis. Other axes are similar, such as $yvm, $zvm,&nbsp;$avm, $bvm, $cvm. 
* The example shows motor 1 mapped to the X axis. Other motors mapped to other axes are similar.

Steps 

1. Make sure motor setting such as step angle ($1sa), microsteps ($1mi), and polarity ($1po) are correct for the motor and the setup. Make sure the travel per revolution ($1tr) is set correctly for your machine. Typical values are $1sa = 1.8 degrees per step, $1mi = 8 microsteps, $1po = 0 (not inverted), and $1tr = 0.100 as the reciprocal of lead screw pitch, e.g. 1/(10 TPI). [or 2.54 in mm]. 
1. Set a maximum jerk value ($xjm) where you can audibly hear the motor come up to speed. A value of 20,000,000 [50,000,000] is good. Note: commas are not accepted by config. 
1. Test traverse rate with a G0, such as G0 X5 [G0 X100]. The motor should accelerate, cruise at speed, then decelerate to a stop. The motor should not stall or fail to start. Lower the velocity if this is the case. 
1. If the motor hums but doesn't start it's probably not getting enough current. Alternately, if the motor stops and starts; or stutters; and the driver chips are excessively hot the motor is getting too much current.&nbsp; 
1. If the the motor more or less works but seems to be dropping steps it could be any of the mechanical system (too much friction), the current setting, or the velocity max being too high. Probably some combination of all three. Experimentation is required. It's best to try to fix them in that order. 
1. It's worth noting that the mechanics of the axes may not be identical, and the achieveable traverse rates may differ for each axis. You can set them optimally for each axis and moves in more than one dimension will takes the individual settings into account.&nbsp; 
1. It's also worth noting that on some machines mechanical resistance is greater in some parts of the travel than others (e.g. more resistance at the ends of travel due to shaft coupler runout or other mechanical factors). Be sure to test the entire travel for each axis before finalizing the seek rate.

Set feed rates ($xfr) similarly. These often require adjustment for a given job or material as the cutting loads may vary. The traverse rates should not require job-by-job adjustment.