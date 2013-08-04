Consider these build notes:

### Circuit

    var redPin = "P8_12";
    var greenPin = "P8_11";
    var inputPin = "P8_14";

Wire the Red LED from P8-12 to GND (pins P8 1 and 2 are GND).

Wire the Green LED from P8-11 to GND.

Wire the button between GND and P8-14, with a pullup resistor to 3v3 (pins P8 3 and 4 are 3v3.)


### Avrdude

Run these command in a terminal _from your Mac_ (Note that this assumed Dropbox is running and synced):

    scp ~/Dropbox/Synthetos/avrdude-5.11.1-BBB.tgz root@beaglebone.local:
    scp ~/Dropbox/Synthetos/bbb-programmer.tgz root@beaglebone.local:
    
    ssh root@beaglebone.local

_Note: bbb-programmer.tgz has a altered copy of `/usr/lib/node_modules/bonescript/index.js` in it. If bonescript is updated, that may cause problems._

Now you should be connected to the beaglebone, the rest of the commands in that terminal will go to the BBB:

    cd /
    tar xzvf ~/avrdude-5.11.1-BBB.tgz
    tar xzvf ~/bbb-programmer.tgz
    opkg update
    opkg install kernel-module-ftdi-sio
    opkg install libftdi-dev

Now you should be able to go to [Cloud9](http://beaglebone.local:3000/), open `programmer.js` and run it.

Once you've verified proper operation, to deploy the BB as a programmer, move the `programmer.js` script into the `autorun` folder. You can do that with Cloud9 or in the terminal with this command:

    mv /var/lib/cloud9/{programmer.js,autorun/}

###Changing the hostname

Run this command on the BBB comand line (sss'd in) to change the hostname to `bbb-prog`:

    perl -pi -e 's/beaglebone2/bbb-prog/' /etc/host{name,s}



## Making AVRDude

_Please use the precompiled binary in Dropbox. There are the instructions needed to build it._

**To be clear, if you use the binary from Dropbox, you do not need to follow these instructions.**

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