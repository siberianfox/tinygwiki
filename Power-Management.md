Power management is used to keep the steppers on when you need them and turn them off when you don't. You generally want some form of power management so you don't leave the steppers on for extended idle times such as walking away from your machine and leaving it on overnight with the motors idling. 

##Global Power Management Commands
These commands affect all motors.
<pre>
$me=N       Enable all motors for N seconds
$me         Enable all motors for the default idle time
$md         Disable all motors (immediately)
{me:60}     JSON command to enable for 60 seconds
{md:n}      JSON command to disable all motors 
</pre>

For example, to lock all motors for 3 minutes for a tooling operation send `{me:180}`

##Per-Motor Power Management Commands
Power management can be set per motor using the $1pm command (for each motor number). Settings:
<pre>
$1pm=0     Motor 1 disabled
$1pm=1     Motor 1 always powered
$1pm=2     Motor 1 powered during a machining cycle (any axis is moving)
$4pm=3     Motor 4 only powered when it is moving

JSON equivalents:
{1pm:0}  or {1:{pm:0}}
{1pm:1}  or {1:{pm:1}}
{1pm:2}  or {1:{pm:2}}
{4pm:3}  or {4:{pm:3}}
</pre>

###Motor Disabled - e.g. {1pm:0}
Setting This will turn off motor power and prevent the motor from turning on. Disabling This will prevent 

Examples:

$4pm=2         Set motor 4 to be powered when any axis is moving
{4pm:2}        Same as above
{4:{pm:2}}     Same as above
Stepper motors consume maximum power when idle. They hold torque and get hot. If you shut off power the motor has (almost) no holding torque. Some machine configurations are OK if you shut off the power on idle (like most leadscrew machines), others are not (some belt/pulley configs and some non-cartesian robots).

Other notes:

A motor set to $1pm=2 (or 3) will become powered and will remain powered for N seconds specified in the $mt variable (e.g. 60 seconds, which would be {"mt":60} ).
Power mode changes take effect immediately (used to change on the next move)
All non-disabled motors are powered on startup and from reset. They may time out according to $mt
All non-disabled motors can be enabled by issuing a $me command ( also {"me":""} )
All motors are disabled by issuing a $md command ( also {"md":""} )