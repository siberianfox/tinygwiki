_This page applies to edge-0.97 branch only_

### JSON Request Objects
TinyG accepts the following types of commands:

- Gcode block - e.g. N20 G0 X111.3 Y21.0
- JSON command - e.g. {"xvm":15000} or {x:n}
- Text command - e.g. $xvm=15000
- Special characters:  !, ~, %, ^x

The handling of requests and responses depends on the type of command:
- JSON commands are received and returned as JSON. See [TinyG JSON Handling](JSON-Operation) for JSON details.
- Text commands are received and returned as lines of text.
- Gcode may be received:
  - As a raw gcode block on a single text line or
  - Wrapped in JSON - e.g. {"gc":"N20 G0 X111.3 Y21.0"}
  - Gcode responses are returned as text mode or JSON depending on currently active communications mode.
- Special characters are single characters that are intercepted by the serial IO system to perform feedhold, cycle start (resume), queue flush, and reset. Special characters do not generate responses.

This page discusses using a 'txt' key so that any of the above requests can be wrapped and returned as JSON. It also describes how a transaction ID can be applied to these JSON requests and their responses. The reasons for this include:

- Support a transaction ID that is independent of any data in the command
- Allow the protocol to be extended to add qualifiers, such as 'control', 'data', etc.
- Support explicit routing of requests to specific endpoints (a form of command addressing)

