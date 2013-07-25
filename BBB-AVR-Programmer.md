Consider these build notes:

var redPin = "P8_12";
var greenPin = "P8_11";
var inputPin = "P8_14";

Wire the Red LED from P8-12 to GND (pins P8 1 and 2 are GND).
Wire the Green LED from P8-11 to GND.
Wire the button between GND and P8-14, with a pullup resistor to 3v3 (pins P8 3 and 4 are 3v3.)

## Making AVRDude

_Please use the precompiled binary in Dropbox. There are the instructions needed to build it:_

Notes: https://github.com/todbot/ArduinoOnBeagleBone

Prerequisites:

    opkg install libftdi-dev
    #opkg install libusb-1.0-dev

Download and install:

    wget http://download.savannah.gnu.org/releases/avrdude/avrdude-5.11.1.tar.gz
    tar xavf avrdude-5.11.1.tar.gz
    cd avrdude-5.11.1
    ./configure

To get bison working (maybe):

    mkdir -p /build/v2012.12/build/tmp-angstrom_v2012_12-eglibc/sysroots/x86_64-linux/usr/bin/
    ln -s /usr/bin/m4 /build/v2012.12/build/tmp-angstrom_v2012_12-eglibc/sysroots/x86_64-linux/usr/bin/m4

Then you can make:

    make