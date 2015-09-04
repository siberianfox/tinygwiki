_This is an experimental page_

###JSON Request Objects
To date, there have been no request objects to wrap an incoming command to TinyG. TinyG accepts the following types of commands without any wrappers:

- Gcode block - e.g. N20 G0 X111.3 Y21.0
- JSON command - e.g. {"xvm":15000} 
- Text command - e.g. $xvm=15000
- Special characters:  !, ~, %, ^x

JSON request objects support wrapping the above in JSON. The reasons for this include:

- Supporting a transaction ID that is independent of the command
- Allowing the sender to distinguish between control, data and 'either' commands
- Supporting routing of requests to specific endpoints (addressing)

###JSON Request Wrappers
The following elements can be present in a request wrapper.

**Command Line**. One of:

    {cmd:"<command string>"}    command is either control or data (unidentified)
    {ctl:"<command string>"}    command is a control
    {dat:"<command string>"}    command is data (Gcode)

Handling is:
- Gcode block - will return JSON response to Gcode
- JSON command - will return JSON response to JSON request. The incoming JSON request may be escaped for quotes. 


Valid request wrapper forms:


    {tid:<txn_number>,cmd:"<command string>"}
    {tid:<txn_number>,ctl:"<command string>"}
    {tid:<txn_number>,dat:"<command string>"}

    {cmd:"<command string>",tid:<txn_number>}
    {ctl:"<command string>",tid:<txn_number>}
    {dat:"<command string>",tid:<txn_number>}


If in doubt, test the full request object 

, or a text mode command
