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
* Mount the Mac filesystem as an external filesystem and use this for all your project files. Make sure you actually attach it to the W7 filesystem as a Z: drive or something. Studio6 won't compile if there is just a pathspec and no disk drive letter in the path.

### Get The Studio6.2 Complete Install Kit
Go to Atmel and sign up to download the entire Studio 6.2 production install package, including the .NET part. It's about 700 Mbytes.

### Clone the TinyG Github Repository 
Run git clone to set up TinyG working directory. First you must install git. See github for instructions to do this if you haven't already.
<pre>
From a command line navigate to the directory where you want the tinyg git directory to exist, then run:
git clone git@github.com:synthetos/TinyG.git
</pre>
You should have a directory with all the source files and the Atmel project files for both Studio4 and Studio6. Some of the project files are ignored by git so they don't cause havoc. You will need the AtmelStudio6 project files, which are `tinyg.atsln` and `tinyg.cproj`

_Note that if you are installing under VMware and have your project files in a shared volume (//vmware-host/...) you may need to map a network drive so that the IDE (4 or 6) can see a windows path as opposed to a UNC path. You can tell by looking at the full filepath in the directory window to see if it starts with Z:\... or Y:\...  If not then the IDE may not be able to compile._

### Setup AVRStudio4
You can find AvrStudio 4.19 and the AVR toolchain you need here: http://www.atmel.com/tools/studioarchive.aspx
Download these files:
<pre>
AVR Studio 4.19 (build 730)
Atmel AVR 8-bit and 32-bit Toolchain 3.3.1 - Windows
</pre>

Install the toolchain first, then AVRStudio. Click on the tinyg.aps file to start studio4. Run a Rebuild All from the Build tab to test that it's all working. You should get a clean build. The tinyg.hex file you want is in the /default directory.

You will need to set up the project configs like so. Of particular importance is including the floating point libs so the numeric displays will function properly.
<pre>
In the Custom Options section, the custom compilation [All files] tab should read:
-gdwarf-2 
-std=gnu99
-Wall
-DF_CPU=32000000UL
-Os
-funsigned-char
-funsigned-bitfields
-fpack-struct
-fshort-enums

In the [Linker options] section if must read:   (Note: may need to scroll left window to the end)
-Wl,-u,vfprintf
-lprintf_flt
-lm

That's -W "ell" (not "one") and all other leading 'l's are also "ell"
</pre>

Here's a useful link: http://www.avrfreaks.net/index.php?name=PNphpBB2&file=viewtopic&t=117023

To use Studio4 to program TinyG see [Programming TinyG with the Atmel AVRISP mkii Programmer](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer)

### Set up AtmelStudio6
Download and go through the Studio6 install process. We are using Atmel Studio 6.1 sp2 at this point. Click on the tinyg.atsln file to start the project. A clean build should leave tinyg.hex in the /Debug directory.

To use Studio6 to program TinyG see [Programming TinyG with the Atmel AVRISP mkii Programmer](https://github.com/synthetos/TinyG/wiki/Programming-TinyG-with-the-Atmel-AVRISP-Mkii-Programmer)

## Virgin AtmelStudio6 Setup (Importing TinyG or another project into Studio6)
You only need to do this is you are not using the pre-existing project files, as above
<br><br>
The simplest way to import TinyG or some other existing project into Studio6 is to clone the github repository and use the solution and project files provided. If for some reason these are not available or don't work or you just want to generate them yourselves the following instructions should help. 

These instructions are for importing an existing project such as tinyg into Atmel Studio6 solution/project in a way that's compatible with how we have organized the directories on github. Studio6 likes to set up virgin projects and is not really set up for importing existing projects; someone at Microsoft or Atmel never really exercised this use case, it seems. So I trip over project import in AS6 every single time. There is a menu item called "add existing project" but I think this is only useful for bringing in a "project" into a "solution" and we are setting up a new "solution" in this case.

The directory structure we use is:

`.../GitRepository/solution/project           for example:`

`.../TinyG/firmware/tinyg`

To get this do the following:

1. Create a git repository in the main directory (TinyG).

2. Open studio6 and create a new project. Parameters are:
 - TinyG is a `GCC C Executable Project`
 - The Name is `tinyg`
 - The Location is the path for the git repository. If you are in a VMware VM you may notice that your mounted drive (in my case the Z drive) is not in the browse list. You will need to enter the entire path name, for example: `Z:\Alden\Projects\proj38_TinyG\TinyG`
 - The Solution name is `firmware`. Allow it to create a new solution and to create a directory for the solution. This will create a file called `firmware.atsln` in the `firmware` directory and a `tinyg.cppproj` file in the working directory. Once this is done you can launch the project by double clicking either file. It's also OK to rename `firmware.atsln` to something more meaningful, like `tinyg.atsln`. It can also be moved to the working directory if you desire.

Here's an example of setting up a project called "extruderfin"
![example image](https://www.dropbox.com/s/n7zbysjmjvsq1lv/Screenshot%202013-11-26%2006.16.15.png)

3. Select the processor - TinyG uses an xmega192a3. Kinen slaves usually use ATmega328p

4. Now move all the .c and .h files into the project directory (e.g. tinyg). If there's a collision with the main.c or PROJECT.c file then replace the project-generated file with the one you are moving in. If there's a collision with the .cproj file do not replace it, instead use the one that's already in the directory (the newer one).

5. Go to the Solution Explorer that should be in a right-hand nav box. Right click the orange project directory icon and select add / existing item. Select all the .c, .h and any other files **IN THE MAIN DIRECTORY** that are necessary for the compiler / linker. Don't attempt to add sub-directories until step 6. Don't bother to add .txt, .doc, .md, any .cproj or .atsln files, any AVRstudio4 project files, or anything else that you may have under git management but are not actually part of the compile/link process. If there are compilation or linking artifacts this might be a good time to run `make clean`. You may have to go back multiple times to select as the file browser doesn't work the way you think it should - at least not under VMware on OSX. 

6. Now do this to **add the files in project sub-directories**. (Aside): If you have an existing project with sub-directories (like tinyg) there is no straightforward way to add the files to the project and leave the directory structure intact. You can't just click on the directory to add the entire directory. If you try to add a new directory it won't let you because that name is already used. If you add the items as "existing items" then navigate and click them it moves the files into the parent directory. Here's what you must do. 
 - Either move the sub-directory out of the path or rename it (e.g. xio_ORIG) to get it out of the way. 
 - Create a new directory off the parent directory with the name you want (e.g. xio). 
 - Move the files from the original directory into the new directory. You can delete the _ORIG directory now.
 - Go back to the nav, click on the newly created directory and select "add existing files". Add the files that are found in the newly populated sub-directory. 

7. Setup the following values in the project file. Right click on the main directory (e.g. `tinyg`) in the solution explorer then go to the Project / Properties tab from the drop-down menus. Enter the following values if they are not already set:
<pre>
IN THE TOOLCHAIN TAB

  AVR/GNU COMPLIER
    Optimization should be set to -Os

    SYMBOLS
      F_CPU=32000000UL   (or F_CPU=16000000UL or whatever is correct for your 328 processor)

  AVR/GNU LINKER 
    General: 
      Check the "use vprintf library (-Wl,-u,vprintf)" box (last box)

    Libraries: make sure these lines exist:
       printf_flt
       libm

 IN THE DEVICE TAB
  The DEVICE should say ATxmega192A3 (or whatever you are using)

</pre>

 8. Now try to build it from the Build / Build Solution menu. If it doesn't build it's probably because you left out a file in the solution explorer. Check the files against the directory to make sure you haven't left anything out.

Notes:

(1) It gets worse. If you mess up and do things wrong studio6 will remember your paths in "recents" and fail the next time you try to set something up because things aren't where it thinks they should be. You have to go to the File/Recent Projects and Solutions, try to open the old paths, then remove them when they can't be found (you must have deleted the directories beforehand)

### More AtmelStudio6 Topics
#### AtmelStudio6 fails verification when programming the xmega
We've seen this with earlier versions and Studio6.0 build 1996. Make sure you are up to Studio6 build 1996, service pack 2. See the `help` tab. Also make sure you rae on the latest firmware for the AVRISP mkii.

We've found that even though the verification failed the programming is good. You can either turn off verification or chose to ignore the warning. By the way - we've never seen this failure on Studio4. I actually think this is a bug in Studio6 rather than and actual failure. I have not seen any code die or act erratically when I get these errors.

#### Simulator won't start - Cannot find debugger message
Who knows why this happens. The get the debugger (simulator) back do the following
* Plug in and select an AVRISP mkII in the tinyg/tools tab
* Hit Debug and stop to try to debug. It will fail
* Go back to the tools tab and now select simulator for the tool
* Try to debug again. 60% of the time it works every time

#### Can't view ASCII strings in the debugger
There is a way around this: http://www.avrfreaks.net/index.php?name=PNphpBB2&file=printview&t=105137&start=0

Add the variable as a quickwatch and append ",c" to it. If the var is part of a struct you need the entire reference, e.g. tg.inbuf,c  <br>Then add the quickwatch to the watch. Of course this is impractical if you have a lot of ASCII vars to track

From the Studio6 Help Pages:

_While debugging you might want to track a value of a variable or an expression. To do so you can right click at the expression under cursor and select Add a Watch or Quickwatch_

_To add a QuickWatch expression to the Watch window_
_In the QuickWatch dialog box, click Add Watch._