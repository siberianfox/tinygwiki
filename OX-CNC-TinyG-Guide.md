## Overview
The OX is an open source CNC machine that is sold by [smw3d](http://www.smw3d.com/ox-diy-cnc-kit/). This page is a work in progress please feel free to update it as needed.

### Settings Overview

Typically the configuration is as follows:
* Motor 1 - X axis
* Motor 2 - Y axis
* Motor 3 - Y axis
* Motor 4 - Z axis

The OX's spindle can also be controlled via TinyG/gcode or manually with a potentiometer (manual nob).  If you plan on using TinyG's PWM feature to control your OX please see the [Spindle Settings](#spindle-settings).

This is a dual Y setup.
## Motor Configurations
**Important settings to get right:** (Note these settings are all in MM)
* $1tr=60 - Gt3 belt 40 tooth pulley
* $2tr=60 - Gt3 belt 40 tooth pulley
* $3tr=60 - Gt3 belt 40 tooth pulley
* $4tr=8  - The acme lead screw

## Spindle Settings
**Please make sure you apply these settings BEFORE you hookup the spindle PWM to your TinyG and apply power.  If not your spindle could start spinning on power up.**
* $p1frq=5000
* $p1csl=0
* $p1csh=10000
* $p1cpl=0
* $p1cph=1
* $p1pof=0
