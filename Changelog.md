This page records changes made in the dev and edge branches. It provides a bit more information than can be found in commit messages.

## Edge Branch

## Dev Branch
#### build 385.01
Changed the response to malformed JSON to the following format. '48' is the status code for malformed JSON.

    {"r":{"msg":"{"gc":}"},"f":[1,48,8,174]}
    {"r":{"msg":"{"":}"},"f":[1,48,6,9235]}
    {"r":{"msg":"{gibberish}"},"f":[1,48,12,8007]}


Also note: blank JSON lines will return this form:

    {"r":{},"f":[1,0,8,4401]}

...or this for is you actually entered {"":""}

    {"r":{"":""},"f":[1,40,8,9345]}

