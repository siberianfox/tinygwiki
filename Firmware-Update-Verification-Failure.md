The cause of the `Firmware Update Verification Failure` is that the processors LOCKBITS were set.  These need to be reset to `NOLOCK` status.

##Things You Will Need
1. AVRstudio 6.1 or later.
2. [AVRISP mkII Programmer](http://www.mouser.com/ProductDetail/Atmel/ATAVRISP2/?qs=%2fha2pyFaduiIjcuCL2xhGrtJ51CCcGyXEp%252bMX%2fCQhFCtUWPqBN19Gg%3d%3d) (NOT A CLONE OR A KNOCKOFF)
3. Windows Machine

## Procedure
1. Launch AVRStudio
2. Go to the `Tools` menu then the `Device Programming`
3. Now for the `Tool` select `AVRISP mkII` from the drop down menu.
4. Then select `ATxmega192A3` from the Device drop down menu.
5. Click `Apply`
6. Now select the `Lock bits` from the vertical list on the left.
7. Set ALL Values to `NOLOCK`. (See image below to see how this should look with the correct `LOCKBITS` set.)
8. Click `Program`

![](http://farm9.staticflickr.com/8266/8687779370_e1fd952d09_z.jpg)

##Conclusion
With your `LOCKBITS` correctly set you should be able to make it through the whole [firmware update process](https://github.com/synthetos/TinyG/wiki/TinyG-Boot-Loader#wiki-flashing).