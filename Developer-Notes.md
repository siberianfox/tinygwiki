These are random notes that may be useful for developers
## AtmelStudio6 and AVRStudio4 Setup
I still prefer Studio 4 for development and simulation, but also compile in 6 to maintain compatibility and take advantage of the newer C compiler and libraries for release. The .hex file for 6 is in the Debug dir, the .hex from 4 is in the default dir.

I trip over project setup in 6 every single time. The directory structure we use is:

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

4. Now move all the .c and .h files into the project directory (tinyg). If there's a collision with the main.c or <project>.c file then overwrite the existing file with the one you are moving in.

5. Go to the 

It gets worse. If you mess up and do things wrong studio6 will remember your paths in "recents" and fail the next time you try to set something up because things aren't where it thinks they should be. You have to go to the File/Recent Projects and Solutions, try to open the old paths, then remove them when they can't be found (you must have deleted the directories beforehand)