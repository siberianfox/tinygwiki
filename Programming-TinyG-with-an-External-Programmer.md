TinyG can be programmed over the USB serial port with the boot loader or it can be programmed on the ISP header using either Atmel's Atmel-ICE or AVRISP MKii programmer. The location of the various versions of firmware [is discussed here.](https://github.com/synthetos/TinyG/wiki/firmware) Your garden variety AVR programmers will not work, as they only support the AVR ISP protocol and not the PDI programming protocol required by the Xmega. So Atmel calling this is the AVRISP mkii programmer is a bit confusing. Here's a link for the [Atmel AVRISP mkii Programmer](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=sGAEpiMZZMv256HIxPBQcA8%252bsNH3cLLR)

There are (at least) 3 ways to program the board using the AVRISP2:
* Programming using [AVR Studio4](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer#avr-studio-4)
* Programming using [Atmel Studio 6.1](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-an-External-Programmer#atmel-studio-61)
* Programming using [AVRDUDE with AVRISP2](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-an-External-Programmer#avrdude-with-avrisp2)

Also, regardless of method you want to make sure to get the [xmega fuses](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer#fuses) right

### AVR Studio 4
* First connect the programmer to the 6 pin programming header on the TinyG. Observe polarity - the red wire should be next to the white dot on the board. Plug in the programmer's USB connector to your computer running Studio 4.
* Apply power to the TinyG. The board will not program unless it's powered up. The board's USB does not need to be connected
* Bring up Studio 4. If you are in a virtual machine make sure the Atmel AVRISP mkII device is connected to the virtual machine.
* If you get a dialog about upgrading the firmware on the programmer at any point, do it. You may need to re-plug afterwards to re-establish connection.
* From the drop-down menus select Tools / Program AVR. Choose Auto Connect or Connect. The programmer dialog box should appear after connection.
* The header line should read "AVRISP mkII in PDI mode with ATxmega192A3" (or ATxmega192A3U). If it does not, go to the Main tab and select the device as ATxmega192A3.
* Next go to the Fuses tab and program them according to [here](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer#fuses). If you are not installing the boot loader select BOOTRST to be "Application Reset". If you are installing the bootloader select "Boot Loader Reset".
* Next go to the Program tab and program the flash. Setting are:
 * Erase device before programming (checked)
 * Verify device after programming (checked)
 * Input HEX file - select the tinyg.hex you want. Studio 4 writes its hex file into the /default directory
* Hit the Program button. You should see progress bars for programming and verification and a bunch of messages in the message window ending with "Leaving programming mode.. OK!"
* If not, check the following
 * TinyG blue power light is on
 * green light in on in the programmer / USB connection is made to host
 * device is selected ATxmega192A3
 * hex file is found and is not corrupt
 * AVRISP2 firmware is up-to-date


### Atmel Studio 6.1
These instructions use Atmel Studio from versions 6.1 and up.  Programming the TinyG from Atmel Studio can be used to flash a bootloader or the actual TinyG hex firmware file.  To explain this better, what this means is you cannot use Atmel Studio to flash the bootloader + tinyg firmware onto your TinyG.  Why do you care?  The issue with this is, if you flash a tinyg.hex file directly to your tinyg (not flashing a bootloader) you will not be able to update your TinyG over the serial port (or USB) which is the normal method of updating TinyG.  This method is not recommended for beginners and is normally reserved for developers experimenting with custom builds and debugging of tinyg firmwares.<br>

* First connect the programmer to the 6 pin programming header on the TinyG. Observe polarity - the red wire should be next to the white dot on the board. Plug in the programmer's USB connector to your computer running Atmel Studio

![](https://farm2.staticflickr.com/1639/23738825984_bfb8618444_k.jpg)
* Apply power to the TinyG. The board will not program unless it's powered up. The board's USB does not need to be connected. If the board already has a bootloader wait for the boot loader to stop flashing the spindle direction light. You can also hit the reset button and wait until it stops flashing.
* Bring up Studio.  If you are in a virtual machine make sure the Atmel AVRISP mkII device is connected to the virtual machine.
* If you get a dialog about upgrading the firmware on the programmer at any point, do it. You may need to re-plug afterwards to re-establish connection.   
* From the menu bar select Tools / Device Programming.
![](https://farm2.staticflickr.com/1658/24284586251_9ae99a9876_b.jpg)
* Select Tool to be AVRISP mkII and hit Apply. Device should read ATxmega192A3, Interface should read PDI, and a left navigation bar should appear giving you some options. If the Device is not ATxmega192A3 use the drop down from that field to select ATxmega192A3.
 * If nothing shows up in the Device Signature and Target Voltages boxes click the Read buttons next to each.
* Next go to the Fuses tab and program them according to [here](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer#fuses). If you are not installing the boot loader select BOOTRST to be "Application Reset". If you are installing the bootloader select "Boot Loader Reset".
 * Select Lockbits and make sure they are set up as listed.
* Next go to the Memories tab and program the flash in the Flash box. Setting are:
 * Erase Flash before programming (checked)
 * Verify Flash after programming (checked)
 * Input HEX file - select the tinyg.hex you want. Studio 6 writes its hex file into the /Debug directory. You can also program from the tinyg.elf file. (What does a TinyG Elf look like?)
* Hit the Program button. You should see progress bars for programming and verification and a bunch of messages in the message window ending with "Verifying Flash... OK!"
 * We have seen that Studio 6 doesn't always program the device correctly and gives a verification error to the effect that some byte reads $FF instead of some value. We are not sure why this is and people have reported that the flash programmed correctly even though they saw this error. If this happens try programming again. Regardless of the result, see if the board runs properly. It should.
* If none of this works check the following
 * TinyG blue power light is on
 * green light in on in the programmer / USB connection is made to host
 * device is selected ATxmega192A3
 * hex file is found and is not corrupt
 * AVRISP2 firmware is up-to-date


### AVRDUDE with AVRISP2

## Fuses
Select the Fuses from the programmer dialog and hit PROGRAM when you are done. You want the following settings. Studio 4 and Studio 6.1 values are the same, they just call them slightly different things.

Fuse | Studio 4 Value | Studio 6.1 Value | Notes
-----|-------|------|--------
JTAGUSERID | 0xFF |  0xFF | this is the default setting
WDWP | 8 cycles | 8CLK | default value
WDP | 8 cycles | 8CLK | default value
DVSDON | (unchecked) | (unchecked) | default
BOOTRST | Boot Loader Reset | BOOTLDR | if you have not installed the boot loader use Application Reset
BODPD | BOD enabled continuously | CONTINUOUSLY |
RSTDISBL | (unchecked) | (unchecked) | default
SUT | 0 ms | 0 ms | default
WDLOCK | (unchecked) | (unchecked) | default
JTAGEN | (CHECKED) | (CHECKED) | default - never uncheck this or you will brick the board
BODACT | BOD enabled continuously | CONTINUOUSLY |
EESAVE | (unchecked) | (unchecked) | default
BODLVL | 2.6v | 2V6 |

The fusebytes should reflect the above settings and should read:

Fuse | Value | Notes
-----|-------|------
FUSEBYTE0 | 0xFF |
FUSEBYTE1 | 0x00 |
FUSEBYTE2 | 0xBE | will be FE if Application Reset is selected
FUSEBYTE4 | 0xFE | there is no third fusebyte, and nobody expects the Spanish Inquisition
FUSEBYTE5 | 0xEB |
