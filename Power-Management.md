Power management is used to keep the steppers on when you need them and turn them off when you don't. Set to one of the following:

<pre>
0 = Motor disabled
1 = Motor always powered
2 = Motor powered during a machining cycle (i.e. when any axis is moving)
3 = Motor only powered when it is moving
</pre>

Usage examples:
<pre>
$1pm=1     Enable motor 1 all the time
{1pm:1}    JSON equivalent of the above
</pre>

These additional commands are also available:
<pre>
$me         Enable all motors
$md         Disable all motors
{me:n}
{md:n}
</pre>

###Motor Disabled (0)
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