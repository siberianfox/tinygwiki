These are random notes that may be useful for developers
## AtmelStudio6 Setup (Importing TinyG or another project into Studio6)
Forward: I still prefer Studio 4 for development and simulation, but also compile in 6 to maintain compatibility and take advantage of the newer C compiler and libraries for major releases. The .hex file for 6 is in the Debug dir, the .hex from 4 is in the default dir.

The simplest way to import TinyG or some other existing project into Studio6 is to clone the github repository and use the solution and project files found in the /support directory. If for some reason these are not available or don't work (we don't always remember to update them with project changes) or for some reason you just want to generate them yourselves the following instructions should help. 

These instructions are for importing an existing project such as tinyg into Atmel Studio6 solution/project in a way that's compatible with how we have organized the directories on github. Studio6 likes to set up virgin projects and is not really set up for importing existing projects; someone at Microsoft or Atmel never really exercised this use case, it seems. So I trip over project import in 6 every single time. There is a menu item called "add existing project" but I think this is only useful for brining in a "project" into a "solution" and we are setting up a new "solution" in this case.

The directory structure we use is:

`.../GitRepository/solution/project           for example:`

`.../TinyG/firmware/tinyg`

To get this do the following:

1. Create a git repository in the main directory (TinyG).

2. Open studio6 and create a new project. Parameters are:
 - TinyG is a "GCC C Executable Project" 
 - The Name is "tinyg"
 - The Location is the path for the git repository
 - The Solution name is "firmware". Allow it to create a new solution and to create a directory for the solution

3. Select the processor - TinyG uses an xmega192a3. Kinen slaves usually use mega328p

4. Now move all the .c and .h files into the project directory (e.g. tinyg). If there's a collision with the main.c or PROJECT.c file then replace the project-generated file with the one you are moving in. If there's a collision with the .cproj file do not replace it, instead use the one that's already in the directory (the newer one).

5. Go to the Solution Explorer that should be in a right-hand nav box. Right click the orange project directory icon and select add / existing item. Select all the .c, .h and any other files **IN THE MAIN DIRECTORY** that are necessary for the compiler / linker. Don't attempt to add sub-directories until step 6. Don't bother to add .txt, .doc, .md, any .cproj or .atsln files, any AVRstudio4 project files, or anything else that you may have under git management but are not actually part of the compile/link process. You may have to go back multiple times to select as the file browser doesn't work the way you think it should - at least not under VMware on OSX. 

6. Now do this to **add the files in project sub-directories**. (Aside): If you have an exiting project with sub-directories (like tinyg) there is no straightforward way to add the files to the project and leave the directory structure intact. You can't just click on the directory to add the entire directory. If you try to add a new directory it won't let you because that name is already used. If you add the items as "existing items" then navigate and click them it moves the files into the parent directory. Here's what you must do. 
 - Either move the sub-directory out of the path or rename it (e.g. xio_ORIG) to get it out of the way. 
 - Create a new directory off the parent directory with the name you want (e.g. xio). 
 - Move the files from the original directory into the new directory 
 - Go back to the nav, click on the newly created directory and select "add existing files". Add the files that are found in the newly populated sub-directory. 

7. Now try to build it from the Build / Build Solution menu. If it doesn't build it's probably because you left out a file in the solution explorer.

Notes:

(1) It gets worse. If you mess up and do things wrong studio6 will remember your paths in "recents" and fail the next time you try to set something up because things aren't where it thinks they should be. You have to go to the File/Recent Projects and Solutions, try to open the old paths, then remove them when they can't be found (you must have deleted the directories beforehand)

## More AtmelStudio6 Topics
### Can't view ASCII strings in the debugger
There is a way around this: http://www.avrfreaks.net/index.php?name=PNphpBB2&file=printview&t=105137&start=0

Add the variable as a quickwatch and append ",c" to it. If the var is part of a struct you need the entire reference, e.g. tg.inbuf,c  <br>Then add the quickwatch to the watch. Of course this is impractical if you have a lot of ASCII vars to track

From the Studio6 Help Pages:

_While debugging you might want to track a value of a variable or an expression. To do so you can right click at the expression under cursor and select Add a Watch or Quickwatch_

_To add a QuickWatch expression to the Watch window_
_In the QuickWatch dialog box, click Add Watch._

## TinyG Github Notes
If you are unfamiliar with git it's useful to start here:
* http://learn.github.com/p/intro.html
* http://gitref.org
* http://learn.github.com/p/tagging.html

### Setting up in Github
Do a complete pull 
<pre>git clone git@github.com:synthetos/TinyG.git</pre>
You will have all 3 branches:
* master - stable production branch
* edge - relative stable development branch
* dev - current build. No guarantees.

Also at the command line it looks like you need to configure your git globals 
<pre>git config --global user.name "alden.hart"
git config --global user.email "alden@asdfsd.com"</pre> 
This will make it so when you push updates, your name will be "blue" for tracking purposes on github. (blue meaning it will link to your profile on github)

### Normal Operations

