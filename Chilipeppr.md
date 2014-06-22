### A Browser based TinyG UI
Chilipeppr represents a modern way to do CNC software control for the [TinyG](http://synthetos.myshopify.com/products/tinyg) motion controller.

[chilipeppr.com/tinyg](http://chilipeppr.com/tinyg)
![](http://www.chilipeppr.com/img/screenshot-tinyg.png)
## Features
* 3D Viewer
* Gcode Sender
* TinyG internal stats viewer including planner buffer
* Flow control via planner buffer
* Serial port JSON server that runs on your Windows, Mac, Linux, or Raspberry PI
* Fork any widget or entire workspace to modify/add your own via JSFiddle

All open source. Have fun.

## Getting Started
The first thing you need to get chilipeppr working with TinyG is a **Serial Port JSON Server** or AKA [SPJS](http://github.com/johnlauer/serial-port-json-server).  This is just a fancy way of saying you need a program running on your local machine that translates serial communications with TinyG to the websocket format.  You can also use [SPJS](http://github.com/johnlauer/serial-port-json-server) over the network, however we will cover that in the [Advanced Chilipeppr Usage](https://github.com/synthetos/TinyG/wiki/chilipeppr-advanced-usage) of this document.  Currently, there are Windows, OSX, Raspberrypi and BBB versions of this agent available for download here.  Once you have downloaded and ran [SPJS](http://github.com/johnlauer/serial-port-json-server) we are ready to move on.

## Loading chilipeppr
Go ahead and load up http://chilipeppr.com/tinyg in your browser.  This will bring up the official TinyG workspace for the chilipeppr interface.

##Sending Files
Video walkthrough of the ChiliPeppr Hardware Fiddle - TinyG Workspace.
[![Screenshot](http://chilipeppr.com/img/vidwalkthrough.png)](http://youtu.be/mKLdgpz8gpQ)

##Extending Chilipeppr
To DO

##Forking Chilipeppr
* Step 1. Pick a widget that you want to modify. Pick "Fork" from the upper right menu of the widget. Save your widget in JSFiddle and note the new URL.
* Step 2. Fork the workspace by choosing "Fork" from upper right menu of the workspace title area. Modify it to point to your new forked widget from Step 1. You'll have to look around inside the code and find where the chilipeppr.load() methods are and change the URL from the old widget to the new one.
* Step 3. Fork the boot Javascript by choosing "Fork Boot Javascript" from upper-most right menu in ChiliPeppr (just to the right of your login name). You must be logged in to do this step. Pick a new URL for your workspace. Modify the sample boot Javascript to point to your workspace that you created in Step 2.
* Done. You should now be able to view your newly forked workspace.

##Offline Chilipeppr
To DO
