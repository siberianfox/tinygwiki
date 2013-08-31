Background:

* Build 380.05 was just promoted to Master as of 8/31/13. It's been in edge for a while and tested out to be quite stable. The last code change before pushing to master was to add the RTS/CTS hardware flow control that's been in test since late July.

* The Dev branch has been moving ahead with new feature requests and aligning the TinyG Xmega code base with the ARM port currently in the G2 repository. Once the Motate hardware abstraction layer is finished for the Xmega these code bases will be merged and there will be a single code base for the Xmega and ARM TinyG variants. The Dev branch is currently at build 389.06 as of 8/31/13.

The Dev branch will be promoted to edge in early September.

There are a lot of changes between build 380.05 and 389.06. This page lists the main changes 
that affect developers and users. This page is not meant to replace the github commit messages but does provide some consolidation and a bit more information than can be found in commit messages alone. Changes that affect code internals but not functionality are not listed.

## Changes between Edge build 380.05 and Dev builds 389.06

* Footer location. The footer 

* Added robgrz G28.4 homing cycle - but this is not fully tested yet

* Fixed bug where final machine state after homing remained as "Homing" instead of "Stop"

* Motor power management

* Responses to malformed JSON Changed the response to malformed JSON to the following format. '48' is the status code for malformed JSON.

    {"r":{"msg":"{"gc":}"},"f":[1,48,8,174]}
    {"r":{"msg":"{"":}"},"f":[1,48,6,9235]}
    {"r":{"msg":"{gibberish}"},"f":[1,48,12,8007]}


Also note: blank JSON lines will return this form:

    {"r":{},"f":[1,0,8,4401]}

...or this for is you actually entered {"":""}

    {"r":{"":""},"f":[1,40,8,9345]}

