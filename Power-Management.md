_Note: These behaviors are complete in firmware build 438.09 and later_

##Intro
Power management is used to keep the steppers on when you need them and turn them off when you don't. 

Stepper motors consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Most lead screw or geared machines will hold position if you shut off the power when the motor is not moving, but belt machines generally do not. 

In either case it's usually a good idea to keep all motors powered during a machining cycle to maintain absolute machine position. Depending on your machine you either may want to remove power some number of seconds after the machining cycle, or leave the motors powered on for as long as the machine is on.

You also generally want to use power management to de-power the machine if it's left unattended for an extended time. You don't want to leave the steppers on for extended idle times such as walking away from your machine and leaving it on overnight with the motors idling. 

The power management commands let you set up the right set of actions for your machine and use.

##Per-Motor Commands

	Setting | Description | Notes
	--------|-------------|-----------------------------
	$1pm | Display power mode | Returns one of the power modes below
	$1pm=0 | Always disabled | Motor will not be enabled by $me 
	$1pm=1 | Always enabled | Motor will not be disabled by $md 
	$1pm=2 | Enabled in cycle | Motor is powered during machining cycle (any axis is moving) and for $mt seconds afterwards
	$1pm=3 | Enabled while moving | Motor is powered when it is moving and for $mt seconds afterwards. Motors in this state can disable themselves during cycles if timeout is less than cycle time.

<pre>
Text mode examples:
$1pm=0     Motor 1 disabled
$2pm=1     Motor 2 always powered
$1pm=2     Motor 1 powered during a machining cycle (any motor moving)
$4pm=3     Motor 4 only powered when it is moving

JSON mode equivalents:
{1pm:0}  or {1:{pm:0}}
{2pm:1}  or {2:{pm:1}}
{1pm:2}  or {1:{pm:2}}
{4pm:3}  or {4:{pm:3}}
</pre>

These commands affect all motors and take effect as soon as they are issued.

###Motor Disabled (0)
This will turn off motor power and prevent the motor from turning on. Disabling the motor will prevent that axis from participating in any move. The motor will not be affected by $me or $md commands. This setting takes effect immediately.

###Motor Always Powered (1)
This will turn on motor power and leave it on until the board is shut down. The motor will not be affected by $me or $md commands. You generally do not want to use this mode as it will leave the motors on for extended periods of time if you do not power down the machine. Better to set a long motor power timeout and use Powered In Cycle (2)

###Motor Powered In Cycle (2)
This will turn on the motor power at the start of any move on any axis (a "cycle"), and will de-energize the motors $mt seconds after the cycle is complete (all motion stops). The timeout interval is set by the $mt value (see Global Power Management Commands).

###Motor Powered When Moving (3)
This will turn on the motor power only when that axis is moving, and will remove power $mt seconds after that axis stops moving.

## Global Power Management Commands
These commands affect all motors.
 
	Setting | Description | Notes
	--------|-------------|-----------------------------
	$md(=N) | Disable motors | $md will disable all motors. Set N = 1-4 to disable motor N only
	$me(=N) | Enable motors | $me will enable all motors. Set N = 1-4 to enable motor N only
	$mt | Set motor enable timeout | In seconds, up to 1 million seconds
	$pwr | Report enable states | PWR returns all motor enables states. This is like a virtual motor LED that returns 1 if motor N is enabled (LED is lit), 0 if not
	$pwr1 | Report enable state | PWRn returns a single motor state

<pre>
Text mode examples:
$me=180    Lock all motors for 3 minutes for a tooling operation 
$md        Disable all motors
$mt=300    Set timeout to 5 minutes
$pwr       Query power enabled for all motors
$pwr2      Query power enabled for motor 2

JSON mode equivalents:
{me:180}
{me:n}
{mt:300}
{pwr:n}
{pwr2:n}
</pre>

###Motor Enable (me)
This will turn on any motor that is not disabled ($1pm=0). If provided a valid motor number it will enable that motor only. 

###Motor Disable (md)
This will turn off any motor that is not permanently enabled ($1pm=1). If provided a valid motor number it will disable that motor only.

###Motor Timeout (mt)
Sets the number of seconds before a motor will shut off automatically. When the timeout starts is se by context:

$1pm=0 Disabled motors are always off. They do not time out
$1pm=1 Always-on motors are always on. They do not time out, either
$1pm=2 Motors that are powered-in-cycle begin timeout at the end of the cycle, which is when the last motor stops moving.
$1pm=3 Motors that are powered-while-moving begin timeout at the end of their movement

Motor timeouts are suspended during feedholds. This allows changing or adjusting tools without loss of position.

