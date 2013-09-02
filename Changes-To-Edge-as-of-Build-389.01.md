Background:

* Build 380.05 which was previously in edge was just promoted to Master as of 8/31/13. It's been in edge for a while and tested out to be quite stable. The last code change before pushing to master was to add the RTS/CTS hardware flow control that's been in test since late July.

* The Dev branch has been moving ahead with new feature requests and aligning the TinyG Xmega code base with the ARM port currently in the G2 repository. Once the Motate hardware abstraction layer is finished for the Xmega these code bases will be merged and there will be a single code base for the Xmega and ARM TinyG variants. The Dev branch is currently at build 391.01 as of 9/2/13.

The Dev branch will be promoted to edge in early September.

There are a lot of changes between build 380.05 and 391.01. This page lists the main changes 
that affect developers and users. This page is not meant to replace the github commit messages but does provide some consolidation and a bit more information than can be found in commit messages alone. Changes that affect code internals but not functionality are not listed.

## Changes between Edge build 380.05 and Dev build 391.01

* **JSON Footer** - The JSON footer is now at "level 0" instead of "level 1"; i.e. it is now a sibling to the "r" object not a child of the "r" object:
<pre>
    {"r":{"xvm":16000,"f":[1,0,13,1435]}}            old footer format
    {"r":{"xvm":16000},"f":[1,0,11,6442]}            new footer format
</pre>

* **Malformed JSON** - Responses to malformed JSON changed to the following: An "err" string returned with the original string, and a status code of 48 indicating malformed JSON. See [Status Codes](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Codes) for [tinyg status codes](https://github.com/synthetos/TinyG/wiki/TinyG-Status-Codes). Any quotes in the error string are escaped. Also note the new response for for blank JSON lines
<pre>
    {"r":{"err":"{\"gc\"}"},"f":[1,48,7,5466]}       response to {"gc"}
    {"r":{"err":"{gibberish}"},"f":[1,48,12,4462]}   response to {gibberish}
    {"r":{"err":"{"},"f":[1,48,2,9860]}              response to a lone {

    {"r":{"":""},"f":[1,40,8,9345]}                  response to {"":""}: valid JSON but null command
    {"r":{"xvd":1},"f":[1,40,10,8992]}               response to {"xvd":1} valid JSON but unrecognized command (Status code 40)
</pre>

* **Status report can contain up to 30 NV pairs**  - The caveat is that the total length of the longest SR cannot exceed 512 characters, all inclusive (curlies, LFs, etc). This should be a problem but please keep and eye out for this.

* **G28.4 homing cycle - Experimental code**  - This is code Rob Grzesek (robgrz) and I have been working on. The function is to do a homing cycle without a final zero in the axis. It's Not fully tested yet, and still has an issue where about 0.001" is added to the displayed distance even though the measured distance seems to be accurate. 

* **G38.2 probing cycle - Experimental code**  - This is code Rob added for probing that is still being shaken out. We need a larger discussion of what's needed for probing. So far we have the following cases that people want to do:
 * Touch off for Z setting and/or work location
 * Machine zeroing using a center finder technique (electric ring) instead of homing switches
 * Probing primitives to support Z axis leveling for PCB milling or other functions

* Bug Fixes
 * Fixed bug where final machine state after homing remained as "Homing" instead of "Stop"

This was already in EDGE, but it's useful to know about:

* **Motor power management** ...works differently (better). Changes:
 * Power management changes take effect immediately (not at the first move)
 * $1pm=1 works as before. Axis will energize on movement and de-energize immediately when that axis stops moving.
 * $1pm=0 energizes the axis as soon as any axis moves and leaves the axis energized until N seconds after motion stops
 * $me energizes all motors set to $1pm=0
 * $md deenergizes all motors
