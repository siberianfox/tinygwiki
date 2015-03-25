**This wiki page is a work in progress**
# Managing tinyG configuration parameters from the Chilipeppr/tinyG Workspace #
A widget is now available in the http://chilipeppr.com/tinyg workspace to support users in developing a settings and parameter management strategy for their workflow. The widget has two related but separate functionality threads:
## Archive ##
This implementation produces a text file on the PC running the Chilipeppr application. The contents of the file are the results of a $$ command sent to tinyG. The user may interactively add comments to the file for future reference. The widget provides a default name for the file which includes a date-timestamp plus current firmware build, this default can be overridden by the user prior to saving the file to the PC. The file is written using standard web-page download mechanisms, which results in the file be written to the PC in the default "Downloads" directory as set by the browser in use.
## Restore from Archive ##
Restore from archive can be viewed as reversing the archive process - a text file is read from the Chilipeppr host PC, parsed for settable parameters and written to tinyG. The contents of the file are assumed to follow the same syntax as a file created by the Archive widget, but edits are possible either on the PC (using a supported text editor) or in the widget window prior to committing the parameters to tinyG. Any number of parameters can be set by the file, e.g. a file with just Motor Parameters or just Axis Parameters or just G.xx Coordinate offsets can be written.
The widget implements a synchronous parameter by parameter write; each parameter is sent to tinyG and the widget waits for acknowledgement from tinyG before the next parameter is sent.
A write of the full $$ parameter set typically completes in 2 to 3 seconds.
### Parameter Filter ###
The Restore-from-Archive widget automatically filters (blocks writes of) parameters that are Read-Only or that have been determined to cause verification issues with the parameter write process. At present, the following parameters are filtered: 

  `command_filter = ['fb', 'fv', 'hp', 'hv', 'id', 'ej', 'jv', 'js', 'baud'];`

# Detailed How-T0 : Archive #
From the Chilipeppr/tinyG workspace, find the icon for configuretinyG on the TinyG Widget menu and click to open

![Open configuretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_226.jpg)

Then select the down carot adjacent to the SAVE button and click on Archive/Restore to/from Local File

![Open archiveandrestoretinyG](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_227.jpg)

And the widget opens with two Tabbed workspaces, Archive to File and Restore from Archive File

![Widget Workspace](https://dl.dropboxusercontent.com/u/50261731/Wiki%20Work/Selection_228.jpg)



wip


# Detailed How-To : Restore-from-Archive #


wip

### Acknowledgements ###

The step by step in these widgets follows closely a set Python scripts originally posted by Scott Carlson
Kevin Hauser was developing the GUI based tinyG configuration editor for Chilipeppr at the same time and provided the synchronous write code workable in JavaScript. John Laurer provided constant guidance on how jsFiddle and Chilipeppr are structured and work.