Before getting started coding always make sure you are in sync with the github central repository. 
<pre>
cd your-working-directory
git status
git pull
</pre>

Status tells you what branch you have open and what files are not committed. The pull gets you up-to-date with the centrol repo and ensures you are not going to clobber something later 

From here it depends on what you want to work on. I assume you want to go to dev to push your new changes you made.

### Changing Branches

<pre>git checkout dev</pre> 
This now switches your files for you. If you encounter difficulties and cannot switch to dev it's usually because some files in the dev branch have changed and will be clobbered if you switch. In general you need to move these files out of the way (e.g. to the desktop), do the checkout, then replace the checked out files with the ones you moved aside. Or not, if you don't want the changes. We are still working out the .gitignore as some of these files are irrelevant to the source. 

You can also change branches to an earlier push by:
<pre>git checkout <hashcode of that commit> </pre>
To recover just change branch back to dev or whatever.

Now edit or move files around. If you edit files things work as usual. If you have files edited elsewhere just copy them over the existing ones. 

Once you are done issue a git status... Its optional but informative. Shows whats changed etc... 
<pre>git status</pre> 
If you need to get rid of any files do it through git, not the OS or finder/explorer. Some examples: 
<pre>git rm filename.ext
git rm file*
git rm -r dirname
</pre> 
To push to github the&nbsp;normal operation is illustrated. The pull is just to make sure you won't encounter collision difficulties. Use whatever your build number is instead of 337.07 in the example.&nbsp; 
<pre>cd &lt;current dir&gt;
git pull
git add .
git commit -m "337.07 dev - informative blah blah blah"
git tag -a 337.07 -m"build 337.07 in dev - informative blah blah blah"
git push origin dev --tags </pre>

**NOTICE THE ORIGIN KEYWORD IN THE PUSH**

This will push your changes up stream. 

The pull should have been done before you made any changes, but at least this will tell you if you are going to have a problem in your push.

### Merging

This is a bit tricky but not hard. Just need to use a nice GUI merge tool if there are conflicts. 

The command is: 
<pre>git checkout master</pre> 
(This sets your master branch active... This is where you are going to PULL another branch into merge with) git merge &lt;branch to merge with&gt; 

First make sure everything is up to date - synchronize the lowest branch to github: 

*If the IDE is open close it and save whatever you need to 
*Add and Commit anything that's been changed 
*run git status to confirm there's nothing hanging out there 
*git push origin &lt;branch&gt;

Example of merging master with edge: 
<pre>git checkout master
git merge edge</pre> 
That should do it.<br> But it usually doesn't. Usually you find merge conflicts on readme.md, tinyg.aws or other files. Do this:<br> 

*Make a temp directory on the desktop or somewhere else outside the git directory 
*Put copies of all the files in conflict into this directory
*git rm all the conflicted files. Remember to do *git rm*, not just *rm*!
*See if the files that were pulled in from the pull branch (e.g. from edge merging into master) are the ones you want. If so keep them,. If not, overwrite them from the temp directory
*Add any new files to git - e.g git add .
*Commit all changes - e.g. commit -m"339.09 added files from edge into master as part of a merge"
*Do the merge again (e.g. git merge edge)
***Finish the merge by running git&nbsp;

### Promoting Branches

Steps to promote dev to edge and edge to master are noted here.
Example of promoting dev to edge
* Start by closing out edge and syncing it to github. See first steps in MERGING, above
* git checkout edge to change to edge branch
* git pull if the checkout says you are behind the origin
* git status to see if it's clean
* We maintain separate readme files outside of git - look in github support. Edit the readme_edge.md file and make a copy into the main directory. Delete the current readme.md and rename the readme_edge.md to readme.md

### Tags

Tags support getting back to earlier builds:

See here: https://github.com/synthetos/TinyG/tags

To list local tags type 
<pre>git tag
</pre> 
Once you are ready to commit (or push your changes) create a tag using this format:
<pre>git tag -a 337.06 -m "build 326.06 - &lt;some note as to what this build is about&gt;"</pre> 
Then push the&nbsp;tag to the public repo. By default, the ‘git push’ command will not transfer tags to remote servers. To do so, you have to explicitly add a –tags to the ‘git push’ command:
<pre>git push origin dev --tags
</pre> 
<br> You can delete a remote tag the same way that you delete a remote branch. 
<pre> git push origin&nbsp;:326.06
</pre> 
And delete your local tag with: 
<pre>git tag -d 326.06
</pre> 
from http://stackoverflow.com/questions/6151970/how-do-you-remove-a-tag-from-a-remote-repository

### .gitignore

Note that when you change .gitignore you must explicitly remove it from the cache by issuing:
<pre>
git rm --cached .gitignore
</pre>
Here's what we have in .gitignore right now:
<pre>
*.elf
*.pyc
*.o
*.map
*.lss
*.map
*.d
tinyg.aws
tinyg.aps
tinyg.atsln
tinyg.atsuo
tinyg.cproj
tinyg.eep
.DS_Store
</pre>
See here for details: http://help.github.com/ignore-files/


