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