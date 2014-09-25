Power management is used to keep the steppers on when you need them and turn them off when you don't. 

Stepper motors consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Some machine configurations will hold position if you shut off the power when the motor is not moving (like many leadscrew or geared machines), others will not (some belt/pulley configs and some non-cartesian robots). You generally want to set power management so that the motor is powered during a machining cycle to maintain absolute machine position, although there are other options.

You also generally want to use power management to de-power the machine if it's left unattended for an extended time. You don't want to leave the steppers on for extended idle times such as walking away from your machine and leaving it on overnight with the motors idling.

##Global Power Management Commands
These commands affect all motors and take effect as soon as they are issued.
<pre>
Text Mode:
$me=N       Enable all motors for N seconds
$me         Enable all motors for the default idle time
$md         Disable all motors

JSON mode:
{me:60}     Enable all motors for 60 seconds
{me:n}      Enable all motors for the default idle time
{md:n}      Disable all motors
</pre>

For example, to lock all motors for 3 minutes for a tooling operation send `{me:180}`

###Motor Timeout
Motor power timeout is set globally using $mt. Examples:
<pre>
$mt=300     Set timeout to 5 minutes
{mt:300}    Set timeout to 5 minutes
</pre>

##Per-Motor Power Management Commands
Power management can be set per motor using the $1pm command ($N for each motor number). Settings:
<pre>
Text mode:
$1pm=0     Motor 1 disabled
$1pm=1     Motor 1 always powered
$1pm=2     Motor 1 powered during a machining cycle (any axis is moving)
$4pm=3     Motor 4 only powered when it is moving

JSON mode:
{1pm:0}  or {1:{pm:0}}
{1pm:1}  or {1:{pm:1}}
{1pm:2}  or {1:{pm:2}}
{4pm:3}  or {4:{pm:3}}
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