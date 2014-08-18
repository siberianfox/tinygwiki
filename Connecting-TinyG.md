[<<< Prev - Getting Started](TinyG-Start) --- [Next - Test Drive>>>](Test-Drive-TinyG)<br><br>
If you are on this page you have already selected your motors and power supply and are ready to hook them up.

## Connect Power
The **MOST IMPORTANT** thing to do is to wire your power input correctly. So check and double check this before actually turning on the power.

![TinyG diagram version 8](http://farm3.staticflickr.com/2842/10495433164_c3e47f475a_o.png)

**THIS PAGE ASSUMES THAT THE POWER SUPPLY'S BLACK IS GROUND AND RED OR YELLOW IS +24VOLT (HOT)**. Not all power supplies adhere to this convention so you still need to check this carefully. Read below:

1. Check you have the correct power supply. You should have a DC power supply between 12 and 30 volts --- 24 volts is ideal. It should be capable of providing 4 to 15 amps. 
1. BEFORE connecting it to TinyG, turn on the power supply and make sure you have correct voltage. Make sure it is DC, not AC. Hopefully you have a volt meter or can get your hands on one. If you don't you might consider a trip to the Radar Shed for something like [this](http://www.radioshack.com/product/index.jsp?productId=4214667).
1. With the power supply off, wire the negative to the GND terminal of the power input block, and the positive to the +Vmot side. Negative is BLACK by convention and positive is RED or YELLOW, or some other bright color. Check you have the correct voltage and polarity before connecting the power supply to TinyG. We have encountered power supplies that do not obey these conventions, and incorrect voltage or polarity can destroy your board!
1. Turn on the power supply. If the blue light turns on this is a good sign. If not, blow away the smoke and send us an email.

## Establish USB connection
Next establish USB connection with your host computer. 

####Install FTDI Drivers
* If you do not have the FTDI VCP USB drivers for your system you will need to install them. It's possible they are already on your system as many applications use them, including the older Arduinos. The easiest way to check if you have them is to fire up CoolTerm (see step 2) and see if something like `tty.usbserial-AE01DVWD` or `usbserial-CRAZYMON` shows up when you scan the serial ports. On Windows you will see something like `COM12`, and no indication of what that connects to, so you might have to hunt. 

* If not, unplug TinyG and install the drivers. You can get them from the [FDTI VCP Driver Page](http://www.ftdichip.com/Drivers/VCP.htm). You want the VCP driver for your host, not some of the other drivers they offer. 

* **Please note: As of Mavericks (OSX 10.9.x) OSX will appear to communicate with TinyG without loading the FTDI supplied drivers. However, there is a bug in the mac drivers that will cause a job to stall midstream. You still need to install the FTDI suppled drivers. For OSX you want the [FTDI VCP driver of OSX, version 2.2.18](http://www.ftdichip.com/Drivers/VCP.htm), presumably 64 bit X86 if that's what your machine supports.**

####Get Coolterm
* Download and connect to Roger Meier's [Coolterm](http://freeware.the-meiers.org/). You will need the FTDI drivers mentioned above to do this. Go to the Options menu and Re-Scan Serial Ports. You should see something like `usbserial-AE01DVWD`. Configure the following settings:
 * 115,200 baud
 * 8 data bits
 * no parity
 * 1 stop bit
 * XON flow control (assuming you are using XON. See note in **Verify Flow Control** for RTS/CTS usage)<br>

* It's also useful to set the following - but not strictly necessary
 * Options/Terminal - Line Mode
 * Options/Enter Key Emulation - CR

* Hit OK to leave the Options menu

####Connect to TinyG
* Hit the "Connect" button. Enter a few carriage returns. TinyG should respond with prompts. If not, hit the reset button on the TinyG. You should see some JSON startup messages wrapped in JSON curly braces something like this:
<pre>
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"Loading configs from EEPROM","f":[1,15,0,8891]}}
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"SYSTEM READY","f":[1,0,0,8820]}}
tinyg [mm] ok> 
</pre>
* If not, go back and check your driver, your serial settings, your USB cable, and that you have a blue light and not blue smoke. For help from the command line enter 'h' for TinyG help, or '$h' for configuration help 

* **If you simply cannot connect try powering down the TinyG and quitting Coolterm (or your terminal program), powering back up and restarting the terminal. There is a known bug in the FTDI drivers that can cause this sometimes.**

####Verify Flow Control
* Verify you have flow control configured properly. The default flow control for TinyG is XON, which uses the folliwing settings:
 * Coolterm: `XON` checked
 * TinyG `$ex=1`

* RTS/CTS flow control is also available. Both Coolterm and TinyG must be configured the with these settings:
 * Coolterm: `CTS` checked
 * Coolterm: `DTR` checked
 * TinyG: `$ex=2`

## Wire Your Motors
First, turn off the power to TinyG. Never connect or disconnect anything (except possibly USB) with the power on.

### Synthetos Connector Kit
For v7 and above TinyG's no longer need ANY stepper motor connector packs. The newest versions use screw terminals.

### Find the Coil Pairs
The first thing you need to do is make sure you know which wires are connected to the same coil. 

