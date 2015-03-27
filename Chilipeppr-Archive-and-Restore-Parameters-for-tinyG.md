**This wiki page is a work in progress**
# Managing tinyG configuration parameters from the Chilipeppr/tinyG Workspace #
A widget is now available in the http://chilipeppr.com/tinyg workspace to support users in developing a settings and parameter management strategy for their workflow. The widget has two related but separate functionality threads:
## Archive ##
This implementation produces a text file on the PC running the Chilipeppr application. The contents of the file are the results of a $$ command sent to tinyG. The user may interactively add comments to the file for future reference. The widget provides a default name for the file which includes a date-timestamp plus current firmware build, this default can be overridden by the user prior to saving the file to the PC. The file is written using standard web-page download mechanisms, which results in the file be written to the PC in the default "Downloads" directory as set by the browser in use.
[Details here](https://github.com/synthetos/TinyG/wiki/Chilipeppr-Archive-and-Restore-Parameters-for-tinyG#detailed-how-to--archive)
## Restore from Archive ##
Restore from archive can be viewed as reversing the archive process - a text file is read from the Chilipeppr host PC, parsed for settable parameters and written to tinyG. The contents of the file are assumed to follow the same syntax as a file created by the Archive widget, but edits are possible either on the PC (using a supported text editor) or in the widget window prior to committing the parameters to tinyG. Any number of parameters can be set by the file, e.g. a file with just Motor Parameters or just Axis Parameters or just G.xx Coordinate offsets can be written.
The widget implements a synchronous parameter by parameter write; each parameter is sent to tinyG and the widget waits for acknowledgement from tinyG before the next parameter is sent.
A write of the full $$ parameter set typically completes in 2 to 3 seconds.
### Parameter Filter ###
The Restore-from-Archive widget automatically filters (blocks writes of) parameters that are Read-Only or that have been determined to cause verification issues with the parameter write process. At present, the following parameters are filtered: 

  `command_filter = ['fb', 'fv', 'hp', 'hv', 'id', 'ej', 'jv', 'js', 'baud'];`

# Using this widget #
Workflows are personal preferences. It is highly recommended to keep configuration archives available for reference use in new builds , firmware upgrades and as attachments to help requests on the various Forums supporting tinyG..

Using an archive file to facilitate transition to a new tinyG FW build has been successfully tested but needs to be carefully studied. Some FW builds introduce new parameters that may need to be configured for the new upgrade. If parameters are removed as part of an upgrade, attempting to write an non-existent parameter will likely cause a failure of the write process.
A recommendation would be to compare an Archive file from the new FW load to a previous full Archive (manually, or use a tool such as 'diff') to verify parameter usage before attempting a Restore from Archive file.

# Detailed How-To : Archive #
From the Chilipeppr/tinyG workspace, find the icon for configuretinyG on the TinyG Widget menu and click to open

![Open configuretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_226.jpg)

Then select the down carot adjacent to the SAVE button and click on Archive/Restore to/from Local File

![Open archiveandrestoretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_227.jpg)

And the widget opens with two Tabbed workareas, Archive to File and Restore from Archive File

![Widget Workspace](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_228.jpg)

Step 1 - Click the Upload from tinyG box and the Edit Window populates with the results from a $$ command to tinyG.

A comment line `// Response from $$ Command` is added at the top of the file.

![Populated Edit Window](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_235.jpg)

Step 2  - Depending on your workflow, you may wish to edit in additional comment rows, starting with '// ', in the file. An example added row is shown with a red arrow above.
You may also make any other edits to or deletions from to the contents of the Edit window

note: You have the same editing capability once the file is downloaded to you PC. Choose the solution best for you. Always good to remind yourself why you made the backup, perhaps
 
`// tinyG configurations updated and tested `
`or`
`// Special tinyG settings for running _xyz_ job`

You can also edit the default file name field to match your workflow preferences.

Step 3 - The final Step for Archive is to save the file to your PC's default Downloads directory as set in your browser settings.This action is initiated by clicking the Download Archive File button. The GUI actions may vary between browsers.
Using your OS File Browser, navigate to the location of your Downloads directory to find the Archive file. 

Click the Close button at the bottom of the widget window to close the widget and return to the configuretinyG widget.

# Detailed How-To : Restore-from-Archive #
From the Chilipeppr/tinyG workspace, find the icon for configuretinyG on the TinyG Widget menu and click to open

![Open configuretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_226.jpg)

Then select the down carot adjacent to the SAVE button and click on Archive/Restore to/from Local File

![Open archiveandrestoretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_227.jpg)

And the widget opens with two Tabbed workspaces, Archive to File and Restore from Archive File. Click on the Restore from Archive File Tab and the following window appears

![Restore Window](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_236.jpg)

Step 1 is to identify a configuration file from which to restore. Click on Choose File and a "File Chooser" window will open. It's appearance is defined by (and provided by) your Operating System; this example is the file chooser provided by KDE desktop on Linux.

![Typical file chooser interface](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_237.jpg)

Navigate your file system, per you workflow, identify the configuration file you wish to use, select it and click the Open button. You will return to the widget window with the chosen file name indicated (red arrow)

![File selected](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_238.jpg)

Step 2 - Now click on Load File to Text Window and the workarea will populate with the file contents.

![Configuration Archive Loaded](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_239.jpg)

At this point you can scroll thru the parameter file, make optional parameter changes and/or deletions. In the following example, all but the X-axis parameters have been deleted from the workarea.
note: Changes made in the workarea will be written to tinyG but are not written back to the PC file.
If you want a copy of your changes, run make a new Archive of tinyG after writing and verifying the new parameters.

![Edited workspace](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_240.jpg)

Step 3 - When review and edits are complete, click on Download to tinyG. Each line in the text window is parsed for a writable parameter and the parameters are written to tinyG. For tinyG v7 and v8,  these writes are to on-chip flash memory and take a variable but finite period of time. The widget waits for a parameter write confirmation before moving on to the next line in the workarea. If you watch tinyG, you will see the activity LEDs near the USB port flashing.
Upon completion, the results of processing the parameter writes is displayed in the work area, showing verified status and some diagnostic metrics on the total write cycle time as seen by the widget.

![Response from Download of parameters.](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_241.jpg)

At least one tinyG parameter, `$p1frq pwm frequency` , is known to require a tinyG hard reset to take effect. A good practice is likely to perform such a rest of tinyG after exiting the Widget.
A tinyG reset, either by reset button or sending reset from the Chilipeppr tinyG widgeticon, will cause tinyG to disconnect from the Serial Port Json Server.
After reconnecting, use the Chilipepper configuretinyG widget and/or the Serial Port Console to check your parameters.


### Acknowledgements ###

The step by step in these widgets follows closely a set Python scripts originally posted by Scott Carlson. 
Kevin Hauser was developing the GUI based tinyG configuration editor for Chilipeppr at the same time and provided the synchronous write code workable in JavaScript. John Laurer provided constant guidance on how jsFiddle and Chilipeppr are structured and work.