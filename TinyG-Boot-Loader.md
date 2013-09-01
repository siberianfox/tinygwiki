_Huge thanks to Kevin Osborn who got this working_

**Topics:**
* [How Do I Know if I Have the Bootloader?](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-howdoiknow)
* [Updating TinyG Firmware using the Boot Loader](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-updating)
* [Flashing the Boot Loader onto the Xmega Chip](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-flashing)
* [Project Setup and Compiling the Boot Loader for TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-projectsetup)
* [Programming TinyG with the Atmel AVRISP Mkii Programmer](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-avrisp)

<a id="howdoiknow"></a>
# How Do I Know if I Have the Bootloader?
TinyG's shipped from March 10, 2013 have a boot loader that supports flashing the TinyG firmware using AVRdude (aka the AVR109 protocol). You can tell if you have the boot loader if the Spindle Direction LED flashes when you hit reset:

* On hardware reset (reset button) or software reset (Control-X) the spindle direction bit will flash 4x/sec for about 3 seconds indicating the board is in boot loader mode. After that it will start the application. If no application is present it will revert to the boot loader, so the light will just keep flashing.

You can also manually enter the bootloader from the application:

* Issuing a boot command: $boot=1 or {"boot":1} will enter the boot loader and cause it to remain in the boot loader for 60 seconds before reverting to the application.


If the LED doesn't flash you don't have the bootloader. See [Flashing the Boot Loader onto the Xmega Chip](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-flashing) if you want do do this yourself. You can also return your board to us and we'll do it. Contact us if you want us to put the bootloader onto your board.

<a id="updating"></a>
# Updating TinyG Firmware using the Boot Loader
To update the TinyG firmware run avrdude from a directory that has the tinyg.hex file you want to load.<br>

**Step1**. Navigate to the directory that has the tinyg.hex file you want.

**Step2**. Find your serial port. You will need to enter the USB port you are actually using. To find your serial port in Mac/Linux you can run `ls /dev` and look for the tty.usbserial-XXXXXXX port<br>

**Step3**. Enter the boot loader and flash the chip using Avrdude.  Use the Avrdude distributed with the Arduino - it's pretty up to date. You can enter the bootloader any of the following ways:
* Hit reset on the board
* Send a `^x` (control X) to the board (software reset)
* Send an `ESC` to the board
* Send the command `$boot=1`
* Send the JSON command `{"boot":1}
 
Next you need to enter the avrdude command before the LED stops blinking. It currently blinks 10 times, or about 3 seconds.

Here's an example command line from Windows:<br>
`avrdude -p x192a3 -c avr109 -b 115200 -P COM19 -U flash:w:tinyg.hex`

Here's an example command line from Mac:<br>
`avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg.hex`

You should see the following dialog if the loader works correctly
<pre>
macintosh-3:default username$ avrdude -p x192a3 -c avr109 -b 115200 -P /dev/tty.usbserial-AE01DWZS -U flash:w:tinyg.hex

Connecting to programmer: .
Found programmer: Id = "XBoot++"; type = S
    Software Version = 1.7; No Hardware Version given.
Programmer supports auto addr increment.
Programmer supports buffered memory access with buffersize=512 bytes.

Programmer supports the following devices:
    Device code: 0x7b

avrdude: AVR device initialized and ready to accept instructions

Reading | ################################################## | 100% 0.00s

avrdude: Device signature = 0x1e9744
avrdude: NOTE: FLASH memory has been specified, an erase cycle will be performed
         To disable this feature, specify the -D option.
avrdude: erasing chip
avrdude: reading input file "tinyg.hex"
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: writing flash (96706 bytes):

Writing | ################################################## | 100% 12.09s



avrdude: 96706 bytes of flash written
avrdude: verifying flash memory against tinyg.hex:
avrdude: load data flash data from input file tinyg.hex:
avrdude: input file tinyg.hex auto detected as Intel Hex
avrdude: input file tinyg.hex contains 96706 bytes
avrdude: reading on-chip flash data:

Reading | ################################################## | 100% 11.51s



avrdude: verifying ...
avrdude: 96706 bytes of flash verified

avrdude done.  Thank you.

macintosh-3:default username$ 
</pre>

<a id="flashing"></a>
# Flashing the Boot Loader onto the Xmega Chip
_Update 5/22/13: We have had reports that programming (flashing) the boot loader using AtmelStudio6.0 or Studio6.1 can report a verification error. The boot loader is actually loaded correctly and does work, in spite of this error (so far no counter examples have been reported). Flashing using AVRStudio4 does not report these errors._

The following instructions are how to flash the boot loader using Atmel Studio6. AVRStudio4 is similar, as would be command-line operation. These instructions use the xboot.hex file already in the project.  You can get the xboot.hex file here.  [xboot.hex] (https://raw.github.com/synthetos/TinyG/master/xboot/xboot/xboot.hex)  **NOTE: You need to right click and save as on the xboot.hex link to download the hex file correctly.** The hex should be all you need to get the bootloader onto the chip, but if you want to compile the bootloader yourself go to the next section: [Compiling the Boot Loader for TinyG](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#compiling-the-boot-loader-for-tinyg)

**Step 1**. Get the right programmer. The xmega requires PDI programming. Use the Atmel [AVRISP mkii](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=%2fha2pyFaduiLEF45YHzXlzYdfQlCIaNgRBHMmCoiTxs%3d) or some other programmer that supports PDI programming. Plug the programmer into TinyG and apply power to TinyG.

**Step 2**. Bring up Studio6 and the device programming panel. Look under `Tools / Device Programming`
* In the programming panel verify Tool is AVRISP mkii, the device is ATxmega192A3 and the interface is PDI. Hit `Apply`, then `Read`. You should see the Device signature and voltage field populate. Voltage should be 3.2v or thereabouts.

**Step 3**. Set the fuses. Go to Fuses and set according to [here](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer#fuses). Hit `Program` to program the fuses.

**Step 4**. Go to `Memories`. Select xboot.hex in the `Flash` section. Do not use xboot-boot.hex as it's org'ed in the wrong place (0 instead of 0x30000). Check the `Erase Flash Before programming` box or it won't verify. Hit `Program`.

**Step 5**. Do this step if you also want to program TinyG onto the chip. Select tinyg.hex in the `Flash` section. Uncheck the `Erase Flash Before programming` box. Hit `Program`.


If this all worked you will see the Spindle Direction light flash for about 3 seconds then TinyG will deliver its startup messages.

<a id="projectsetup"></a>
# Compiling the Boot Loader for TinyG
Use these instructions if you want to change the xboot.hex file. If all you want to do is flash it onto TinyG see the previous section.

The boot loader is Alex Forencich's xboot which can be found in the xboot directory in the main TinyG git tree. This xboot has the settings and modifications for use on TinyG. 

## Getting Xboot to work from the native Makefile
Bring up a cmd window and navigate to the working directory. Run the command:
<pre>
make conf/x192a3.conf.mk
</pre>

Step 1. The command line you need is:<br>
`make conf\x192a3.conf.mk`

You should see something like this:
<pre>
Z:\Alden\Projects\proj38_TinyG\TinyG\xboot\xboot>make conf/x192a3.conf.mk
cp conf/x192a3.conf.mk config.mk
make
make[1]: Entering directory `Z:/Alden/Projects/proj38_TinyG/TinyG/xboot/xboot'

