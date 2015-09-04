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

###JSON Wrappers
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

### Requests and Responses
The following describes how different types of commands are handled and what to expect in the responses.

- **Gcode Block**
The request will be executed as Gcode. An r{} response will be generated with the transaction ID returned in the response. The example below shows a response with e transaction ID where JV is set to JV_FOOTER (1), but is representative of other JV settings

          request:  {tid:12345,txt:"N20 G0 X111.3 Y21.0"}
          response: {r:{tid:12345},f:[3,0,24]}
          response: {r:{},tid:12345,f:[3,0,24]}

- **JSON Command** The JSON will be executed as per usual. An r{} response will be generated with the tid returned in the response:

          request:  {tid:23456,txt:"{\"xvm\":15000}"}
          response: {r:{xvm:1500,tid:23456},f:[3,0,24]}
          response: {r:{xvm:1500},tid:23456,f:[3,0,24]}

- **Text Mode Command**  This mode introduces a new behavior, which is submission of a text-mode command in a JSON wrapper. The text-mode request will be executed as per the $ command. The response from the text-mode command will be returned in a "msg" tag with the response in the string. If the string has CR or LF in it these will returned as well, i.e. the JSON response may span multiple lines.

          request:  {tid:34567,txt:"$xvm"}

          response: {r:{msg:"[xvm] X velocity maximum    15000 mm/min
                    ",tid:12345},f:[3,0,24]}

          response: {r:{msg:"[xvm] X velocity maximum    15000 mm/min
                    "},tid:12345,f:[3,0,24]}

  - Note that the text line contains a line feed which causes the text message to span two lines 

- **Special Characters** The special characters are substituted with JSON equivalents. These are listed below:

    <pre>
    !     !:t       feedhold
    ~     ~:t       cycle start (resume)
    %     %:t       queue flush
    ^x    can:t     cancel (^x by itself is non-printable ASCII)
    </pre>

  - Note these operational differences if these commands are wrapped instead of sent as single chars:
    - An r{} response will be generated for the command (Special Characters sent as single character commands do not generate responses)
    - A tid may be included in the request and response
    - The command may be routed to a destination endpoint
    - The command will be received as a control line and processed in-turn as a control, potentially behind any previously queued controls. Note also that the act of processing the command as JSON will add approximately 5 - 10 milliseconds to the service time versus the equivalent unwrapped command.
