This gets pretty deep in the weeds. This page is a collection of common practices we use on Github. It's a way to document the things that have tripped us up on Github and to provide some documentation for common procedures like branch promotion, etc.

TOC
 * [Gitx Git GUI](#at-last---gitx)
 * [Github Cheat Sheet](#github-cheat-sheet)

## Getting Started
If you are unfamiliar with git it's useful to start here:
* http://learn.github.com/p/intro.html
* http://gitref.org
* http://sethrobertson.github.io/GitBestPractices/ -- Read this. We try not to repeat what's already here.
* http://learn.github.com/p/tagging.html

### At Last - Gitx
If you are working on the Mac, [Gitx](http://gitx.frim.nl/) is a really nice UI for Github. It's not perfect, but it really helps. Some things still need to be done from the command line, and it can hang up occasionally on large push and pull operations and need to be killed from the Activity Monitor. But on balance it makes managing the git tasks much easier. 

The examples on this page are written assuming Gitx. Some command line operations are also provided as git command line commands, e.g. `user& git status`, or [`user& git status`] for the command line version of the gitx command just mentioned.

## Github Standard Practices
A few rules to live by. Many git problems start from working in some "place" you don't think you are in or on some set of files in an indeterminate state. These practices help reduce those possibilities.

**When you sit down to work always do this**
* Check Branches and Stage area [`user& git status`] to see what branch you are on and if you have any uncommitted files in your local repository
* Commit or stash any uncommitted files before doing the pull. You should generally avoid leaving uncommitted files at the end of a session, but it's not always possible.
* Pull origin and update current branch [`user& git pull origin CURRENT_BRANCH`] to make sure you are synced with origin.
  * You might need to do a manual merge at this point.

**When you are ready to end the session** leave the work area clean. Commit everything - unless you are sure you don't want to. Decide if you want to push or not.
* Commit from Stage dialog
* Command line for above:
  *[`git status`] to see what you've got
   * [`git add`] any new files you might have created
  * [`git commit -a -m"XXX.YY Build number and notes that are understandable by someone other than you"`]
  * [`git push origin CURRENT_BRANCH`]
* Push if decided

**If you want to change branches do this:**
* Commit and push everything in the current branch as above
* `git checkout NEW_BRANCH`
* `git pull origin NEW_BRANCH`

## Setting up TinyG in Github
Do a complete pull 
<pre>
git clone git@github.com:synthetos/TinyG.git
</pre>
You will have all 3 branches:
* master - stable production branch
* edge - relative stable development branch
* dev - current build. No guarantees.

Also at the command line it looks like you need to configure your git globals 
<pre>
git config --global user.name "yourname"
git config --global user.email "yourname@asdfsd.com"
</pre> 
This will make it so when you push updates, your name will be "blue" for tracking purposes on github. (blue meaning it will link to your profile on github)

## Common Operations
### Using Tracking Branches
If you are going to a do a major new feature it can be useful to create a separate tracking branch. This isolates your changes from the parent branch until you merge in later, and makes removing those changes easier should you need to do that later. Setting up a tracking branch (Gitx):

* From the REMOTES/Origin area right click the parent branch (e.g. dev) and select `Create a branch that tracks origin/dev...`
* Name the branch `feature-MMM.mm-name-of-feature` - where MMM.nnn is the major and minor build number for the branch
* Later you can push the branch to origin, or not, depending on how you want to run it.

Do the development. When you are ready to merge the branch back into the parent:

* Select the parent branch (e.g. dev)
* Merge feature branch into main. It will likely have collisions and the merge will fail
* Got to gitx Staging and and edit the unstaged files to remove conflicts. Compile and verify operation
* Now from the command line (terminal) `git commit -a -m"merging feature-MMM.mm-name-of-feature`
* `git status ` to confirm that the dir is clean
* Back to gitx, push dev to origin.

### Manual Merging
To resolve conflicts you can just edit the conflicted files and remove the <<<<<<<< sections. When committing be sure the add these files with a git commit -a or a git add command.

### Graphical Merging
This is a bit tricky but not hard. Just need to use a nice GUI merge tool if there are conflicts. 

If you have not already set up a graphical merge tool run this:
<pre>
git config merge.tool   (response is silent as you have no merge tool)
git config --global merge.tool opendiff
git config merge.tool   (response should now say opendiff)
opendiff                (run the command just to see of you have it. Response is "too few arguments...)
</pre>

Now do the pull and get one or more conflicts. Git status will show Unmerged Paths. Something like:
<pre>
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#
#	both modified:      TinyG2/tinyg2.h
</pre>

Type `git mergetool` and hit return at the prompt. Filemerge will open. There is a dot at the bottom of Opendiff that you can drag up to show the resulting file in a new pane (default position does not show the bottom pane). When done, "save" and then quit opendiff completely. It should take you back to the terminal. If there are more files to merge hit return again and repeat.

When done run `git commit`. (from command line - does not work from gitx). It may throw you into vi. Save / Quit using `:x`. If you are in vim the command is `ESC` the `:x`

You might end up with one or more xxxx.orig files. Run `git clean -n` to list these. You can then run `git clean -f` to remove them

### Using .gitignore

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

### Replacing working branch with a recover branch
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

### Brute force promotion of dev to edge or edge to master
THis is not the recommended way to do things. Better to do a merge. But in some cases this may be necessary. 

**Promotion checklist**
* Final check compile in both AS4 and AS6 (ensure project files are OK)
* Double check:
 * Build number is correct (and check all other Revisions and Compile-time settings) 
 * Settings to settings_default.h
 * Comment out test99
 * __SIMULATION is off

<pre>
#first make sure both dev and edge are clean (no uncommitted files)
git checkout dev
git merge -s ours edge    (edit commit message, exit with ":x")
git status       (just checking)
git diff HEAD^   (should show no changes)

#now go merge
git checkout edge
git merge dev

#verify it worked
git diff dev     (should show no changes)
git push origin edge
</pre>

###Tips

- Merge using `--no-ff` That's two dashes
- To set --no-ff as the default for that repo: `git config --add merge.ff false`