## Connect Power
The **MOST IMPORTANT** thing to do is to wire your power input correctly. So check and double check this before actually turning on the power.

![Image of TinyG Connected to Power](http://farm9.staticflickr.com/8225/8503620821_795a1fa867_b.jpg)

What you see above is a TinyG that is connected correctly. **THIS ASSUMES THAT THE POWER SUPPLY WIRING ADHERES TO CONVENTION AND BLACK IS GROUND AND +24VOLT (HOT) IS RED OR YELLOW**. Not all power supplies adhere to convention so you still need to check this carefully. Read below:

1. Check you have the correct power supply. You should have a DC power supply between 12 and 30 volts --- 24 volts is ideal. It should be capable of providing 4 to 15 amps (or more). BEFORE connecting it to TinyG, turn on the power supply and make sure you have correct voltage and that its DC, not AC. Hopefully you have a volt meter or can get your hands on one. If you don't you might consider a trip to the Radar Shed for something like [this](http://www.radioshack.com/product/index.jsp?productId=4214667).
1. Wire the negative to the GND terminal of the power input block, and the positive to the +Vmot side. Negative is BLACK by convention and positive is RED, or YELLOW, or some other bright color by convention. Check you have the correct voltage and polarity before connecting the power supply to TinyG. We have encountered power supplies that do not obey these conventions!
1. Turn on the power supply. If the blue light turns on this is a good sign. If not, blow away the smoke and send us an email.

## Establish USB connection
Next establish USB connection with your host computer. 

1. If you do not have the FTDI VCP USB drivers for your system you will need to install these. It's quite possible they are already on your system as many applications use these, including the older Arduinos. The easiest way to check if you have them is to fire up CoolTerm (see step 2) and see if something like `tty.usbserial-AE01DVWD` or `usbserial-CRAZYMON` shows up when you scan the serial ports. If not, unplug TinyG and install the drivers. You can get then from the [FDTI VCP Driver Page](http://www.ftdichip.com/Drivers/VCP.htm). You want the VCP driver for your host, not some of the other drivers they offer.

2. Download and connect to Reger Meier's [Coolterm](http://freeware.the-meiers.org/). You will need the FTDI drivers mentioned above to do this. Go to the Options menu and Re-Scan Serial Ports. You should see something like `usbserial-AE01DVWD`. Configure the following settings:
 * 115,200 baud
 * 8 data bits
 * no parity
 * 1 stop bit<br>
It's also useful to set the following - but not strictly necessary
 * Options/Terminal - Line Mode
 * Options/Enter Key Emulation - CR

3. Hit OK to leave the Options menu 

3. Hit the "Connect" button. Enter a few carriage returns; you may get some prompting action. If not, hit the reset button on the TinyG. You should see some JSON startup messages wrapped in JSON curly braces something like this:
<pre>
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"Loading configs from EEPROM","f":[1,15,0,8891]}}
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"SYSTEM READY","f":[1,0,0,8820]}}
tinyg [mm] ok> 
</pre>
If not, go back and check your driver, your serial settings, your USB cable, and that you have a blue light and not blue smoke.

For help from the command line enter 'h' for TinyG help, or '$h' for configuration help 

## Wire Your Motors
First, turn the power back off. Never connect or disconnect anything (except possibly USB) with the power on.

### Synthetos Connector Kit
For all v7 and above TinyG's no longer need ANY stepper motor connector packs.  

### Find the Coil Pairs
The first thing you need to do is make sure you know which wires are connected to the same coil. 

Bipolar motors have 4 wires (2 pairs), Unipolar motors typically have 6. <br>
Some other motors have 5, or 8, or whatever. 8 wire motors are usually wired as 2 sets of bipolar windings (i.e. essentially 2 bipolars wired together). 5 wire motors are usually in a "star" configuration that has a common ground and require a specialized driver. TinyG cannot drive 5 wire steppers.

