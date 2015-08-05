[<<< Prev - Getting Started](TinyG-Start) --- [Next - Test Drive>>>](Test-Drive-TinyG)<br><br>
If you are on this page you have already selected your motors and power supply and are ready to hook them up.

Connecting TinyG Steps
* [Connect power](#connect-power)
* [Connect USB](#establish-usb-connection)
* [Test connection](#test-connection-to-tinyg)
* [Wire your motors](#wire-your-motors)
* [Set motor current](#setting-motor-current)
* [Test drive](Test-Drive-TinyG)

## Connect Power
The **MOST IMPORTANT** thing to do is to wire your power input correctly. So check and double check this before actually turning on the power.

![TinyG diagram version 8](http://farm3.staticflickr.com/2842/10495433164_c3e47f475a_o.png)

**THIS PAGE ASSUMES THAT THE POWER SUPPLY'S BLACK IS GROUND AND RED OR YELLOW IS +24VOLT (HOT)**. Not all power supplies adhere to this convention so you still need to check this carefully. Read below:

1. Check you have the correct power supply. You should have a DC power supply between 12 and 30 volts --- 24 volts is ideal. It should be capable of providing 4 to 15 amps. 
1. If you have a power supply where you are responsible for wiring the AC lines (like a Meanwell or some other supply with a terminal strip) - make sure the AC is wired correctly. By convention, the AC neutral is the white wire in the power cord, the hot is the black wire and the ground is green. Make sure the ground is connected. Not only for safety, but also because you may experience spurious resets if the system is not properly grounded.
1. BEFORE connecting it to TinyG, turn on the power supply and make sure you have correct voltage. Make sure it is DC, not AC. Hopefully you have a volt meter or can get your hands on one. If you don't you might consider a trip to the Radar Shed for something like [this](http://www.radioshack.com/product/index.jsp?productId=4214667).
1. With the power supply off, wire the negative to the GND terminal of the power input block, and the positive to the +Vmot side. Negative is BLACK by convention and positive is RED or YELLOW, or some other bright color. Check you have the correct voltage and polarity before connecting the power supply to TinyG. We have encountered power supplies that do not obey these conventions, and incorrect voltage or polarity can destroy your board!
1. Turn on the power supply. If the blue light turns on this is a good sign. If not, blow away the smoke and send us an email.

## Establish USB connection
Next establish USB connection with your host computer. 

####Install FTDI Drivers
* If you do not have the FTDI VCP USB drivers for your host computer you will need to install them. It's possible they are already on your system as many applications use them, including the older Arduinos. 

* PC Installation - Get the latest Windows driver from the [FDTI VCP Driver Page](http://www.ftdichip.com/Drivers/VCP.htm). You want the VCP driver for your host, not some of the other drivers they offer. Install and reboot your system.

* MAC Installation - As of Mavericks (OSX 10.9.x) OSX will appear to communicate with TinyG without loading the FTDI supplied drivers. However, the native mac drivers do not perform flow control with the FTDI on the v8, so they will not work. You need the VCP 2.3 driver from the [FDTI VCP Driver Page](http://www.ftdichip.com/Drivers/VCP.htm). You must also reboot your system once the installation is finished (they don't tell you this). If installed correctly you should see something like `usbserial-DA00Y5MM` when you scan for new serial ports. (Note that the Mac native driver will also show you this, sos the only real way to tell if you have the FTDI driver is to look in APPLE_IN_THE_UPPER_LEFT_CORNER / About This Mac / System Report / Software / Extensions for FTDIUSBSerialDriver, version 2.3)

####Set up Coolterm
Once you have the FTDI drivers in place you will want Roger Meier's Coolterm to connect and test your tinyg. Coolterm is a terminal emulator that provides command line access to TinyG and can also stream files to TinyG.  We use Coolterm as the preferred way to test the board without introducing variables from advanced UIs and host controllers such as [ChiliPeppr](Chilipeppr)

* Download and install [Coolterm](http://freeware.the-meiers.org/). For the Mac we have noticed that the latest version (1.4.5) does not do well on large file transmissions. So we still use version 1.4.3. This is available on the Coolterm page under [Previous Releases](http://freeware.the-meiers.org/previous/).

* Go to the Options menu and Re-Scan Serial Ports. You should see something like `usbserial-AE01DVWD` or `COM12`. Configure the following settings:
 * 115,200 baud
 * 8 data bits
 * no parity
 * 1 stop bit
 * XON flow control (or use CTS, but make sure the $ex setting agrees)

* It's also useful to set the following - but not strictly necessary
 * Options/Terminal - Line Mode
 * Options/Enter Key Emulation - CR

* Hit OK to leave the Options menu

####Test Connection to TinyG
* Hit the "Connect" button. Enter a few carriage returns. TinyG should respond with prompts. If not, hit the reset button on the TinyG. You should see some JSON startup messages wrapped in JSON curly braces something like this:
<pre>
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"Loading configs from EEPROM","f":[1,15,0,8891]}}
{"r":{"fb":371.030,"fv":0.950,"hv":7.000,"id":"9H3583-RMP","msg":"SYSTEM READY","f":[1,0,0,8820]}}
tinyg [mm] ok> 
</pre>
* If not, go back and check your driver, your serial settings, your USB cable, and that you have a blue light and not blue smoke. For help from the command line enter 'h' for TinyG help, or '$h' for configuration help 

* **If you simply cannot connect try powering down the TinyG and quitting Coolterm (or your terminal program), powering back up and restarting the terminal. There is a known bug in the FTDI drivers that can cause this sometimes.**

**Verify Flow Control**
Once you are connected it's a good idea to verify you have the correct flow control settings

* The default flow control for TinyG is CTS, which uses the following settings:
 * Coolterm: `CTS` checked
 * TinyG `$ex=2`

* XON/XOFF flow control is also available. Both Coolterm and TinyG must be configured the with these settings:
 * Coolterm: `XON` checked
 * TinyG: `$ex=1`

## Wire Your Motors
It's best to wire your motors when they are not yet in your machine. This way you can test drive them without worrying about mechanical issues such as excessive friction or crashing into the side of the machine.

If your motors are already in a machine sometimes it's easier just to use one or more spare motors rather than remove them. It will not damage the TinyG board to drive an axis with no motor attached.

But first, turn off the power to TinyG. Never connect or disconnect anything (except possibly USB) with the power on.

### Motor Connectors (was "Synthetos Connector Kit")
Most TinyG v7s and above have screw terminals and therefore do not need a motor connector kit. If you have a board that has quick release headers on it here are the mating parts:
* Terminal Shell - Molex 09-50-3041 (need 1 per motor)
* Crimp Terminals - Molex 08-50-0134 (need 4 per motor, and usually some extras)

You need 4 terminals for each housing (shell). You can get these from Mouser or DigiKey. TE/Amp also makes some nice parts with the shell and terminals all-in-one: 
* TE/Amp 3-644463-4 (need one per motor)

### Find the Coil Pairs
The first step is make sure you know which wires are connected to the same coil. 

The following are common motor types:

* Bipolar motors have 4 wires (2 pairs). This wire color code is typical for many bipolar motors:

	Wire Color  | Winding
	---------|-------------
	Green    |  Winding A1
	Black    |  Winding A2
	Red      |  Winding B1
	Blue     |  Winding B2 

* Unipolar motors typically have 6 wires also connected to to 2 windings. The additional wires are the center taps for each winding. These can usually be ignore (not connected). This wire color code is typical for many unipolar motors:

	Wire Color | Winding      
	---------|------------
	Green    | Winding A1
	Yellow   | Center tap A
	Black    | Winding A2
	Red      | Winding B1
	White    | Center tap B
	Blue     | Winding B2 

* 8 wire motors: 8 wire motors are usually wired as 2 sets of bipolar windings - essentially these are 2 bipolars wired together. They can be wired by finding which pairs are in parallel with each other and either not using the second winding, or wiring it in parallel for more torque (and more current draw). There is no common color code for 8 wire motors. (Please correct me if I'm wrong).

* 5 wire motors are in a "star" configuration that has a common ground and require a specialized driver. TinyG cannot drive 5 wire steppers.


To determine which wires go to which windings use a volt meter to verify that green and black connect together, and red and blue connect together, and that they don't connect to the other pair. Typical DC resistance across a winding is about 1 to 5 ohms. If you have a Unipolar motor the center taps will show 1/2 the resistance of the full winding.

Here's a shortcut to finding wire pairs for a bipolar (4 wire) motor.

Spin your stepper motor with your fingers. Depending on the size / holding torque this could be easy or pretty hard. All you really want from this is to get a feel how the motor spins without any of the wires connected to each other. Now that you know how hard it is to spin with your fingers, connect 2 wires together. Just pick any two. Try to spin the motor again. If it feels the same then more than likely these are NOT connected to the same coil. Disconnect these wires. Connect one of the other wires to one of the first wire pairs you tried. Try to spin the motors again. This should be much harder. If so, you have found your wire pairs. Tape these 2 together (not wired but just taped to group them). Tape the remaining 2 wires together as well. 

Unipolars are a bit more complicated, but not much. To do a series wiring find the outer taps of each coil. These are often color coded by convention (see below). Using a volt meter to find the resistance across the outer pair. The resistance between the center tap an an outer tap will be 1/2 the resistance between the outer taps. 

Some useful information on wiring steppers can be found here: [http://reprap.org/wiki/Stepper_motor](http://reprap.org/wiki/Stepper_motor)

### Connector Pinouts
Back to connecting your TinyG. Each of the 4 motor outputs have a four pin terminal block wired as: 

* A1
* A2
* B1
* B2

Attach one pair to A1/A2 and the other pair to B1/B2. If your motor spins the wrong way, you can swap A1 with A2 and vice versa. This can also be done in software by using the polarity setting, e.g. $1po=1 to invert.

## Setting Motor Current
**WARNING: Do not turn the current trimpots too hard. They will break. They adjust from the 8pm position to 4pm, only.**

* Motor current for each axis is adjusted with the trimpot nearest that axis. Clockwise increases current, counter-clockwise decreases current.

* You want the motor current set slightly above the range you need for your application, but not much higher. Overdriving the motors draws more current and risks overheating or thermal shutdown. 

  A motor that is receiving too much current may also run "rough". If you experience this try backing off the current and see if the motor runs more smoothly.

  Overcurrent also causes your motors to heat up a lot more. Stepper motors draw the most current when they are not moving so this is when they will get the hottest. Motors that run too hot run the risk of being ruined by demagnetizing.

* Follow these steps to set the motor current:
   * Set current to zero by gently turning the trimpot all the way counter-clockwise
   * Send a relatively slow move to that axis, something like `g1 f400 x50`
   * Adjust the current up (clockwise) until the motor moves or starts humming (meaning it's stalled)
   * Try the move again at this new setting. If it's still stalled increase the current some more and try again, or drop to a slower feed rate (F value).
   * Once the motor is running turn the current down until it stops. Mark this as the low current spot.
   * Run the motor again and adjust current up until it runs rough or goes into thermal shutdown (see below).
   * Now back off until the cycling stops and the motor runs smoothly, and mark this as your upper limit.
   * Repeat the above with a high speed move, like a `g0 x100`. This should also work without stalling. If it stalls you may need to adjust the maximum velocity or jerk settings - you may need to tune those. See TinyG Tuning. 
   * When you actually run jobs you want to back off the current as much as you can while still running reliably to somewhere between your upper and lower limit. This will minimize board and motor heating.

  If the motor starts to cycle on and off it indicates thermal shutdown is occurring. Cycling will occur under thermal shutdown, and only gets more severe as the current goes up - where it appears the motor is stuttering. Thermal shutdown is, of course, to be avoided, but we've never seen it actually damage the drivers or motors and we've seen some pretty abusive cases (actually, we caused them)!

* Now is also a good time to check out [Power Management](TinyG-Configuration-for-Firmware-Version-0.97#1pm---power-management-mode). By default power management is set to $_pm=2: "Motor powered during a machining cycle" (i.e. when any axis is moving).

## Cooling
Most NEMA17 applications we have seen do not require any additional cooling. Passive cooling as fine as long as you allow for convection. The chip transfers most of the heat to the 2 oz. copper on the bottom of the board. Make sure you have adequate airflow (convection) across the bottom and the top of the board. Vertical orientations are nice for this.

If you are using those large NEMA17s (e.g. 125 oz.in) or NEMA23's you may need fan cooling. Again - provide adequate airflow across both sides of the board.

Heatsinks are not necessary and should be avoided unless you really need them. If you do decide you need heatsinks attach them to the bare copper on the bottom - not the tops of the chips. Use the exposed pads and be careful not to exceed the pads or you risk shorting some signals to ground.

# Test Drive
Now that you are connected your motors and limit switches, move on the [Test Drive your TinyG](https://github.com/synthetos/TinyG/wiki/Test-Drive-TinyG)