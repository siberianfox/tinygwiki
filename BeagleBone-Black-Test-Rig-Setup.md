[BeagleBone Black Test Rig Operation](https://github.com/synthetos/TinyG/wiki/BeagleBone-Black-Test-Rig-Operation)
## One-time BBB setup:

**First: update the BBB**

Download the latest image from http://beagleboard.org/latest-images (2013-6-20 right now) and then follow the instruction on updating the BBB from [Adafruit]. You wan the eMMC Flasher image.(http://learn.adafruit.com/beaglebone-black-installing-operating-systems/mac-os-x) or the [official instructions](http://beagleboard.org/Getting%20Started#update). (I prefer the Adafruit ones, personally. -Rob)

The staps are, basically: 

1. Download image
1. Decompress image
1. Copy (with special software or commands) the decompressed image to an SD card
1. Put that SD card into a powered-down BBB
1. Push the "Boot" (S2) button on the BBB (closes to the SD card slot, but on the other side) while plugging in the BBB
1. Wait 3-40 minutes for all four LEDs to stay lit, then unplug the BBB
1. Remove the SD card
1. Plug the BBB back in

Note that you'll need a 4GB or bigger _micro_SD card.

**This will take a long time.**

Connect the BBB Ethernet port. The BBB must be connected to ethernet for most of the rest of this to work
Open a Terminal (or PuTTY) windows and type:

    ssh root@beaglebone.local
    # You may have to fix your local ~/.ssh/known_hosts if you had another beaglebone connected.
    # When asked for the beaglebone root password hit ENTER 

    # Now you're connected to the BeagleBone, these commands will run on the BBB:
    /usr/bin/ntpdate -b -s -u pool.ntp.org

    #These are simply for sanity's sake:
    echo 'EDITOR=/usr/bin/nano export EDITOR' > ~/.profile
    echo 'eval `ssh-agent -s`' > ~/.profile
    perl -pi -e 's/NTPSERVERS=""/NTPSERVERS="pool.ntp.org"/' /etc/default/ntpdate

    # Setup timezone
    rm /etc/localtime
 
    # Pick one:
    ln -s /usr/share/zoneinfo/America/New_York /etc/localtime
    ln -s /usr/share/zoneinfo/America/Chicago /etc/localtime  

## TinyG-Specific stuff:

These also go into the BBB:

    opkg update
    opkg install python-compiler
    opkg install kernel-module-ftdi-sio 

    # Ignore these warning messages:
    # WARNING: could not open /lib/modules/3.8.13/modules.order: No such file or directory
    # WARNING: could not open /lib/modules/3.8.13/modules.builtin: No such file or directory about 

    mkdir ~/.ssh 

### To prepare for git:

_From your computer (in another terminal/PuTYY window):_

    scp ~/.ssh/id_rsa* root@beaglebone.local:.ssh

_On the bone through ssh:_

    # Optional setup of ssh-agent, but it'll save time later
    # You'll need to run this every time you login
    ssh-add
    # Now type your ssh-key password.
    # If you don't know it, search in Keychain Access.app on your computer for 'ssh'.

    # If you ever want to push from git on this BBB:
    git config --global user.name 'Your Name'
    git config --global user.email 'you@gmail.com'  

    # Now to actually get the tester
    cd /var/lib/cloud9
    git clone git@github.com:giseburt/TinyG-node.git
    # It may ask for your ssh key password at this point
    
    cd TinyG-node/
    npm install
    # It may ask for your ssh key password (several times)

To get the list of tinyg's connected (for now):

    cd TinyG-node/       (if you are not already in this directory)
    ./node_modules/serialport/bin/serialportList.js
    # This should always return /dev/ttyUSB0

Now navigate to http://beaglebone.local:3000/ in order to control the tester and see results.

Move the TinyG-node.git/examples/tinygTestRig.js to the top level of the "Workspace" using drag and drop. Double-click on it, then hit run. You can now plug the TinyG into the BBB and it should run the script on the TinyG. Note that you need a recent dev build of TinyG to get the RTS/CTS working, or you will see parser errors.