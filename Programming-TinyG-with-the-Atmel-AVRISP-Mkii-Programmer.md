TinyG can be programmed over the USB serial port with the boot loader, or it can be programmed on the ISP header using the Atmel AVRISP mkii programmer. Your garden variety AVR programmers will not work, as they only support the AVR ISP protocol and not the PDI programming protocol required by the Xmega. So Atmel calling this is the AVRISP mkii programmer is a bit confusing. Here's a link for the [Atmel AVRISP mkii Programmer](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=sGAEpiMZZMv256HIxPBQcA8%252bsNH3cLLR)

_for now this page is missing a lot of stuff, but I wanted a place to list the recommended xmega fuse settings_

## Fuses 
Select the Fuses from the programmer dialog. You want the following settings

	Fuse | Value | Notes
	-----|-------|------
	JTAGUSERID | 0xFF | this is the default setting
	WDWP | 8 cycles | default
	WDP | 8 cycles | default
	DVSDON | <unchecked> | default
	BOOTRST | Boot Loader Reset | if you have not installed the boot loader use Application Reset 
	BODPD | BOD enabled continuously | 
	RSTDISBL | <unchecked> | default
	SUT | 0 ms | default
	WDLOCK | <unchecked> | default
	JTAGEN | <CHECKED> | default
	BODACT | BOD enabled continuously | 
	JTAGEN | <unchecked> | default
	BODLVL | 2.6v | 

The fusebytes are left in their default settings and should read:

	Fuse | Value | Notes
	-----|-------|------
	FUSEBYTE0 | 0xFF |
	FUSEBYTE1 | 0x00 |
	FUSEBYTE2 | 0xBE | there is no third thing
	FUSEBYTE4 | 0xFE |
	FUSEBYTE5 | 0xEB |