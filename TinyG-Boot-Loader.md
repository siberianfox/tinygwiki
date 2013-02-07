**Huge thanks to Kevin Osborn who got this working**

## Updating TinyG Firmware using the Boot Loader
You will find the boot 

## Flashing the Boot Loader onto the Xmega Chip
The following instructions explain how to flash the boot loader using Atmel Studio6. AVRStudio4 is similar, as would be command-line operation. These instructions use the xboot-boot.hex file already in the project. If you want to compile go to the next section.

**Step 1**. Get the right programmer. The xmega requires PDI programming. Use the Atmel [AVRISP mkii](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=%2fha2pyFaduiLEF45YHzXlzYdfQlCIaNgRBHMmCoiTxs%3d) or some other programmer that supports PDI programming. Plug the programmer into TinyG and apply power to TinyG.

**Step 2**. Bring up Studio6 and the device programming panel. Look under Tools/Device Programming
* In the programming panel verify Tool is AVRISP mkii, the device is ATxmega192A3 and the interface is PDI. Hit Apply. You should see the Device signature and voltage field populate. Voltage should be 3.2v or thereabouts.

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
 * Leave the lock bits alone for now

**Step 4**. Use xboot-boot.hex for programming. Do not use xboot.hex as it's org'ed in the wrong place (0 instead of 0x30000)

## Compiling the Boot Loader for TinyG
Use these instructions if you want to change the xboot-boot.hex file. If all you want to do is flash it onto TinyG see the previous section.

Step 1. The command line you need is:<br>
`make conf\x192a3.conf.mk`

But beware - it only works in windows. Use cmd and navigate to the (lower) xboot directory something like:
`Z:\Username\Projects\...\TinyG\xboot\xboot\make conf\x192a3.conf.mk`

It enters an infinite loop in Linux and generates this error iin OSX

`macintosh-3:xboot your-user-dir$ make conf\x192a3.conf.mk
.dep/fifo.o.d:1: *** multiple target patterns.  Stop.
macintosh-3:xboot your-user-dir$
`
We have not diagnosed or fixed this yet.

## Developer Notes
THe boot loader is Alex Forencich's xboot which can be found in the xboot directory in the main TinyG git tree. This xboot has the settings and modifications for use on TinyG. (You can safely ignore the xmega_boot directory.)

The xboot project has been set up under both AVRStudio4 and Atmel Studio6 (Windows only). Project files are the xboot.* files in the github TinyG/support dir. Move these to your project directory then click on xboot.aps to invoke 4, xboot.atsln for 6. Note that both of these projects use an external Makefile and therefore have makefile generation disabled. You do not want to turn in internal makefile or you will clobber the real Makefile.

The makefile can also be compiled from the command line but Kevin got an infinite loop in Linux. It worked under Windows.