### JSON Text Wrapper - "txt"
The 'txt' key can be used to wrap any text that can otherwise be provided via a command line. This permits a UI to issue text mode commands without leaving JSON mode, and display responses back to a user as if they were working directly ay the TinyG command line. The format is:

    {txt:"<command>"}    command is arbitrary text

  - The command is arbitrary text that may otherwise be entered without a JSON wrapper
  - The command must be a quoted string, and may have escaped quotes - i.e. must follow valid JSON syntax
  - Examples:

          {txt:"N20 G0 X111.3 Y21.0"}     - Gcode sent as txt, relaxed JSON mode
          {"txt":"N20 G0 X111.3 Y21.0"}   - Gcode sent as txt, strict JSON mode
          {"txt":"{\"xvm\":15000}"}       - JSON sent in a JSON wrapper, strict JSON mode
          {txt:"$x"}                      - A text mode command sent in relaxed JSON mode
          {txt:"!"}                       - Feedhold sent as wrapped text

    _(When in doubt of valid JSON format, see [jsonlint](http://jsonlint.org/) or [jslint](http://www.jslint.com/))_

### Transaction ID - "tid"
A transaction ID 'tid' can be provided on a JSON line. It will be returned with the JSON response. Format is:

    {tid:<txn_ID>}    a number from 1 to 4 billion

  - The transaction ID is a number from 1 - 4,294,967,295. Zero is considered "no ID"
  - A tid tag can be anywhere in the request - it is not order dependent
  - If transaction ID is present it will be returned as a tid tag in the response for that command
  - Transaction IDs are ALWAYS returned as a top-level object in the response
  - Examples:

          Relaxed JSON mode example
            request:  {txt:"{xvm:15000}",tid:42}           
            response: {r:{xvm:15000},tid:42,f:[3,0,24]}

          Strict JSON mode examples
            request:  {"tid":42,"txt":"{\"xvm\":15000}"}
            response: {"r":{"xvm":15000},"tid":42,"f":[3,0,24]}

            request:  {"tid":31415926,"txt":"n290 g0 x10"}
            response: {"r":{"gc":"N42G0X10","n":290},"tid":31415926,"f":[3,0,24]}

## Requests and Responses using "txt" and "tid"
The following describes how different types of commands are handled and what to expect in the responses. All types of commands follow the same scheme. For the purposes of these examples we'll assume there is a non-zero tid in the request. The response will then be a JSON object with three top-level objects:

    r{} response with response data about the command
    tid echoing back the transaction ID
    f{} footer with the usual footer information

- **Gcode Block**
The request will be executed as Gcode. The response is shown below with JSON verbosity set to JV_VERBOSE (JV is maximum):

          request:  {tid:31415926,txt:"n42 g0 x10"}
          response: {"r":{"gc":"N42G0X10","n":42},"tid":31415926,"f":[3,0,24]}

- **JSON Command** The JSON will be executed as per usual. In this example the incoming JSON is a mix of relaxed and strict - output is strict. The encapsulated JSON is escaped to adhere to JSON format rules.

          request:  {tid:8675309,txt:"{\"xvm\":15000}"}
          response: {"r":{"xvm":15000},"tid":8675309,"f":[3,0,24]}

          request:  {"tid":6060842,"txt":"{\"x\":n}"}
          response: {"r":{"x":{"am":1,"vm":15000,"fr":16000,"tn":0.000,"tm":290.000,"jm":5000,"jh":10000,"jd":0.0100,"sn":1,"sx":0,"sv":3000,"lv":100,"lb":10.000,"zb":2.000}},"tid":6060842,"f":[3,0,24]}

- **Text Mode Command**  Wrapped text introduces a new behavior - allowing the submission of a text-mode command in a JSON wrapper. The wrapped text will be executed as a text-mode command. The response from the text-mode command will be returned in a "msg" tag with the response in the string. The msg string will be escaped to conform to JSON rules - i.e. if the response string has LF it (which they almost all do) LF will be returned as '\n'. The UI can then unwrap these responses into multi-line displays for the end-user.

          request:  {tid:34567,txt:"$xvm"}
          response: {"r":{"msg":"[xvm] x velocity maximum      15000 mm/min\n","tid":34567,"f":[3,0,24]}}

          request:  {tid:45678,txt:"$"}
          response: {"r":{"msg":"[fb]  firmware build            440.32\n[fv]  firmware version            0.97\n[hp]  hardware platform           1.00\n[hv]  hardware version            8.00\n[id]  TinyG ID                    3X3566-YMB\n[ja]  junction acceleration 2000000 mm\n[ct]  chordal tolerance           0.0100 mm\n[sl]  soft limit enable           0\n[st]  switch type                 1 [0=NO,1=NC]\n[mt]  motor idle timeout          2.00 Sec\n[ej]  enable json mode            2 [0=text,1=JSON]\n[jv]  json verbosity              5 [0=silent,1=footer,2=messages,3=configs,4=linenum,5=verbose]\n[js]  json serialize style        1 [0=relaxed,1=strict]\n[tv]  text verbosity              1 [0=silent,1=verbose]\n[qv]  queue report verbosity      0 [0=off,1=single,2=triple]\n[sv]  status report verbosity     1 [0=off,1=filtered,2=verbose]\n[si]  status interval           250 ms\n[ec]  expand LF to CRLF on TX     0 [0=off,1=on]\n[ee]  enable echo                 0 [0=off,1=on]\n[ex]  enable flow control         2 [0=off,1=XON/XOFF,2=RTS/CTS]\n[rxm] serial RX mode              1 [0=char_mode,1=line_mode]\n[baud] USB baud rate              5 [1=9600,2=19200,3=38400,4=57600,5=115200,6=230400]\n[gpl] default gcode plane         0 [0=G17,1=G18,2=G19]\n[gun] default gcode units mode    1 [0=G20,1=G21]\n[gco] default gcode coord system  1 [1-6 (G54-G59)]\n[gpa] default gcode path control  2 [0=G61,1=G61.1,2=G64]\n[gdi] default gcode distance mode 0 [0=G90,1=G91]\n","tid":45678,"f":[3,0,24]}}

          request:  {tid:45678,txt:"?"}
          response: {"r":{"msg":"X position:         10.000 mm\nY position:          0.000 mm\nZ position:          0.000 mm\nA position:          0.000 deg\nFeed rate:         200.000 mm/min\nVelocity:            0.000 mm/min\nUnits:               G21 - millimeter mode\nCoordinate system:   G54 - coordinate system 1\nDistance mode:       G90 - absolute distance mode\nFeed rate mode:      G94 - units-per-minute mode (i.e. feedrate mode)\nMachine state:       Stop\n","tid":45678,"f":[3,0,24]}}

- **Special Characters** The special characters are substituted with JSON equivalents. These are listed below:

    <pre>
    chr   cmd        w/tid
    !     {!:t}      {!:t,tid:42}     feedhold (shown as relaxed JSON)
    ~     {~:t}      {~:t,tid:42}     cycle start (resume)
    %     {%:t}      {%:t,tid:42}     queue flush
    ^x    {can:t}    {can:t,tid:42}   cancel (^x by itself is non-printable ASCII)
    </pre>


          request:  {tid:45678,txt:"!"}
          response: {"r":{"msg":"","tid":45678,"f":[3,0,24]}}


  - Note these operational differences if single-character commands are wrapped instead of sent as single chars:
    - A blank r{} response will be generated for the command (Special Characters sent as single character commands do not generate responses)
    - A tid may be included in the request and response
    - The command will be received as a control line and processed in-turn as a control, potentially behind any previously queued controls. Note also that the act of processing the command as JSON will add approximately 5 - 10 milliseconds to the service time versus the equivalent unwrapped command.
