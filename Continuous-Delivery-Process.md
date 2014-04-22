_This is currently for discussion only._

# Purpose for Continuous Delivery

The purpose for continuous delivery of TinyG is currently two-fold, with a third for later:

  1. Verify every push or pull-request of TinyG code compiles.
  1. Push (occasional) binaries that were compiled to the web site.
  1. (Later) Run unit-tests against every push and pull-request and report on it.

1 is handled now using Travis-CI. 3 is for the future. This page is to discuss the procedures of 2, also using Travis-CI.

As I understand it, here are the goals and restrictions of pushing binaries to the web site:

  1. We DO NOT want to push the build of every commit of ANY branch.
  1. We DO want to be able to push the build of SOME commits from ANY branch.
  1. We DO want to keep a back long of binaries. We would like this to be mostly automatic, and then we can selectively prune.


# Proposal

_Rob, April 21, 2014:_ This is my proposed _procedure_:

  1. Travis-CI will, on every commit and pull-request, verify build ONLY, and will NOT push the binary up.
  1. Upon a Tag being added to a commit or ANY BRANCH (but only in the Synthetos repo), with the format `v###.##nnn` and with a comment, then Travis-CI WILL push a built binary to the synthetos.github.io repo.
    1. In the tag format, `#` is a number from `0-9`, and `nnn` is any (option) combination of letters, "-", or numbers.
      1. Travis-CI will NOT upload the binary if the `###.##` portion does not match the `TINYG_FIRMWARE_BUILD` define from the code.
    1. Example tags would be `v120.00rc1`, `v120.01b1`, or `v120.02`. Tags could also contain (very short) descriptions: 
    1. The comment of the tag will be used as the build description on the web site, and should be as descriptive as possible.
    1. If the comment of the tag on ANY branch contains "#hidden" then the binary will be built, pushed to the synthetos.github.io repo, but the existence of the binary will be hidden from the web site. (See below for revealing technique with a specially crafted URL.)
  1. The binaries will be named with the following format: `TinyG-${TAG}-${BRANCH}.hex` _except_ for builds from master. Builds from master will simply be `TinyG-${TAG}.hex`.

# Presentation of the web site

The presentation of the binaries on the web site will be as follows:

  1. Builds from master will be listed FIRST, from newest to oldest. They will show  the version prominently, as well as the date they were uploaded. The tag comment will be shown as the description.
    1. Optional: We could have an expandable commit log (of Master only) to be used as the change log.
  1. Builds from Edge will be separated by both space and a warning that these are experimental builds an NOT SUPPORTED as well as the list itself will be collapsed by default, and a button must be clicked to expand the list.
  1. Builds from Dev OR from any branch with `#hidden` in the tag comment will NOT be shown, UNLESS you go to a specially crafted url with the query string `?show=${tag}` at the end of the url, and `${tag}` must match _exactly_ the tag of the build.
    1. http://synthetos.github.io/ will ONLY show the Master and Edge builds as described above.
    1. http://synthetos.github.io/?show=v120.00-othermill will show and highlight the build with the tag `v120.00-othermill`.

# `tinyg-binaries.json` format

The sort order of these has no bearing on the order shown on the web site. 

```JSON
{
  "NOTICE" : "Existence of a binary in this file does NOT mean that it's supported or stable.\
See http://github.com/synthetos/TinyG/wiki/tinyg-binaries.json-notes for more info.",

  "binaries": [
    {
      "branch": "master",
      "name": "tinyg-master-380.08",
      "tag": "v380.08-othermill", 
      "version": "380.08",
      "uploaded": "_date-time_",
      "description": "Comments from the tag.\
May contain multiple lines.",
      "hidden": false
    },
...
  ]
}
```