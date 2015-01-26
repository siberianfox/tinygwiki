###Intro
Power management is used to keep the steppers on when you need them and turn them off when you don't. 

Stepper motors consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Some machine configurations will hold position if you shut off the power when the motor is not moving (like many leadscrew or geared machines), others will not (some belt/pulley configs and some non-cartesian robots). 

You generally want to set power management so that the motor is powered during a machining cycle to maintain absolute machine position. Depending on your machine you either may want to remove power some number of seconds after the machining cycle, or leave the motors powered on for as long as the machine is on.

You also generally want to use power management to de-power the machine if it's left unattended for an extended time. You don't want to leave the steppers on for extended idle times such as walking away from your machine and leaving it on overnight with the motors idling. This can be done by setting motor timeouts.

##Power Management Commands

### $1pm - Motor Power Mode
Power management can be set per motor using the $1pm command ($N for each motor number). Settings:

	Text | Description | Notes
	--------|-------------|-----------------------------
	$1pm=0 | Disable motor | Motor is disabled via the motor ENABLE line 
	$1pm=1 | Always enabled | Motor is always enabled 
	$1pm=2 | Enabled in cycle | Motor is enabled during machining cycle (any axis is moving) and for $mt seconds afterwards
	$1pm=3 | Enabled while moving | Motor is enabled when it is moving and for $mt seconds afterwards. Motors in this state can disable themselves during cycles if timeout is less than cycle time.

<pre>
Text mode examples:
$1pm=0     Motor 1 disabled
$1pm=1     Motor 1 always powered
$1pm=2     Motor 1 powered during a machining cycle 
$4pm=3     Motor 4 only powered when it is moving

JSON mode examples:
{1pm:0}  or {1:{pm:0}}
{1pm:1}  or {1:{pm:1}}
{1pm:2}  or {1:{pm:2}}
{4pm:3}  or {4:{pm:3}}
</pre>

These commands affect all motors and take effect as soon as they are issued.

###Global Power Management Commands

	Setting | Description | Notes
	--------|-------------|-----------------------------
	$me=N | Enable all motors for N seconds | Motors will be disabled will occur after N seconds
	$me | Enable all motors | Motors will be disabled will occur after $mt seconds
	$md | Disable all motors | 
	$mt | Set motor power timeout | In seconds, up to 4 million seconds

Examples
<pre>
$me=180     Lock all motors for 3 minutes for a tooling operation 
{me:180}    JSON equivalent of above
$mt=300     Set timeout to 5 minutes
{mt:300}    JSON equivalent of above
</pre>

Note: All non-disabled motors are powered on startup and from reset, and will time out according to $mt

###Motor Disabled (0)
_Example {1pm:0}_<br>
This will turn off motor power and prevent the motor from turning on. Disabling the motor will prevent that axis from participating in any move. This setting takes effect immediately.

###Motor Always Powered (1)
_Example {1pm:1}_<br>
This will turn on motor power and leave it on until the board is shut down. You generally do not want to use this mode as it will leave the motors on for extended periods of time if you do not power down the machine. Better to set a long motor power timeout and use Enabled In Cycle (2)

###Motor Powered In Cycle (2)
_Example {3pm:2}_<br>
This will turn on the motor power at the start of any move on any axis (a "cycle"), and will de-energize the motors MT seconds after the cycle is complete (all motion stops). The timeout interval is set by the MT value.

###Motor Powered When Moving (3)
_Example {2pm:3}_<br>
This will turn on the motor power only when that axis is moving, and will remove power MT seconds after that axis stops moving.
