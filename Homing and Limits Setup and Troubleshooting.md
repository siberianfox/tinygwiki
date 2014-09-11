You may want to familiarize yourself with the [main homing page](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Description-and-Operation) before reading this page.

Troubleshooting homing and limit operation can involve a number of potential areas. These areas are treated as separate topics:

* [Homing - How it's supposed to work](#homing---how-its-supposed-to-work)
* [Soft and Hard Limits - How it's supposed to work](#soft-and-hard-limits---how-its-supposed-to-work)
* [Homing Setup and Configuration](#homing-setup-and-configuration)
* [Switch Wiring and Grounding / Troubleshooting Noise Problems](#limit-switches)


##Homing - How it's supposed to work
Homing is supposed to follow this sequence. 
* Enter `G28.2 X0 Y0 Z0`. Any axis you want homed must be in the command. The value (0) is irrelevant but must be present
* Homing always following this sequence: `Z-->X-->Y-->A` Z is always first so the tool lifts from the table and clears all objects
* Initial backoff - if either Zmin or Zmax are closed when homing starts the first movement is to back off that switch
* Search Move - the axis should move towards the limit switch at a medium search velocity, hit the switch, and stop immediately.
* Latch Move - the axis should move off the switch at a very slow velocity, stopping once the switch is cleared
* Zero Backoff - the axis moves to a set zero position - usually a few mm away from the latch position
* At this point the homing flag for that axis is set, in this case $homz = 1.
* The above actions complete for all other axes in the homing sequence. At the end of a successful homing cycle the Machine Homed flag is also set: $home = 1. The following commands should return like so:
<pre>
tinyg [mm] ok> $home
Homing state:        Homed  (indicates entire machine is homed)
</pre>
<pre>
tinyg [mm] ok> $hom
Homing state:        Homed
X axis homing state: 1      (indicates a given axis is homed)
Y axis homing state: 1
Z axis homing state: 1
A axis homing state: 0
B axis homing state: 0
C axis homing state: 0
tinyg [mm] ok> 
</pre>
<pre>
tinyg [mm] ok> {hom:n}
{"r":{"hom":{"e":1,"x":1,"y":1,"z":1,"a":0,"b":0,"c":0}},"f":[1,0,8]}
</pre>

##Soft and Hard Limits - How it's supposed to work

###Soft Limits
Soft limits check each new Gcode block to see if it would cause the tool to exceed the work volume defined by the travel limits. If the Gcode exceeds limits program execution is halted (alarmed). The alarm can be cleared by sending a clear command `$clear` or `{clear:n}`, or resetting the system with `^x` (control x), the reset button, or a power cycle. The clear command preserves system state, the resets do not.

Soft limits tests the endpoint of the move for straight traverses and feeds. It also checks the endpoint for arcs, and if any part of the arc would extend beyond the work envelope.

The following steps use a Shapeoko2 as an example:
* Configure the machine dimensions
<pre>
[xtn] x travel minimum            0.000 mm
[xtm] x travel maximum          280.000 mm
[ytn] y travel minimum            0.000 mm
[ytm] y travel maximum          280.000 mm
[ztn] z travel minimum          -95.000 mm
[ztm] z travel maximum            0.000 mm
</pre> 
* Enable soft limits
<pre>
$sl=1
</pre>
<pre>
{sl:1}
</pre>
* Soft limits are only enabled if the machine is homed. Home the machine if not already homed. Test is:
<pre>
tinyg [mm] ok> $home
Homing state:        Homed  (indicates entire machine is homed)
</pre>
<pre>
tinyg [mm] ok> {home:n}
{"r":{"home":1},"f":[1,0,9]}
</pre>
* At this point any command that exceeds limits will send the machine into an Alarm state. An error 220 will be returned, "Soft limit exceeded"
  * Gcode blocks received after the soft limit will be rejected with error 203 "Machine is alarmed" 
  * Gcode blocks received prior to the soft limit (i.e. in the planner queue) will run to completion. The machine will stop in an Alarm state at the end of the last move before the move that exceeded soft limits.
* To clear the alarm state enter:
<pre>
$clear
</pre>
<pre>
{clear:n}
</pre>
Since commands arriving after the soft limit are rejected the host can always send a clear, as the buffer will be read until the clear can be processed. If the host is going to attempt job recovery care must be taken to restart the job from the correct point. We recommend using line numbers. It may be moot. If the Gcode file is not sized properly for the work volume there really may be no way to restart the job anyway. 
###Hard Limits

##Homing Setup and Configuration
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

### Homing Configuration
Homing needs to be set up and configured correctly for it to work. And the switches need to be firing exactly and not picking up spurious noise. This is s step-by-step guide to setting up homing by doing one thing at a time.

**BEFORE YOU DO ANYTHING** Realize that the switch inputs are limited to 3.3 volts. Do not apply higher voltages as this may damage the processor. In normal wiring this is not an issue as the switches are just tied to ground; but for active switches or other configurations this must be dealt with.

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
* Either the min or max switch can be enabled for homing, but not both. This will cause an error.
* The search velocity sets how fast the tool moves towards the limit initially. It should be a good deal less than your maximum velocity (xvm), but not excruciatingly slow. This obviously interacts with the jerk setting, so these can be set in tandem.
* The latch velocity sets how fast the tool backs off the switch after it's been hit by the search. This should be quite slow, as this is the part that determines the accuracy of the zero.
* The latch backoff should be a distance that will always clear the switch once it's been hit. It doesn't matter if this number is larger than what's needed, so be generous. This value is also used to clear off any closed homing and limit switches at the start of the search, so it should be adequate for switches at both ends of travel. 
* The zero backoff is how far you want to move off the switch before actually setting zero. This value MUST clear the switch completely, or you will run into problems.

See if you can home X by running G28.2X0. Remember that if the zero backoff does not back all the way off the switch you will have problems.

**[4]** Now do the same for Y, and then Z. See if you can home them individually, and in combination. Do some Gcode runs to make sure nothing misfires during the run. Try it with and without the spindle running. The spindle motor is often the most extreme source of electrical noise.  

**[5]** Only once all homing works go back and enable limits. Or not. See if things still work. Then record your configuration. If the limits fire when you are running it's probably because of electrical noise from the spindle or some other source, or perhaps faulty wiring. Look here: [Limit switches fire in the middle of a job](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#limit-switches-fire-in-the-middle-of-a-cutting-job)

##Limit Switches
Limit switches (aka end stops) may be configured to kill a job if the travel exceeds the machine dimensions. The same switches are used for limits and homing, but they are 2 different and distinct functions. So a switch can be thought of as a homing switch during a homing cycle, and a limit switch at all other times. 

In CNC-land hitting a limit switch is considered to be an unrecoverable error which kills the job and shuts down the machine. So it's really bad. When a limit is hit the machine enters an alarm state and signals this by shutting down all movement and flashing the Spindle Direction LED rapidly. Do not expect position to be preserved. If you are unsure that the Gcode file will fit in your work envelope it's a good idea to do a dry run before actually tooling any material. 

Another reason limit switches can get hit is if the machine has lost steps somewhere and is not where it thinks it is. In this case the error usually indicates that the max velocity or feedrates are set too high for the job, or the cornering or acceleration is too extreme and should be tuned for the job/material.

#Troubleshooting
The fact that you are here implies you are having problems either getting homing to work, or with limit switches, or both. Please first read these pages:
* [Tinyg Homing](https://github.com/synthetos/TinyG/wiki/TinyG-Homing) page and make sure you understand how homing and limits are supposed work and be configured.
* [Homing and Limits Setup](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#homing-and-limits-setup)

Common Problems:
* [I think the configuration is wrong](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#configuration-problems)
* [Limit switches fire in the middle of a job](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#limit-switches-fire-in-the-middle-of-a-cutting-job)
* [Axis starts and stops and moves in opposite direction](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#axis-starts-and-stops-and-moves-in-the-opposite-direction-before-searching)
* [Limit switches fire on first move after homing](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Setup-and-Troubleshooting#limit-switches-fire-on-first-move-after-homing)

### Configuration Problems
A common source of homing problems are configuration problems. If you are having issues with homing check your configuration before you do anything else:
* Is your $st switch type set to the switch wiring you are using? Check $st in the system group ($sys) is 0 for NO switches, 1 for NC. All switches must be of the same type.
* Are the switches set correctly up for all axes that have switches? I'd recommend disabling limits until you have homing working, then going and back and enabling limits if you want to. To configure an axis for homing set the min or max switch to 1, and the other switch to 0. Here's a typical config by way of example:
 * $xsn=1
 * $xsx=0
 * $ysn=1
 * $ysx=0
 * $xsn=0
 * $zsx=1
* Make sure that only one switch per axis is enabled for homing. You can't have 2 homing switches on an axis. 
* Is your motor polarity set correctly - i.e. does the motor move in the correct direction? X typically moves right for positive motion, Y moves away from you (away from the front of the table), and Z moves up for positive motion. Homing will get confused if your motion direction does not agree with the notion of "min" and "max" for the $_sn and $_sx settings.
* Is the $_tm maximum travel set for the size of the table? If it's too short the search move may never reach the switch. 
* Is the $_lb latch backoff long enough to actually clear the switch? If not you will not have an accurate zero.
* Have you allowed a sufficient $_zb zero backoff? If ZB is too small you run the risk of misfiring a limit switch when you return to zero.  

### Limit switches fire in the middle of a cutting job
This is a common problem in many CNC setups (not just TinyG), and can usually be dealt with.

**Electrical Noise** What's usually happening is that electrical noise from the spindle or some other source is being picked up by the switch lines and is firing the limit switches spuriously. Here are some tips on diagnosing and fixing this problem. 

First, try to reduce the noise at its source
* Try running the job with the spindle off and see if you get the same behavior. If not, it's likely that electrical noise being generated by the spindle is the culprit. You can also put an oscilloscope on the switch lines and look at the noise and see if it comes and goes as the spindle is turned on and off. Here are some steps to reduce spindle noise:
 * If the spindle is AC connect it to a different AC circuit than the machine power supply.
 * If the spindle is DC run it from a different power supply than the motor power supply.
 * Brushless spindles are quieter both electrically and mechanically than spindles that have commutator brushes in them. 
 * Damp the noise by putting a ceramic capacitor of sufficient voltage (typ. 300v or more) across the spindle power lines. Experiment with different values, such as 1 uF or 0.1 uF. If the spindle is AC the capacitor cannot be polarized (most ceramics are not polarized).
 * Damp the noise by running the spindle wiring trough a ferrite bead or toroid.
 * House the spindle electronics in a separate, shielded enclosure away from the TinyG board. 
 * Often grounding is a problem. Here's a handy reference: http://www.cnczone.com/forums/phase_converters_vfd/133188-limit_switches_tripping_when_using_vfd_spindle.html

Next, try to reduce the noise in the lines
* Use normally closed (NC) switches instead of NO switches.  
* Use twisted pair shielded wired for switch lines to reduce noise pickup. Ground the shield at the board side of the line and leave the other side floating.
* Do not run switch lines alongside motor or spindle lines. Especially along spindle lines. If the switch line needs to cross a motor or spindle line do this at right angles. This is inconvenient if you want to share cable guide or wiring harness, but is sometimes necessary. 
* Look for ground loops and eliminate these. Use only a single point of ground for all switches originating at the board's GND connector.
* Put an pullup resistor on on each switch input line (need more detail here). TinyG V7's and V8's have a 2.7K pullup resistor to 3.3 volts on each switch line. This value can be lowered somewhat for more noise immunity, but should not really go below about 1K Ohm (parallel, including the 2.7K) or you risk pulling too much current from the 3.3v supply. 
* Put a capacitor to ground on each switch line. TinyG V8's have a 0.22 uF capacitor to ground on each switch line.

If all else fails
* Disable the limits using the switch parameters. Use the switches for homing only.

Some more references:
* http://www.cnccookbook.com/CCCNCNoise.html

**Faulty Wiring or Switch** Alternately, the wiring or switches may be intermittent and firing randomly. An oscilloscope or VOM can be used to diagnose this case. If this happens try disconnecting one switch axis at a time and see if the problem goes away of gets better.

### Axis starts and stops and moves in the opposite direction before searching
You probably have NC switches that are incorrectly configured as NO switches ($st=0 instead of $st=1). The fact that this works at all with the incorrect switch type is because the switches only fire on a change in state. Otherwise the machine would immediately halt.

###Limit switches fire on first move after homing
Check if the zero backoff is adequate to clear the switch and prevent it from spurious firings once motion starts.