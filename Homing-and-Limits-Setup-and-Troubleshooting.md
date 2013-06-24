##Homing and Limits Setup
### Background
Most machines (but not all) have the XY origin in the lower left hand corner - i.e. the minimum X (to the left) and minimum Y (towards the operator - you). Z is commonly zeroed at its highest point - making all plunges into the work negative values. This translates to the following settings:
<pre>
$xsn=1
$xsx=0
$ysn=1
$ysx=0
$zsn=0
$zsx=1
$asn=0
$asx=0
</pre>

Switches can be normally open (NO) or normally closed (NC), but all switches must be of the same type. We generally recommend using NC configurations as they are more noise immune, and a fault in a switch or the wiring is evident.

### Setup
Homing needs to be set up exactly for it to work. And the switches need to be firing exactly and not picking up spurious noise. This is s step-by-step guide to setting up homing by doing one thing at a time.

**[1]** Disable the min and max switches on all axes:
<pre>
$xsn=0
$xsx=0
$ysn=0
$ysx=0
$zsn=0
$zsx=0
$asn=0
$asx=0
</pre>

**[2]** Set the switch type $st to your configuration. Set to normally-open (NO) or normally-closed (NC) for your switch configuration.
<pre>
$st=0    (NO: if you have normally open switches)
--or--
$st=1    (NC: if you have normally closed switches)
</pre>

NOTE: All switches must be of the same type. If you are unsure what you have set it to NO. Homing will still work even if you have NC switches - it will just [do a little dance](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#axis-starts-and-stops-a-few-times-and-moves-in-the-opposite-direction-before-performing-the-search) first.

**[3]** Enable the X axis only and for homing only (not homing+limits); typically this is $xsn=1 if X is at the minimum end. Set some reasonable values for the homing parameters. Here are some examples for a fast, belt driven machine:
<pre>
[xjh] x jerk homing     10000000000 mm/min^3
[xsn] x switch min                1 [0=off,1=homing,2=limit,3=limit+homing]
[xsx] x switch max                0 [0=off,1=homing,2=limit,3=limit+homing]
[xsv] x search velocity        3000.000 mm/min
[xlv] x latch velocity          100.000 mm/min
[xlb] x latch backoff            20.000 mm
[xzb] x zero backoff              3.000 mm
</pre>

* The homing jerk can be greater than the normal max jerk as you want the machine to stop very quickly once it hits the switch. You may need to experiment with this value.
* The search velocity sets how fast the tool moves towards the limit initially. It should be a good deal less than your maximum velocity (xvm), but not excruciatingly slow. This obviously interacts with the jerk setting, so these can be set in tandem.
* The latch velocity sets how fast the tool backs off the switch after it's been hit by the search. This should be quite slow, as this is the part that determines the accuracy of the zero.
* The latch backoff should be a distance that will always clear the switch once it's been hit. It doesn't matter if this number is larger than what's needed, so be generous. This value is also used to clear off any closed homing and limit switches at the start of the search, so it should be adequate for switches at both ends of travel. 
* The zero backoff is how far you want to move off the switch before actually setting zero. This value MUST clear the switch completely, or you will run into problems.

See if you can home X by running G28.2X0. Remember that if the zero backoff does not back all the way off the switch you will have problems.

**[4]** Now do the same for Y, and then Z. See if you can home them individually, and in combination. Do some Gcode runs to make sure nothing misfires during the run. Try it with and without the spindle running. The spindle motor is often the most extreme source of electrical noise.  

**[5]** Only once all homing works go back and enable limits. Or not. See if things still work. Then record your configuration. If the limits fire when you are running it's probably because of electrical noise from the spindle or some other source, or perhaps faulty wiring. Look here: [Limit switches fire in the middle of a job](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#limit-switches-fire-in-the-middle-of-a-cutting-job)

#Troubleshooting
The fact that you are here implies you are having problems either getting homing to work, or with limit switches, or both. Please first read these pages:
* [Tinyg Homing](https://github.com/synthetos/TinyG/wiki/TinyG-Homing) page and make sure you understand how homing and limits are supposed work and be configured.
* [Homing and Limits Setup](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#homing-and-limits-setup)


Common Problems:
* [I think the configuration is wrong](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#configuration-problems)
* [Axis starts and stops and moves in opposite direction](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#axis-starts-and-stops-and-moves-in-the-opposite-direction-before-searching)
* [Limit switches fire in the middle of a job](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#limit-switches-fire-in-the-middle-of-a-cutting-job)
* [Limit switches fire on first move after homing]()

### Configuration Problems
Most homing problems are configuration problems. Especially if you are running normally closed switches. If you are having issues with homing check your configuration before you do anything else
* Is your $st switch type variable set to the switch wiring you are using? Check is $st in the system group ($sys) is 0 for NO switches, 1 for NC. All switches must be of the same type.
* Are the switches set correctly up for all axes that have switches? I'd recommend disabling limits until you have homing working, then going and back and enabling limits if you want to. To configure an axis for homing set the min or max switch to 1, and the other switch to 0. Here's a typical config by way of example:
 * $xsn=1
 * $xsx=0
 * $ysn=1
 * $ysx=0
 * $xsn=0
 * $zsx=1
* Is your motor polarity set correctly - i.e. does the motor move in the correct direction? X typically moves right for positive motion, Y moves away from you (away from the front of the table), and Z moves up for positive motion. Homing will get confused if your motion direction does not agree with the notion of "min" and "max" for the $_sn and $_sx settings.
* Is the $_tm maximum travel set for the size of the table? If it's too short the search move may never reach the switch. 
* Is the _lb latch backoff long enough to actually clear the switch? If not you will not have an accurate zero.
* Have you allowed a sufficient $_zb zero backoff? If ZB is too small you run the risk of misfiring a limit switch when you return to zero.  

## Axis starts and stops and moves in the opposite direction before searching
You probably have NC switches that are incorrectly configured as NO switches ($st=0 instead of $st=1). The fact that this works at all with the incorrect switch type is because the switches only fire on a change in state. Otherwise the machine would immediately halt.

# Limit Switch Problems
## Limit switches fire in the middle of a cutting job
This is a common problem in many CNC setups (not just TinyG). Here are a few handt references:
* http://www.cnczone.com/forums/phase_converters_vfd/133188-limit_switches_tripping_when_using_vfd_spindle.html

What's usually happening is that electrical noise from the spindle or some other source is being picked up by the switch lines and is firing the limit switches spuriously. Here are some tips on diagnosing and fixing this problem.

First, try to stop the noise at its source
* Try running the job with the spindle off and see if you get the same behavior. If not, it's likely that electrical noise being generated by the spindle is the culprit. You can also put an oscilloscope on the switch lines and look at the noise and see if it comes and goes as the spindle is turned on and off. 
 * Brushless spindles are quieter both electrically and mechanically than spindles that have commutator brushes in them. 
 * Some noise can be damped by putting a ceramic capacitor of sufficient voltage (typically 300v or more) across the spindle power lines. Experiment with different values, such as 1 uF or 0.1 uF. If the spindle is AC the capacitor cannot be polarized (most ceramics are not polarized).
 * You can also damp some noise by running the spindle wiring trough a ferrite bead or toroid.