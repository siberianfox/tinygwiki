Consider these build notes:

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