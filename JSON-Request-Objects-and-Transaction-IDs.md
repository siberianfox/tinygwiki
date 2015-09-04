_This is an experimental page_

###JSON Request Objects
TinyG JSON handling has for a long time now provided an r{} response to commands entered in JSON mode. To date, there have been no request objects to wrap an incoming command to TinyG. TinyG accepts the following types of commands without any wrappers:

- Gcode block - e.g. N20 G0 X111.3 Y21.0
- JSON command - e.g. {"xvm":15000} or {x:n}
- Text command - e.g. $xvm=15000
- Special characters:  !, ~, %, ^x

This page discusses how JSON request objects support wrapping the above in JSON. The reasons for this include:

- Support a transaction ID that is independent of the command
- Allow the protocol to be extended to add qualifiers, such as 'control', 'data', etc.
- Support explicit routing of requests to specific endpoints (a form of command addressing)

###JSON Request Wrappers
The following elements can be present in a request wrapper.

- **Text Command** Mandatory. String. Format:

        {txt:"<command>"}    command is arbitrary text

  - The command is arbitrary text that may otherwise be entered without a JSON wrapper
  - The command may have escapes for quotes. _(When in doubt, see [jsonlint](http://jsonlint.org/))_
  - Examples:

          {txt:"N20 G0 X111.3 Y21.0"}
          {txt:"{\"xvm\":15000}"}
          {txt:"$x"}

- **Transaction ID** Optional. Numeric. Format:

        {tid:<txn_ID>}

  - The transaction ID is a number from 1 - 1,000,000. Zero is considered "no ID". _(Note: range will be 4 billion)_
  - If transaction ID is present it will be returned in the r{} response for that command
  - Examples:

          {txt:"{\"xvm\":15000}",tid:42}       relaxed JSON mode, not order dependent
          {"tid":42,"txt":"{\"xvm\":15000}"}   strict JSON mode, not order dependent

### Handling txt: Commands
Commands are 
The following describes how different types of commands are handled and what to expect in the responses.

- **Gcode Block**
  - The request will be executed as Gcode. 
  - An r{} response will be generated as is currently done. 
  - If a tid was provided in the request it will returned in the r{}.

- **JSON Command**
  - The JSON will be executed as per usual. 
  - An r{} response will be generated as is currently done. 
  - If a tid was provided in the request it will returned in the r{}.

- **Text Mode Command**
  - This mode introduces a new behavior, which is submission of a text-mode command in a JSON wrapper. 
  - The text-mode request will be executed as per the $ command. 
  - The response from the text-mode command will be returned in a "msg" tag with the response in the string.
  - If the string has CR or LF in it these will returned as well, i.e. the JSON response may span multiple lines.

- **Special Characters**
  - The special characters and their JSON wrappings are listed below:

    <pre>
    !     ctl:"!"     feedhold
    ~     ctl:"~"     cycle start (resume)
    %     ctl:"%"     queue flush
    ^x    ctl:"CAN"   cancel (^x by itself is non-printable ASCII)
    ^x    ctl:"can"   cancel (as above)
    </pre>

  - Note these operational differences if these commands are wrapped instead of sent as single chars:
    - An r{} response will be generated for the command (Special Characters sent as single character commands do not generate responses)
    - A tid may be included in the request and response
    - The command may be routed to a destination endpoint
    - The command will be received as a control line and processed in-turn as a control, potentially behind any previously queued controls. Note also that the act of processing the command as JSON will add approximately 5 - 10 milliseconds to the service time versus the equivalent unwrapped command.
