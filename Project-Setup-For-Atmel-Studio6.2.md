This page provides in instructions to set up an Atmel Studio 6.2 project for TinyG. TinyG is maintained in this environment so all the project files are available. TinyG is also under development in various other environments that support AVRCCC, such as Xcode, and AVR Studio 4.

## Setting Up TinyG as an Atmel Studio 6.2 Project

### Get a Windows Environment that will Support this
We use VMware (5 or 6) running Windows 7 on Max OSX (10.8 or 10.9). Here's what we've learned about this setup
* If you can, build a dedicated VM for this application. Who knows how Windows programs interact if you have more than one in an environment. 
* Allocate 2 Gb of RAM to the W7 OS and about 40 - 60 Gb of disk. 
* Select the option to break the disk into 2Gb chunks. 
* Do not snapshot as it bloats the VM image. Make offline backups instead. 
* Make sure the VM is set to the number of cores on your machine or it may run excessively slowly. There's some kind of bug in the communications if the cores are mismatched. This may have been fixed.
* Use the C: drive only for installing the Studio6.2 and the rest of the tool chain 
* Mount the Mac filesystem as an external filesystem and use this for all your project files. See note below:
* _Note that if you are installing under VMware and have your project files in a shared volume (//vmware-host/...) you will need to map a network drive so that AS6.2 can see a windows path as opposed to a UNC path. You can tell by looking at the full filepath in the directory window to see if it starts with Z:\... or Y:\...  If not then the IDE will not be able to compile._

### Get The Studio6.2 Complete Install Kit
* Go to Atmel and sign up to download the entire Atmel Studio IDE 6.2 production install package, including the .NET part. It's about 700 Mbytes. Current build is build 1153.
* Walk through the entire installation process. You will not need the Atmel Solutions framework when asked. You will need the USB drivers when asked.

### Get a Hardware Programmer

### Clone the TinyG Github Repository 
* Run git clone to set up TinyG working directory. First you must install git. See github for instructions to do this if you haven't already.
<pre>
From a command line navigate to the directory where you want the tinyg git directory to exist, then run:
git clone git@github.com:synthetos/TinyG.git
</pre>
* You should have a directory with all the source files and the Atmel project files for both Studio4 and Studio6. Some of the project files are ignored by git so they don't cause havoc. You will need the AtmelStudio6 project files, which are `tinyg.atsln` and `tinyg.cproj`
* If you are on a mac, you may want to get Gitx. It's a GUI that makes git management much easier.

### Atmel Studio6 Bugs, Workarounds, and Other Topics
Here are some known bugs that we have had to work around

#### Can't compile under -Os optimization
This seems to be a known bug with AVRGCC 4.8.1. The fix is to compile under -O2. Find this in the Properties (TinyG) / Toolchain / AVR/GCC Coplier / Optimization window. In the flags box that follows add: 
`-fno-align-functions  -fno-align-jumps  -fno-align-loops -fno-align-labels -fno-reorder-blocks -fno-reorder-blocks-and-partition -fno-prefetch-loop-arrays -fno-tree-vect-loop-version`

#### Can't view ASCII strings in the debugger
The problem is that ASCII display are shown as decimal or hedidecimal numbers. There is a way around this documented here: http://www.avrfreaks.net/index.php?name=PNphpBB2&file=printview&t=105137&start=0

* Add the variable as a quickwatch and append ",c" to it. If the var is part of a struct you need the entire reference, e.g. tg.inbuf,c  <br>Then add the quickwatch to the watch. Of course this is impractical if you have a lot of ASCII vars to track

From the Studio6 Help Pages:

* _While debugging you might want to track a value of a variable or an expression. To do so you can right click at the expression under cursor and select Add a Watch or Quickwatch_

* _To add a QuickWatch expression to the Watch window_
* _In the QuickWatch dialog box, click Add Watch._

#### AtmelStudio6 fails verification when programming the xmega
We've seen this with earlier versions and Studio6.0 build 1996. We have not heard of this yet for AS6.2. Make sure you are up to Studio6 build 1996, service pack 2. See the `help` tab. Also make sure you are on the latest firmware for your programmer (Atmel-ICE or AVRISP mkii).

We've found that even though the verification failed the programming is good. You can either turn off verification or chose to ignore the warning. By the way - we've never seen this failure on Studio4. I actually think this is a bug in Studio6 rather than and actual failure. I have not seen any code die or act erratically when I get these errors.

#### Simulator won't start - Cannot find debugger message
Who knows why this happens. The get the debugger (simulator) back do the following
* Plug in and select an AVRISP mkII in the tinyg/tools tab
* Hit Debug and stop to try to debug. It will fail
* Go back to the tools tab and now select simulator for the tool
* Try to debug again. 60% of the time it works every time


(1) It gets worse. If you mess up and do things wrong studio6 will remember your paths in "recents" and fail the next time you try to set something up because things aren't where it thinks they should be. You have to go to the File/Recent Projects and Solutions, try to open the old paths, then remove them when they can't be found (you must have deleted the directories beforehand)

