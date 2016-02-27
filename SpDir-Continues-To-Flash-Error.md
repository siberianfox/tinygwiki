In 2015 a batch of TinyG v8's shipped from the factory with incorrect `LOCKBITS` set.  To fix this issue you may contact Synthetos and we will fix your board and ship it back to you free of cost.  However, if you want to fix this error yourself then proceed below.

##DIY SpDir LOCKBIT's Fix
To fix this you will need:
* A windows machine (vm or bare metal)
* An AVRISP MKII or compatible programmer that support Atmel Studio and PDI programming mode.

## Instructions
1. You can get the xboot.hex file here.  [xboot.hex (https://raw.github.com/synthetos/TinyG/master/xboot/xboot/xboot.hex)  **NOTE: You need to right click and save as on the xboot.hex link to download the hex file correctly.**
2. Launch Atmel Studio and press CTRL-SHIFT-P (or click on the chip with a lightning bolt on it from the menu).
You should see a screen like this:
![](https://farm2.staticflickr.com/1697/23739145324_44027a6a15_b.jpg)
3.  Select your programmer (assuming its plugged in correctly to your tinyg and your tinyg is powered up, the raised part  on one side of the connector matches up with the gap in the white outline around the target on the tinyg), select the Atxmega192A3 as the chip and then click apply and then read.  If all went well you should see your device signature populate.
4. Next click on the `Lock Bits` menu on the left (only visible after a successful step 3).  It will look like this:
![](https://www.flickr.com/photos/rileyporter/24071049480/in/dateposted-public/)
Notice how they do not all say `NOLOCK`.  They need to ALL say `NOLOCK`.
5. Change all LOCKS to say `NOLOCK` from the dropdown menus.  
6. Then click the program button below once they all are set to `NOLOCK`.
***NOTE*** Sometimes this works sometimes it does not.  I have not figured out why it works only sometimes.  Not to worry move on to the section below if it fails.  If it works then you should be good to go and ready to update your tinyg firmware like normal.


## Failed To Set Lock Bits

If the above method failed its time to just erase the chip.
1. Click on the `Memories` tab on the left.  Then click on the erase chip like this:
![](https://farm2.staticflickr.com/1558/23739809703_a028949ca6_b.jpg)
2. Now go back to the `LOCKBITS` tab and verify that they are all set to `NOLOCK`. Like this:
![](https://farm2.staticflickr.com/1643/23738449894_f7a6a6043f_b.jpg)

## Re-flashing TinyG & the Bootloader
If you were able to reset the lockbit without erasing the board you are pretty much done.  However if you had to erase the board we now have no bootloader so follow the steps below to reflash the bootloader AND the new TinyG firmware.
<br>

1. Follow the steps [here](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#flashing-the-boot-loader-onto-the-xmega-chip) to load your bootloader onto your TinyGv7-8.
**But come back here to finalize loading the TinyG firmware**
2. Now we need to load a tinyg firmware onto your board via the newly flashed bootloader you loaded in step 1. Remove the AVRISP programmer as you not need it to flash your board.  Follow the instructions [here](https://github.com/synthetos/TinyG/wiki/TinyG-TG-Updater-App).