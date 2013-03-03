## Rotational Axis Settings and Modes
* The rotational axes (ABC) run natively in degrees mode. All gcode values for the ABC axes are in degrees. The configuration settings of the rotational axes are also all in degrees (with the exception of the Radius setting). 
* Velocity max and feed rate are in degrees per minute and behave as per RS274NGCv3 feed rate definitions. 
* Travel per revolution means the number of degrees the machine moves per motor revolution - it expresses gearing. For example a rotary table has a 90:1 gear ratio. The travel per revolution should be set to 4. (360 / 90). 
* Travel hard and soft limits are in degrees. Most of the time rotational axes are "wrapped" axes that have no limits. In this case the limits should be set to -1. 
* All homing cycle values (rates and distances) are also in degrees, although the meanings may vary depending on homing modes.
<br>

### Slaved Extruder Example
Take the case of a stepper controlled extruder for 3d printing. The stepper motor is driven from the C axis. 

* The C axis max feed rate is initially set to a degrees per minute that is somewhat lower than the stepper can deliver. 1000 steps per second for a 1.8 degree stepper equals 108,000 degrees per second. If this rate is faster than the extruder and/or stepper can handle it should be lowered - as it is used to limit the maximum rate an extrusion move can be executed. 
* The travel_per_revolution setting accounts for the gearing of the stepper to the extrusion mechanism. Let's say the gearing is 50:1. Travel per revolution is set to 360/50 = 7.2 
* Once these 2 settings are established they need not be changed. 
* The C axis radius is used to set the extrusion rate. This is experimental will probably change depending on the part being built, and can change dynamically during a build by issuing a $araNNN command (or MXXX command once we get that linked). 
* Now when there is movement in the XY plane the extruder will feed at a rate proportional to the movement. The extrusion rate will also obey the same acceleration/deceleration as the extruder head. Movement in Z will not affect the extrusion rate. 
* So why is there a separate EXTRUDER mode? Extruder mode works as above, but additionally it only extrudes for G1, G2 and G3. It does not extrude during seeks (G0).
