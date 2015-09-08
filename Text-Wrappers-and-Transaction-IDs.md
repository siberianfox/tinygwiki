_This is an experimental page_

###JSON Request Objects
TinyG accepts the following types of commands:

- Gcode block - e.g. N20 G0 X111.3 Y21.0
- JSON command - e.g. {"xvm":15000} or {x:n}
- Text command - e.g. $xvm=15000
- Special characters:  !, ~, %, ^x

The handling of requests and responses depends on the type of command:
- JSON commands are received and returned as JSON. See [TinyG JSON Handling](JSON-Operation) for JSON details.
- Text commands are received and returned as text. 
- Gcode may be received as raw gcode or wrapped in JSON (e.g. {"gc":"N20 G0 X111.3 Y21.0"}  ). Gcode responses are provided as JSON or text depending on current mode.
- Special characters are single characters that are intercepted by the serial IO system to perform feedhold, cycle start (resume), queue flush, and reset. Special characters do not generate responses.

This page discusses using a 'txt' key so that all requests can be wrapped and responded in JSON. It also describes how a transaction ID can be applied to these JSON request objects and their responses. The reasons for this include:

- Support a transaction ID that is independent of the command
- Allow the protocol to be extended to add qualifiers, such as 'control', 'data', etc.
- Support explicit routing of requests to specific endpoints (a form of command addressing)

###JSON Text Wrapper - "txt"
The 'txt' key can be used to wrap any text that can otherwise be provided via a command line. This permits a UI to issue text mode commands without leaving JSON mode, and display responses back to a user as if they were working directly ay the TinyG command line. The format is:

    {txt:"<command>"}    command is arbitrary text

  - The command is arbitrary text that may otherwise be entered without a JSON wrapper
  - The command must be a quoted string, and may have escaped quotes - i.e. must follow valid JSON syntax
  - Examples:

          {txt:"N20 G0 X111.3 Y21.0"}
          {txt:"{\"xvm\":15000}"}
          {txt:"$x"}
          {txt:"!"}

    _(When in doubt of valid JSON format, see [jsonlint](http://jsonlint.org/))_

###Transaction ID - "tid"
A transaction ID 'tid' can be provided on a JSON line. It will be returned with the JSON response. Format is:

    {tid:<txn_ID>}    a number from 1 to 4 billion

  - The transaction ID is a number from 1 - 4,000,000,000. Zero is considered "no ID".
  - If transaction ID is present it will be returned in the response for that command.
  - Examples:

          {txt:"{xvm:15000}",tid:42}           relaxed JSON mode, not order dependent
          {"tid":42,"txt":"{\"xvm\":15000}"}   strict JSON mode, not order dependent

## Requests and Responses using "txt" and "tid"
The following describes how different types of commands are handled and what to expect in the responses. All types of commands follow the same scheme. For the purposes of these examples we'll assume thre is a non-zero tid in the request. The response will then be a JSON object with three top-level objects:

    r{} response with response data about the command
    tid echoing back the transaction ID
    f{} footer with the usual footer information 

- **Gcode Block**
The request will be executed as Gcode. The response is shown below with JSON verbosity (JV) set to JV_VERBOSE (maximum):

          request:  {tid:31415926,txt:"n42 g0 x10"}
          response: {"r":{"gc":"N42G0X10","n":42},"tid":31415926,"f":[3,0,24]}

- **JSON Command** The JSON will be executed as per usual. In this example the incoming JSON is a mix of relaxed and strict - output is strict. The encapsulated JSON is escaped to adhere to JSON format rules. 

          request:  {tid:23456,txt:"{\"xvm\":15000}"}
          response: {"r":{"xvm":15000},"tid":23456,"f":[3,0,24]}

- **Text Mode Command**  This mode introduces a new behavior, which is submission of a text-mode command in a JSON wrapper. The text-mode request will be executed as per the $ command. The response from the text-mode command will be returned in a "msg" tag with the response in the string. If the string has CR or LF in it these will returned as well, i.e. the JSON response may span multiple lines.

          request:  {tid:34567,txt:"$xvm"}

          response: {r:{msg:"[xvm] X velocity maximum    15000 mm/min
                    ",tid:12345},f:[3,0,24]}

  - Note that the text line contains a line feed which causes the text message to span two lines 

- **Special Characters** The special characters are substituted with JSON equivalents. These are listed below:

    <pre>
    chr   cmd        w/tid
    !     {!:t}      {!:t,tid:42}     feedhold (shown as relaxed JSON)
    ~     {~:t}      {~:t,tid:42}     cycle start (resume)
    %     {%:t}      {%:t,tid:42}     queue flush
    ^x    {can:t}    {can:t,tid:42}   cancel (^x by itself is non-printable ASCII)
    </pre>

  - Note these operational differences if these commands are wrapped instead of sent as single chars:
    - An r{} response will be generated for the command (Special Characters sent as single character commands do not generate responses)
    - A tid may be included in the request and response
    - The command will be received as a control line and processed in-turn as a control, potentially behind any previously queued controls. Note also that the act of processing the command as JSON will add approximately 5 - 10 milliseconds to the service time versus the equivalent unwrapped command.