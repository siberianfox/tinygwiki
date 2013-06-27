### Summary:
If you want a no frill easy to setup and send files quickly sort of solution then CoolTerm is a good choice.<br>If you followed the instructions in the connecting and tuning sections of the wiki then you already have it install too.
### Pros:
* Very easy to start sending files quickly.
* Cross platform.

### Cons:
* [Asynchronous Commands](https://github.com/synthetos/TinyG/wiki/JSON-Flow-Control-Specification#async-commands) are not available during a file send. 
* No GUI Preview.

Summary:
##

1. Make sure you have CoolTerm installed and you are able to connect to TinyG.  See [Connecting to TinyG](https://github.com/synthetos/TinyG/wiki/Connecting-TinyG#establish-usb-connection) for complete instructions on how connected to TinyG with CoolTerm.<br>
2. If you are now connected to TinyG with CoolTerm, disconnect.  We need to change one value in the <code>Options</code> of Coolterm **BEFORE** we connect to TinyG.  Now that you have clicked the options button you should see something like this:<p>
![CoolTerm Connect](http://farm6.staticflickr.com/5550/9149058282_4f6b3abb41_z.jpg)
<p>Notice that the <code>XON</code> is checked.  **This is VERY IMPORTANT.  Please make sure CoolTerm has <code>XON</code> checked!**<p>

3. Once you are done checking XON go ahead and click <code>OK</code>.  Now click the <code>Connect</code> button.  As long as you have the right port selected and the right baud rate set, you should now be connected to TinyG.<br>
4.  Technically, we could send a file to TinyG now.  However, we need to make sure <code>XON</code> is enabled on TinyG.  It IS enabled by default, but it is always good to make sure before sending files that will depend on <code>XON</code>.  To check this you will need to click in the text input box in CoolTerm.  It is at the very bottom of CoolTerm.  Once you are able to type into the input box issue a XON query by sending: <br><code>$ex</code> then hit enter.  You just issued getter command in TinyG for the XON enabled property.  If <code>XON</code> is enabled, you should see something like this:<br>
$ex 1 - Paste this in.
5.  Now you are ready to send a gcode file.  Just to re-cap... <br>
You....
1. Checked to make sure CoolTerm was connected with <code>XON</code> checked.
2. Checked to make sure TinyG has <code>XON</code> enabled on the hardware.
<br>
Now to send a file (assuming your tool is at the desired X,Y,Z coordinates) click on the <code>Connection</code> menu and then select the "Send Text File".  From here you will be asked to select a text file. <p>WARNING when you click the selected file and hit open you file is being send to TinyG instantly!<p>Selected you gcode file (gcode files are just text files with different file extensions).
