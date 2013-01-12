These are random notes that may be useful for developers
## AtmelStudio6 Setup
These instructions are for setting up an Atmel Studio6 solution/project in a way that's compatible with how the project is organized on github. I trip over project setup in 6 every single time. I still prefer Studio 4 for development and simulation, but also compile in 6 to maintain compatibility and take advantage of the newer C compiler and libraries for release. The .hex file for 6 is in the Debug dir, the .hex from 4 is in the default dir.

The directory structure we use is:

.../GitRepository/solution/project  for example:

.../TinyG/firmware/tinyg

To get this do the following:

1. Create a git repository in the main directory (TinyG).

2. Open studio6 and create a new project. Parameters are:
 - TinyG is a "GCC C Executable Project" 
 - The Name is "tinyg"
 - The Location is the path for the git repository
 - The Solution name is "firmware". Allow it to create a new solution and to create a directory for the solution

3. Select the processor - TinyG uses an xmega192a3. Kinen slaves usually use mega328p

4. Now move all the .c and .h files into the project directory (e.g. tinyg). If there's a collision with the main.c or <project>.c file then replace the project-generated file with the one you are moving in. If there's a collision with the .cproj file do not replace it, instead use the one that's already in the directory (the newer one).

5. Go to the Solution Explorer that should be in a right-hand nav box. Right click the orange project directory icon and select add / existing item. Select all the .c, .h and any other files that are necessary for the compiler / linker. Don't bother to add .txt, .doc, .md, the .cproj file, any AVRstudio4 files, or anything else that you may have under git management but are not technically part of the compile/link process. You may have to go back multiple times to select as the file browser doesn;t work the way you think it should - at least not under VMware on OSX. 

6. Now try to build it from the Build / Build Solution menu. If it doesn't build it's probably because you left out a file in the solution explorer.

Notes:

It gets worse. If you mess up and do things wrong studio6 will remember your paths in "recents" and fail the next time you try to set something up because things aren't where it thinks they should be. You have to go to the File/Recent Projects and Solutions, try to open the old paths, then remove them when they can't be found (you must have deleted the directories beforehand)