Bipolar motors have 4 wires (2 pairs), Unipolar motors typically have 6. Some other motors have 5, or 8, or whatever. 8 wire motors are usually wired as 2 sets of bipolar windings (i.e. essentially 2 bipolars wired together). 5 wire motors are usually in a "star" configuration that has a common ground and require a specialized driver. TinyG cannot drive 5 wire steppers.

The following color code is typical for many motors

<pre>
	Color    | Bipolar      | Unipolar      | Notes
	---------|--------------|---------------|--------
	Green    |  Winding A1  | Winding A1    |
	Yellow   |  (none)      | Center tap A  |
	Black    |  Winding A2  | Winding A2    |
	Red      |  Winding B1  | Winding B1    |
	White    |  (none)      | Center tap B  |
	Blue     |  Winding B2  | Winding B2    |

</pre>

Use your volt meter to verify that green and black connect together, and red and blue connect together, and that they don't connect to the other pair. Typical DC resistance across a winding is about 1 to 5 ohms. If you have a Unipolar motor you can just leave the center taps disconnected.

If this doesn't work here's a shortcut to finding wire pairs for a bipolar (4 wire) motor.

Spin your stepper motor with your fingers. Depending on the size / holding torque this could be easy or pretty hard. All you really want from this is to get a feel how the motor spins without any of the wires connected to each other. Now that you know how hard it is to spin with your fingers, connect 2 wires together. Just pick any two. Try to spin the motor again. If it feels the same then more than likely these are NOT connected to the same coil. Disconnect these wires. Connect one of the other wires to one of the first wire pairs you tried. Try to spin the motors again. This should be much harder. If so, you have found your wire pairs. Tape these 2 together (not wired but just taped to group them). Tape the remaining 2 wires together as well. 

Unipolars are a bit more complicated, but not much. To do a series wiring find the outer taps of each coil. These are often color coded by convention (see below). Using a volt meter to find the resistance across the outer pair. The resistance between the center tap an an outer tap will be 1/2 the resistance between the outer taps. 

Some useful information on wiring steppers can be found here: [http://reprap.org/wiki/Stepper_motor](http://reprap.org/wiki/Stepper_motor)

### Connector Pinouts
Back to connecing your TinyG. Each of the 4 motor outputs have a four pin terminal block wired as: 

* A1
* A2
* B1
* B2

Attach one pair to A1/A2 and the other pair to B1/B2. If your motor spins the wrong way, you can swap A1 with A2 and vice versa. This can also be done in software by using the polarity setting, e.g. $1po=1 to invert.

## Setting Motor Current
**WARNING: Do not turn the current trimpots too hard. They will break. They adjust from the 8pm position to 4pm, only.**

Motor current for each axis is adjusted with the trimpot nearest that axis. Clockwise increases current, counter-clockwise decreases current.

You want the motor current set slightly above the range you need for your application, but not much higher. Overdriving the motors draws more current and risks overheating or thermal shutdown. 

Overcurrent also causes your motors to heat up a lot more. Stepper motors draw the most current when they are not moving so this is when they will get the hottest. Motors that run too hot run the risk of being ruined by demagnetizing.

Begin by setting the current to zero by gently turning the trimpot all the way counter-clockwise. Then issue a very long Gcode command for that axis, something like g0x1000.  Turn the trimpot clockwise until the motor starts moving reliably. You can hit the reset button and re-enter the Gcode command to verify that it will start at this current setting. Note this lower bound pot setting. 

Next continue to turn up the pot until the motor starts to cycle on and off - indicating thermal shutdown is occurring. Now back off until the cycling stops and mark this as your upper limit. Cycling will occur under thermal shutdown, and only gets more severe as the current goes up - where it appears the motor is stuttering. Thermal shutdown is, of course, to be avoided, but we've never seen it actually damage the drivers or motors and we've seen some pretty abusive cases (actually, we caused them)! Mark the spot just below thermal shutdown as the upper limit. Now read about cooling to increase the upper limit. 

When you actually run jobs you want to back off the current as much as you can while still running reliably to somewhere between your upper and lower limit. This will minimize board and motor heating.

Now is also a good time to make a mental note to enable Low Power Idle mode (e.g. $1pm=1) if you have a leadscrew-type machine or some other configuration that will hold position while idle without power being applied to the motors. See [TinyG Configuration](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)

## Cooling
You can get more headroom before thermal shutdown by cooling the board. As much of the board as possible is 2 oz. heatsink copper. Both top and bottom copper provide cooling. But passive cooling can only do so much given the TinyG footprint. 

Fan cooling is the best way to cool. TinyG uses copper for cooling both on the top and the bottom of the board, so make sure air flow is provided to both surfaces.

## Configuring Limit and Homing Switches
Version 8 of TinyG has screw terminals for connecting your limit and homing switches. [This page](https://github.com/synthetos/TinyG/wiki/Homing-and-Limits-Description-and-Operation) describes setup, operation and troubleshooting of the limit and homing switches.

# Initial Setup
Now that you are connected your motors and limit switches, move on the [Initial Setup.](https://github.com/synthetos/TinyG/wiki/Initial-Setup)