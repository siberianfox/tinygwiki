## One-time BBB setup:

**First: update the BBB**

    ssh root@beaglebone.local

    # You may have to fix your local ~/.ssh/known_hosts if you had another beaglebone connected.
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

**This should work, but doesn't:**

    opkg update
    opkg upgrade --force-overwrite

Instead, you have to download the latest image from http://beagleboard.org/latest-images (2013-6-20 right now) and then follow the instruction on updating the BBB from [Adafruit]. You wan the eMMC Flasher image.(http://learn.adafruit.com/beaglebone-black-installing-operating-systems/mac-os-x) or the [official instructions](http://beagleboard.org/Getting%20Started#update). (I prefer the Adafruit ones, personally. -Rob)

Note that you'll need a 4GB microSD card.

**This will take a long time.**

## TinyG-Specific stuff:

    opkg update
    opkg install python-compiler
    opkg install kernel-module-ftdi-sio 

### To prepare for git:

_From your computer:_

    scp ~/.ssh/id_rsa* root@beaglebone.local:.ssh

_On the bone:_

    mkdir .ssh 
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

    ./node_modules/serialport/bin/serialportList.js
    # This should always return /dev/ttyUSB0
 
Now navigate to http://beaglebone.local:3000/ in order to control the tester and see results.