-------- begin --------
avr-gcc (AVR_8_bit_GNU_Toolchain_3.3.0_364) 4.5.1
Copyright (C) 2010 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.


Size before:
xboot.elf  :
section       size       addr
.text       0x1066    0x30000
.bss         0x206   0x802000
.stab       0x5b68        0x0
.stabstr   0x17403        0x0
Total      0x1e1d7



Generating config.h for atxmega192a3

Compiling: xboot.c
avr-gcc -c -mmcu=atxmega192a3 -I. -gstabs   -Os -funsigned-char -funsigned-bitfi
elds -fpack-struct -fshort-enums -ffunction-sections -fdata-sections -fno-jump-t
ables -Wall -Wa,-adhlns=xboot.lst  -Wstrict-prototypes -std=gnu99 -MD -MP -MF .d
ep/xboot.o.d -DF_CPU=32000000L -DUSE_CONFIG_H xboot.c -o xboot.o

Compiling: uart.c
avr-gcc -c -mmcu=atxmega192a3 -I. -gstabs   -Os -funsigned-char -funsigned-bitfi
elds -fpack-struct -fshort-enums -ffunction-sections -fdata-sections -fno-jump-t
ables -Wall -Wa,-adhlns=uart.lst  -Wstrict-prototypes -std=gnu99 -MD -MP -MF .de
p/uart.o.d -DF_CPU=32000000L -DUSE_CONFIG_H uart.c -o uart.o

Compiling: api.c
avr-gcc -c -mmcu=atxmega192a3 -I. -gstabs   -Os -funsigned-char -funsigned-bitfi
elds -fpack-struct -fshort-enums -ffunction-sections -fdata-sections -fno-jump-t
ables -Wall -Wa,-adhlns=api.lst  -Wstrict-prototypes -std=gnu99 -MD -MP -MF .dep
/api.o.d -DF_CPU=32000000L -DUSE_CONFIG_H api.c -o api.o

Linking: xboot.elf
avr-gcc -mmcu=atxmega192a3 -I. -gstabs   -Os -funsigned-char -funsigned-bitfield
s -fpack-struct -fshort-enums -ffunction-sections -fdata-sections -fno-jump-tabl
es -Wall -Wa,-adhlns=config.lst  -Wstrict-prototypes -std=gnu99 -MD -MP -MF .dep
/xboot.elf.d -DF_CPU=32000000L -DUSE_CONFIG_H xboot.o flash.o eeprom_driver.o ua
rt.o i2c.o fifo.o watchdog.o api.o sp_driver.o --output xboot.elf -Wl,-Map=xboot
.map,--cref -Wl,--gc-sections    -lm -Wl,--section-start=.text=0x030000

