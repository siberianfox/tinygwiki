_Updated 11/8/13 - ash_
This gets pretty deep in the weeds. This page is a collection of tings that tripped us up on Github and some documentation for common procedures like branch promotion, etc.

## At Last - Gitx
If you are working on the Mac, [Gitx](http://gitx.frim.nl/) is a really nice UI for Github. It's not perfect, but it really helps. Some things still need to be done from the command line, and it can hang up occasionally on large push and pull operations and need to be killed from the Activity Monitor. But on balance it makes managing the git tasks much easier.

## Github Cheat Sheet
A few rules to live by. Most git problems I have start from working in some "place" I don't think I'm in or on some set of files in an indeterminate state. These practices help reduce those possibilities.
* When you sit down to work always do this:
 * `git status` to see where you are and if you have any uncommitted files in your local repository
  * If you do have uncommitted files then stash them somewhere before doing the pull
 * `git pull origin CURRENT_BRANCH` to make sure you are synced with the main repo for that branch
* When you are ready to stop always commit everything - unless you are sure you don't want to
 * `git status` to see what you've got
 * `git add` any new files you might have created
 * `git commit -a -m"XXX.YY Build number and notes that are understandable by someone other than you"`
 * `git push origin CURRENT_BRANCH`
* If you want to change branches do this:
 * Commit and push everything in the current branch as above
 * `git checkout NEW_BRANCH`
 * `git pull origin NEW_BRANCH`

## TinyG Github Notes
If you are unfamiliar with git it's useful to start here:
* http://learn.github.com/p/intro.html
* http://gitref.org
* http://learn.github.com/p/tagging.html

### Setting up TinyG in Github
Do a complete pull 
<pre>git clone git@github.com:synthetos/TinyG.git</pre>
You will have all 3 branches:
* master - stable production branch
* edge - relative stable development branch
* dev - current build. No guarantees.

Also at the command line it looks like you need to configure your git globals 
<pre>
git config --global user.name "alden.hart"
git config --global user.email "alden@asdfsd.com"
</pre> 
This will make it so when you push updates, your name will be "blue" for tracking purposes on github. (blue meaning it will link to your profile on github)


### Normal Operations
Before getting started coding always make sure you are in sync with the github central repository. 
<pre>
cd your-working-directory
git status
git pull origin your-current-branch
</pre>

Status tells you what branch you have open and what files are not committed. The pull gets you up-to-date with the centrol repo and ensures you are not going to clobber something later.

From here it depends on what you want to work on. I assume you want to go to dev to push your new changes you made.

### Changing Branches
Before changing branches make sure you have all the work on the current branch committed. Otherwise git won't let you change. If you don't want the work in the current branch just reset  `git reset --hard`. You will lose the work.

<pre>git checkout dev</pre> 
This now switches your files for you. If you encounter difficulties and cannot switch to dev it's usually because some files in the dev branch have changed and will be clobbered if you switch. In general you need to move these files out of the way (e.g. to the desktop), do the checkout, then replace the checked out files with the ones you moved aside. Or not, if you don't want the changes. We are still working out the .gitignore as some of these files are irrelevant to the source. 

You can also change branches to an earlier push by:
<pre>git checkout <hashcode of that commit> </pre>
To recover just change branch back to dev or whatever. Use the git Commits link to find the commit you want, then copy the hash into your clipboard and use it during the checkout. ALl cautions about checking out branches still apply in this case.

Now edit or move files around. If you edit files things work as usual. If you have files edited elsewhere just copy them over the existing ones. 

Once you are done issue a git status... Its optional but informative. Show's what's changed etc... 
<pre>git status</pre> 
If you need to get rid of any files do it through git, not the OS or finder/explorer. Some examples: 
<pre>git rm filename.ext
git rm file*
git rm -r dirname
</pre> 
**Note: git's gotten better about this. I routinely remove files from the OS now and git manages it OK**

### Pushing Code to Github
Our conventions for pushing to github are described here. Use whatever your build number is instead of 337.07 in the example; 
<pre>cd &lt;current dir&gt;
git status 
(add any new files you might want. See the bottom of the status display)
git commit -a -m"367.04 A message that is informative to someone looking for a branch blah blah blah"
git push origin dev
</pre>

If you want to tag the push add the following lines. We've found tags to be redundant if you provide good enough messages. Tags are useful for release candidates and other things like that, however.
<pre>
git tag -a 337.07 -m"build 367.04 Release candidate for version 0.95"
git push origin dev --tags 
</pre>

**NOTICE THE ORIGIN KEYWORD IN THE PUSH**

The pull should have been done before you made any changes, but at least this will tell you if you are going to have a problem in your push.


### Merging

This is a bit tricky but not hard. Just need to use a nice GUI merge tool if there are conflicts. 

The command is: 
<pre>
git checkout master
(This sets your master branch active... This is where you are going to PULL another branch into merge with) git merge branch-to-merge-with 
</pre>


First make sure everything is up to date - synchronize the lowest branch to github: 

* If the IDE is open close it and save whatever you need to 
* Add and Commit anything that's been changed 
* run git status to confirm there's nothing hanging out there 
* git push origin &lt;branch&gt;

Example of merging master with edge: 
<pre>
git checkout master
git merge edge
</pre> 
That should do it.<br> But it usually doesn't. Usually you find merge conflicts on readme.md, tinyg.aws or other files. Do this:<br> 

* Make a temp directory on the desktop or somewhere else outside the git directory 
* Put copies of all the files in conflict into this directory
* git rm all the conflicted files. Remember to do *git rm*, not just *rm*!
* See if the files that were pulled in from the pull branch (e.g. from edge merging into master) are the ones you want. If so keep them,. If not, overwrite them from the temp directory
* Add any new files to git - e.g git add .
* Commit all changes - e.g. commit -m"339.09 added files from edge into master as part of a merge"
* Do the merge again (e.g. git merge edge)
**Finish the merge by running git**



### Checklist for Promoting Branches
Steps to promote dev to edge and edge to master are listed here. There's probably an easier way to do this for someone that really knows how to use github, but this is what I do. Comments and suggestions for improvement are welcome. Derision and sneers are amusing.

Start in the dev branch
* Make sure all dirs are where you want them
 * firmware - all code changes are in place and pushed to github as dev. Make sure the project is set to -Os optimization and all debug defines are disabled. Compile under both 4 and 6 and verify
 * Move the fresh project files into the support directory
 * hardware - look for changes to any schematics or other HW artifacts
 * gcode_samples - add any new files or rearrangements of existing files
* Get the support directory up to date. This includes
 * edit the readme files. If you mad any changes to the dev file copy it into the main dir and rename it to readme.md to replace the one that's in there.
 * bring in the current studio 4 and studio 6 project files for tinyg and xboot. Overwrite any old ones that are there.
 * are there any drivers or other artifacts to add?
* Update xboot if needed
* Make sure dev is fully pushed to github
* Stash copies of all the directories in a stash directory


Now do the edge branch
* Change branches to edge
* Replace the edge directories with the ones you stashed from dev
* Run git status to see what you've got. Look for any untracked files and add them if needed
* Replace the readme.md file in the main directory with the readme_edge.md (renamed to readme.md)
* When you are satisfied that tings look right - git push origin edge

Promoting edge to master is similar

### Tags

Tags support getting back to earlier builds. See here: https://github.com/synthetos/TinyG/tags

To list local tags type 
<pre>
git tag
</pre> 

Once you are ready to commit (or push your changes) create a tag using this format:
<pre>
git tag -a 337.06 -m "build 326.06 - &lt;some note as to what this build is about&gt;"
</pre> 
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

###Replacing the main branch with a Recovery Branch (Rollback - see 405.xx builds)
Here's the scenario this is useful for:
* You find your main branch (presumably dev) has some killer error, and it's already been pushed
* You go back and find the last commit that worked OK by checking it out as a detached head
* You create a new branch (called "recovery") from there
* You work on the recovery branch and make a bunch of changes and commits
* Now you want to merge recovery into the main branch, but have the main branch "become" what's in recovery, discarding all the changes you made in the main branch 

Here's how to do this:
<pre>
git checkout recovery
git merge --strategy=ours dev
git status  (just checking)
git checkout dev
git merge recovery
git push
</pre>
You can then delete the recovery branch. If you want it to garbage collect right away type `git gc`

###Brute force promotion of dev to edge or edge to master
Promotion checklist
* Set Default settings

<pre>
make sure both dev and edge are clean (no uncommitted files)
git checkout dev
git merge --strategy=ours edge
git status  (just checking)
git checkout edge
git merge dev
git push
</pre>
