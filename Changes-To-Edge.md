Background:

* Build 380.05 was just promoted to Master as of 8/31/13. It's been in edge for a while and tested out to be quite stable. The last code change before pushing to master was to add the RTS/CTS hardware flow control that's been in test since late July.

* The Dev branch has been moving ahead with new feature requests and aligning the TinyG Xmega code base with the ARM port currently in the G2 repository. Once the Motate hardware abstraction layer is finished for the Xmega these code bases will be merged and there will be a single code base for the Xmega and ARM TinyG variants. The Dev branch is currently at build 389.06 as of 8/31/13.

The Dev branch will be promoted to edge in early September.

There are a lot of changes between build 380.05 and 389.06. This page lists the main changes 
that affect developers and users. This page is not meant to replace the github commit messages but does provide some consolidation and a bit more information than can be found in commit messages alone. Changes that affect code internals but not functionality are not listed.

## Changes between Edge build 380.05 and Dev builds 389.06


* **G28.4 homing cycle**  Not fully tested yet. Still has 0.001" offset issue

* **Motor power management** ...works differently (better). Changes:
 * Power management changes take effect immediately (not at the first move)
 * $1pm=1 works as before. Axis will energize on movement and de-energize immediately when that axis stops moving.
 * $1pm=0 energizes the axis as soo as any axis moves and leaves the axis energized until N seconds after motion stops
 * $me energizes all motors set to $1pm=0
 * $md deenergizes all motors

* **JSON Footer** The JSON footer is now at "level 0" instead of "level 1"; i.e. it is now a sibling to the "r" object not a child of the "r" object:
<pre>
    {"r":{"xvm":16000,"f":[1,0,13,1435]}}   (old way)
    {"r":{"xvm":16000},"f":[1,0,13,1435]}   (new way)
</pre>

* **Malformed JSON** Responses to malformed JSON Changed the response to malformed JSON to the following format. '48' is the status code for malformed JSON.
<pre>
    {"r":{"msg":"{"gc":}"},"f":[1,48,8,174]}
    {"r":{"msg":"{"":}"},"f":[1,48,6,9235]}
    {"r":{"msg":"{gibberish}"},"f":[1,48,12,8007]}
</pre>

Also note: blank JSON lines will return this form:

    {"r":{},"f":[1,0,8,4401]}

...or this for is you actually entered {"":""}

    {"r":{"":""},"f":[1,40,8,9345]}

* Bug Fixes
 * Fixed bug where final machine state after homing remained as "Homing" instead of "Stop"