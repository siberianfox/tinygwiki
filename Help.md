# TinyG Help Page

For detailed TinyG info see: https://github.com/synthetos/TinyG/wiki<br>
For the latest firmware see: https://github.com/synthetos/TinyG<br>
Please log any issues at http://www.synthetos.com/forums<br>
Have fun

## General
These commands are active from the command line:

- ^x             Reset (control x) - software reset
-  ?             Machine position and gcode model state
-  $             Show and set configuration settings
-  !             Feedhold - stop motion without losing position
-  ~             Cycle Start - restart from feedhold
-  h             Show this help screen
-  $h            Show configuration help screen
-  $test         List self-tests
-  $test=N       Run self-test N
-  $home=1       Run a homing cycle
-  $defa=1       Restore all settings to "factory" defaults

Note: TinyG generates automatic status reports by default. This can be disabled by entering $sv=0

## Configuration Help
These commands are active for configuration:
-  $sys Show system (general) settings
-  $1   Show motor 1 settings (or whatever motor you want 1,2,3,4)
-  $x   Show X axis settings (or whatever axis you want x,y,z,a,b,c)
-  $m   Show all motor settings
-  $q   Show all axis settings
-  $o   Show all offset settings
-  $$   Show all settings
-  $h   Show this help screen

Each $ command above also displays the token for each setting in [ ] brackets<br>
To view settings enter a token:
<pre>
  $_token_
</pre>

For example $yfr to display the Y max feed rate<br>
To update settings enter token equals value:
<pre>
  $`token`=`value`
</pre>
For example $yfr=800 to set the Y max feed rate to 800 mm/minute.<br>
For configuration details see: https://github.com/synthetos/TinyG/wiki/TinyG-Configuration

## Restore Defaults
Enter $defa=1 to reset the system to the factory default values.
This will overwrite any changes you have made.

