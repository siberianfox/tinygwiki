**Huge thanks to Kevin Osborn who got this working**

## Updating TinyG Firmware using the Boot Loader
You will find the boot 

## Flashing the Boot Loader onto the Chip
The following instructions explain how to flash the boot loader using Atmel Studio6. AVRStudio4 is similar, as would be command-line.

1. Get the right programmer. The xmega requires PDI programming. Use the Atmel AVRISP2 (mkii) or some other programmer that supports PDI programming.
1. Use xboot-boot.hex for programming. Do not use xboot.hex as it's org'ed in the wrong place (0 instead of 0x30000)


## Developer Notes
THe boot loader is Alex Forencich's xboot which can be found in the xboot directory in the main TinyG git tree. This xboot has the settings and modifications for use on TinyG. (You can safely ignore the xmega_boot directory.)

The xboot project has been set up under both AVRStudio4 and Atmel Studio6, or it can be run from the command line. To invoke 4 or 6 (Windows only) you need to move the project files into your working directory. Project files are in the /support dir and are all the xboot files. CLick on xboot.aps to invoke 4, xboot.atsln for 6. You can also just use the command line as xboot has its own Makefile and Makefile generation has been disabled in those project files.