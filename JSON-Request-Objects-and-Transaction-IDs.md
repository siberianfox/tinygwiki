_This is an experimental page_

###JSON Request Objects
TinyG JSON handling has for a long time now provided an r{} response to commands entered in JSON mode. To date, there have been no request objects to wrap an incoming command to TinyG. TinyG accepts the following types of commands without any wrappers:

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

- **Command Line** Mandatory String. One of:

        {cmd:"<command>"}    command is either control or data (unidentified)
        {ctl:"<command>"}    command is a control
        {dat:"<command>"}    command is data (Gcode)

  - There must be a command in a request and there can only be one command per request
  - JSON commands may have escapes for quotes. _(When in doubt, see [jsonlint](http://jsonlint.org/))_
  - Examples:

          {cmd:"N20 G0 X111.3 Y21.0"}
          {cmd:"{\"xvm\":15000}"}

- **Transaction ID** Optional. Numeric. Format:

        {tid:<txn_ID>}

  - The transaction ID is a number from 1 - 1,000,000. Zero is considered "no ID". _(Note: range will be increased to 4 billion)_
  - If transaction ID is present it will be returned in the `r` response for that command
  - A lone tid (with no command) will simply be returned in the response when it is processed
  - Examples:

          {tid:42}
          {cmd:"{\"xvm\":15000}",tid:42}    not order dependent
          {tid:42,cmd:"{\"xvm\":15000}"}    not order dependent

- **Routing Tag** TBD

### Handling
The following describes how different types of commands are handled, and what to expect in the responses.

- **Gcode Block**

  Handling is simple. The request will be executed as Gcode. An r{} response 
    - Gcode block - will return JSON response to Gcode
    - JSON command - will return JSON response to JSON request. The incoming JSON request may be escaped for quotes. 


Valid request wrapper forms:

    {tid:<txn_number>,cmd:"<command>"}
    {tid:<txn_number>,ctl:"<command>"}
    {tid:<txn_number>,dat:"<command>"}

    {cmd:"<command>",tid:<txn_number>}
    {ctl:"<command>",tid:<txn_number>}
    {dat:"<command>",tid:<txn_number>}


If in doubt, test the full request object 

, or a text mode command