The following color code is typical for many motors

	Color | Bipolar | Unipolar | Notes
	---------|--------------|---------|----
	green |  Winding A1 | Winding A1 |
	yellow |  (none) | Center tap A  |
	black |  Winding A2 | Winding A2 |
	red |  Winding B1 | Winding B1 |
	white |  (none) | Center tap B |
	blue |  Winding B2 | Winding B2 |

Do bring out that voltmeter you got at Radar Shed and verify that green and black connect together, and red and blue connect together, and that they don't connect to the other pair. Typical DC resistance across a winding is about 1 to 5 ohms. If you have a Unipolar you can just leave the center taps disconnected.

If this doesn's work here's a shortcut to finding wire pairs for a bipolar (4 wire) motor.

Spin your stepper motor with your fingers. Depending on the size / holding torque this could be easy or pretty hard. All you really want from this is to get a feel how the motor spins without any of the wires connected to each other. Now that you know how it "feels" (how hard it is to spin with your fingers) connect 2 wires together. Just pick 2. Try to spin the motor again. If it feels the same then more than likely these are NOT connected to the same coil. Disconnect these wires. Connect one of the other wires to one of the first wire pairs you tried. Try to spin the motors again. This should be much harder. If this is so, you have found your wire pairs. Tape these 2 together (not wired but just taped to group them). Tape the remaining 2 wires together as well. 

Unipolars are a bit more complicated, but not much. To do a series wiring find the outer taps of each coil. These are often color coded by convention (see below). Using a DVM find the resistance across the outer pair. The resistance between the center tap an an outer tap will be 1/2 the resistance between the outer taps. 

Some useful information on wiring steppers can be found here: http://reprap.org/wiki/Stepper_motor

### Connector Pinouts
Each of the 4 motors has a four pin terminal block wired as: 

* A1
* A2
* B1
* B2

Attach one pair to A1/A2 and the other pair to B1/B2. If the motor spin needs to be inverted you can do this in hardware by reversing one of the pairs (e.g. swap A1 with A2 and vice versa), or in software by using the polarity setting, e.g. $1po=1 to invert.

## Setting Motor Current
**WARNING: Do not over-torque the current trimpots. they will break. They have 270 degrees of travel only.**

Motor current for each axis is adjusted with the trimpot nearest that axis. Clockwise increases current, counter-clockwise decreases current.

You want the motor current set slightly above the range you need for your application, but not much higher. Overdriving the motors draws more current and risks overheating or thermal shutdown. 

Start by setting current to zero by gently turning the trimpot all the way counter-clockwise. Then issue a very long Gcode command for that axis, something like g0x1000.  Turn the trimpot clockwise until the motor starts moving reliably. You can hit the reset button and re-enter the Gcode command to verify that it will start at this current setting. Note this lower bound pot setting. 

Next continue to turn up the pot until the motor starts to cycle on and off - indicating thermal shutdown is occurring. Now back off until the cycling stops. Cycling will occur under thermal shutdown, and only gets more severe as the current goes up - where it appears the motor is stuttering. Thermal shutdown is, of course, to be avoided, but we've never seen it actually damage the drivers or motors and we've seen some pretty abusive cases (actually, we caused them)! Mark the spot just below thermal shutdown as the upper limit. Now read about cooling to increase the upper limit. 

Now is also a good time to make a mental note to enable Low Power Idle mode if you have a leadscrew-type machine or some other configuration that will hold position while idle without power being applied to the motors. See [TinyG Configuration](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)

## Cooling
You can get more headroom before thermal shutdown by cooling the board. As much of the board as possible is 2 oz. heatsink copper.<br>Both top and bottom copper provide cooling. But you can only do so much given the limits of the TinyG footprint. 

Heatsinking the drivers will help. We offer some small heatsinks on the site, or a normal DIP or T0-220 heatsink will fit.

Better still is a fan blowing over the board's stepper drivers and (v7 only) voltage regulator. 

Or both. 

## Configuring Limit and Homing Switches
TinyG has 8 limit and homing switches on the GPIO2 port. This requires its own page: [TinyG Homing and Limits](https://github.com/synthetos/TinyG/wiki/TinyG-Homing) 

# Configuring TinyG
Now that you are connected move on the [Configuring TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration)