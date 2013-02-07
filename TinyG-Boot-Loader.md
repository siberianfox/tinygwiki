**Huge thanks to Kevin Osborn who got this working**

## Updating TinyG Firmware using the Boot Loader
You will find the boot 

## Flashing the Boot Loader onto the Chip
The following instructions explain how to flash the boot loader using Atmel Studio6. AVRStudio4 is similar, as would be command-line.

* Get the right programmer. The xmega requires PDI programming. Use the Atmel [AVRISP mkii](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=%2fha2pyFaduiLEF45YHzXlzYdfQlCIaNgRBHMmCoiTxs%3d) or some other programmer that supports PDI programming. Plug the programmer into TinyG and apply power to TinyG.

* Bring up Studio6 and the device programming panel. Look under Tools/Device Programming
 * In the programming panel verify Tool is AVRISP mkii, the device is ATxmega192A3 and the interface is PDI. Hit Apply. You should see the Device signature and voltage field populate. Voltage should be 3.2v or thereabouts.
 * Go to Fuses and set the following
  * BOOTRST to BOOTLDR
  * Other values should be left alone. These are:
    * JTAGUSERID = 0xFF

 
 
* Use xboot-boot.hex for programming. Do not use xboot.hex as it's org'ed in the wrong place (0 instead of 0x30000)


## Developer Notes
THe boot loader is Alex Forencich's xboot which can be found in the xboot directory in the main TinyG git tree. This xboot has the settings and modifications for use on TinyG. (You can safely ignore the xmega_boot directory.)

The xboot project has been set up under both AVRStudio4 and Atmel Studio6, or it can be run from the command line. To invoke 4 or 6 (Windows only) you need to move the project files into your working directory. Project files are in the /support dir and are all the xboot files. CLick on xboot.aps to invoke 4, xboot.atsln for 6. You can also just use the command line as xboot has its own Makefile and Makefile generation has been disabled in those project files.