Sending Gcode Files
===

So once your machine has been [configured](https://github.com/synthetos/TinyG/wiki/TinyG-Configuration), [setup / tuned] (https://github.com/synthetos/TinyG/wiki/TinyG-Tuning) and you have read over the [basics](https://github.com/synthetos/TinyG/wiki#tinyg-basic-pages) you are ready to start sending Gcode files to your TinyG!

File Sender Options
====

Currently there are 4 ways to send gcode files to TinyG.  
1.  **Command Line:** By using Coolterm (the same program you used to configure and tune tinyg at the command line)<br>
2.  **GUI:** tgFX - A GUI controller written in JavaFX2 and supported by Synthetos (us).<br>
3. Write your own file sender.  

##Sending Files via CoolTerm
### Pros:
* Very easy to start sending files quickly.
* Cross platform.

### Cons:
* [Asynchronous Commands](https://github.com/synthetos/TinyG/wiki/JSON-Flow-Control-Specification#async-commands) are not available during a file send. 
* Cross platform.


Using coolterm to send your gcode files is very easy.  However, it does come with certain limitations.  When using CoolTerm to send files, all FEED HOLD, CYCLE STOP and RESET abilities that TinyG support are not accessible. 