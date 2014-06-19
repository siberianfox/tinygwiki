### A Browser based TinyG UI
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
The first thing you need to get chilipeppr working with TinyG is a **Serial Port Json Proxy** or AKA [SPJS](http://github.com/johnlauer/serial-port-json-server).  This is just a fancy way of saying you need a program running on your local machine that translates serial communications with TinyG to the websocket format.  You can also use [SPJS](http://github.com/johnlauer/serial-port-json-server) over the network, however we will cover that in the [Advanced Chilipeppr Usage](https://github.com/synthetos/TinyG/wiki/chilipeppr-advanced-usage) of this document.  Currently, there are Windows, OSX, Raspberrypi and BBB versions of this agent available for download here.  Once you have downloaded and ran the serial->websocket bridge agent we are ready to move on.

## Loading chilipeppr
Go ahead and load up http://chilipeppr.com/tinyg in your browser.  This will bring up the official TinyG workspace for the chilipeppr interface.