Creating load file for Flash: xboot.hex
avr-objcopy -O ihex -R .eeprom xboot.elf xboot.hex

Creating load file for boot section: xboot-boot.hex
avr-objcopy -O ihex --change-addresses -0x030000 xboot.hex xboot-boot.hex

Creating load file for EEPROM: xboot.eep
avr-objcopy -j .eeprom --set-section-flags=.eeprom="alloc,load" \
        --change-section-lma .eeprom=0 -O ihex xboot.elf xboot.eep
c:\Program Files\Atmel\AVR Tools\AVR Toolchain\bin\avr-objcopy.exe: --change-sec
tion-lma .eeprom=0x00000000 never used

Creating Extended Listing: xboot.lss
avr-objdump -h -S xboot.elf > xboot.lss

Creating Symbol Table: xboot.sym
avr-nm -n xboot.elf > xboot.sym

Size after:
xboot.elf  :
section       size       addr
.text       0x1066    0x30000
.bss         0x206   0x802000
.stab       0x5b68        0x0
.stabstr   0x17403        0x0
Total      0x1e1d7

Errors: none
-------- end --------

make[1]: Leaving directory `Z:/Alden/Projects/proj38_TinyG/TinyG/xboot/xboot'

Z:\Alden\Projects\proj38_TinyG\TinyG\xboot\xboot>
</pre>

But beware - it only works in windows. Use cmd and navigate to the (lower) xboot directory something like:
`Z:\Username\Projects\...\TinyG\xboot\xboot\make conf\x192a3.conf.mk`

It enters an infinite loop in Linux and generates this error in OSX

<pre>
macintosh-3:xboot your-user-dir$ make conf\x192a3.conf.mk
.dep/fifo.o.d:1: *** multiple target patterns.  Stop.
macintosh-3:xboot your-user-dir$
</pre>

You should now be ready to flash xboot.hex onto the xmega192. Refer to the Flashing... section, above.

## Setting up Xboot as an AVRStudio4 Project
Xboot has been set up as an AVRStudio4 project (Windows only). use the xboot.aps project file. 

You can either use the native makefile or have AS4 auto generate a makefile. The auto-generated option allows you to use the AVR simulator to debug - otherwise there is no functional difference.

Be aware that in order to generate the config.h file you must change x192a3.conf.mk and make it with the native makefile. The AVRStudio4 project does not run the config.h target.

### Native Makefile
To use the native makefile check `Use External Makefile` in `Project / Configuration Options`.

Running the external make from AS4 does not create the config.h file. Manually run `make conf/x192a3.conf.mk` from a windows command line. You will have to do this initially and each time you change x192a3.conf.mk.

You want to use the xboot.hex in the `xboot` working directory.
 
### Auto-generated Makefile
To use the auto-generated makefile do the following:

1. Generate config.h by running `make conf/x192a3.conf.mk` from a windows command line (as above). You will have to do this initially and each time you change x192a3.conf.mk.
2. Setup the xboot project configuration in `Project \ Configuration Options`
<pre>
in the General section:
uncheck `Use External Makefile`
frequency = 32000000   (32 Mhz)
optimization = -Os
</pre>
<pre>
add the following to Custom Options, [All files]:
-DUSE_CONFIG_H
-ffunction-sections
-fdata-sections
</pre>
<pre>
add the following to Custom Options, [Linker Options]:
-Wl,--section-start=.text=0x030000
-Wl,-Map=xboot.map,--cref
-Wl,--gc-sections
</pre>

You will want to use the xboot.hex file found in the `default` directory

## Setting up Xboot as an AtmelStudio6 Project
_Note: this isn't working and we have abandoned it. Xboot seems to only compile under the AVRGCC 4.3.3 (WinAVR 20100110). Studio 6 runs the AVR_8_bit_GNU_Toolchain_3.4.0_663 (4.6.2). For some reason code generated by the the newer compiler doesn't function - it fails during EEPROM write. Evidently this is a known bug - but it's not our bug, so time to move on. These are not the droids we are looking for._

But if you want to try this do the following:

The project files are the xboot.atsln and xboot.cproj files in the github TinyG/support dir. Move these to your project directory then click on xboot.atsln to start the project up. 

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
F_CPU=32000000L
USE_CONFIG_H
</pre>
<pre>
in Optimization:
-Os
</pre>
<pre>
in Miscellaneous
-I. -gdwarf-2 -std=gnu99 -ffunction-sections -fdata-sections -fno-jump-tables -Wa,-adhlns=flash.lst -Wstrict-prototypes
-- alternately --
-I. -gdwarf-2 -std=gnu99 -ffunction-sections -fdata-sections -fno-jump-tables -Wa,-adhlns=flash.lst -Wstrict-prototypes
</pre>
<pre>
in Linker/Memory Settings:
.text=0x18000    (yes, it's 0x30000 divided by 2)
</pre>

Still to do:
* Find a way to pre-process the config.h file