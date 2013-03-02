**Huge thanks to Kevin Osborn who got this working**

## Updating TinyG Firmware using the Boot Loader

**Step1**: To update the TinyG firmware run avrdude from a directory that has the tinyg.hex file you want to load. Here's an example command line from Windows:<br>
`avrdude -p x192a3 -c avr109 -b 115200 -P COM19 -U flash:w:tinyg.hex`

You will need to enter the USB port you are actually using.<br>
Use the Avrdude distributed with the Arduino - it's pretty up to date. 
Before issuing the command, enter the bootloader by hitting the reset button, or enter the new boot command in g-code or JSON (e.g. $Boot=1, {"boot":1}). You need to enter the avrdude command before the LED stops blinking. It currently blinks 10 times. 

## Flashing the Boot Loader onto the Xmega Chip
The following instructions explain how to flash the boot loader using Atmel Studio6. AVRStudio4 is similar, as would be command-line operation. These instructions use the xboot.hex file already in the project. If you want to compile go to the next section.

**Step 1**. Get the right programmer. The xmega requires PDI programming. Use the Atmel [AVRISP mkii](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=%2fha2pyFaduiLEF45YHzXlzYdfQlCIaNgRBHMmCoiTxs%3d) or some other programmer that supports PDI programming. Plug the programmer into TinyG and apply power to TinyG.

**Step 2**. Bring up Studio6 and the device programming panel. Look under `Tools / Device Programming`
* In the programming panel verify Tool is AVRISP mkii, the device is ATxmega192A3 and the interface is PDI. Hit `Apply`, then `Read`. You should see the Device signature and voltage field populate. Voltage should be 3.2v or thereabouts.

**Step 3**. Set the fuses. Go to Fuses and set the following
* set BOOTRST to BOOTLDR

Other values should be left alone. These are:
 * JTAGUSERID = 0xFF
 * WDWP = 8clk
 * WDP = 8clk
 * DVSDON = (unchecked)
 * BODPD = disabled
 * RSTDISBL = (unchecked)  Don't check this - you will brick the board
 * SUT = 0ms
 * WDLOCK = (unchecked)
 * JTAGEN = (CHECKED) Don't uncheck this, you will brick the board
 * BODACT = disabled
 * EESAVE = (unchecked)
 * BODLVL = 1v6

Hit `Program` to program the fuses

**Step 4**. Use xboot.hex for programming. Do not use xboot-boot.hex as it's org'ed in the wrong place (0 instead of 0x30000). You'll also need to specify `Erase Flash Before programming` or it won't verify.
[put screen capture of device programming here]

## Compiling the Boot Loader for TinyG
Use these instructions if you want to change the xboot-boot.hex file. If all you want to do is flash it onto TinyG see the previous section.

Step 1. The command line you need is:<br>
`make conf\x192a3.conf.mk`

But beware - it only works in windows. Use cmd and navigate to the (lower) xboot directory something like:
`Z:\Username\Projects\...\TinyG\xboot\xboot\make conf\x192a3.conf.mk`

It enters an infinite loop in Linux and generates this error iin OSX

<pre>
macintosh-3:xboot your-user-dir$ make conf\x192a3.conf.mk
.dep/fifo.o.d:1: *** multiple target patterns.  Stop.
macintosh-3:xboot your-user-dir$
</pre>

We have not diagnosed or fixed this yet.

## Developer Notes
The boot loader is Alex Forencich's xboot which can be found in the xboot directory in the main TinyG git tree. This xboot has the settings and modifications for use on TinyG. (You can safely ignore the xmega_boot directory.)

### Getting Xboot to work in Native Windows
Bring up a cmd window and navigate to the working directory. Run the command:
<pre>
make conf/x192a3.conf.mk
</pre>
You should now be ready to flash xboot-boot.hex onto the xmega192. Refer to the Flashing... section, above.

### Getting Xboot to work in Atmel Studio6
The xboot project has been set up under Atmel Studio6 (Windows only). The project files are the xboot.atsln and xboot.cproj files in the github TinyG/support dir. Move these to your project directory then click on xboot.atsln to start the project up. 

This project uses the Studio6 auto-generated makefile in the Debug directory instead of the native xboot Makefile in the working directory. This is so the debugger can support symbolic debugging. The following need to be done for this to work.

1. You must generate config.h by running `make conf/x192a3.conf.mk`. If the conf file changes you must run this again to refresh config.h
2. Setup the xboot project properties:
<pre>
in Build Events / post-build event:
avr-objcopy -O ihex --change-addresses -0x30000 xboot.hex xboot-boot.hex
avr-nm -n xboot.elf > xboot.sym
</pre>
<pre>
in Symbols:
F_CPU=32000000
USE_CONFIG_H
</pre>
<pre>
in Optimization:
-Os
</pre>
<pre>
in Miscellaneous
-mmcu=atxmega192a3 -I. -gstabs -std=gnu99 -ffunction-sections -fdata-sections -fno-jump-tables -Wa,-adhlns=flash.lst -Wstrict-prototypes
-mmcu=atxmega192a3 -I. -gdwarf-2 -std=gnu99 -ffunction-sections -fdata-sections -fno-jump-tables -Wa,-adhlns=flash.lst -Wstrict-prototypes
</pre>
<pre>
in Linker/Memory Settings:
.text=0x18000    (yes, it's 0x30000 divided by 2)
</pre>

Still to do:
* Find a way to pre-process the config.h file