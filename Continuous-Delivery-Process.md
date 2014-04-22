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

_Rob, April 21, 2014:_ This is my proposed procedure:

  1. Travis-CI will, on every commit and pull-request, verify build ONLY, and will NOT push the binary up.
  1. Upon a Tag being added to a commit or ANY BRANCH (but only in the Synthetos repo), with the format "v###.##nnn" and with a comment, then Travis-CI WILL push a built binary to the synthetos.github.io repo.
    1. In the tag format, "#" is a number from 0-9, and nnn is any (option) combination of letters or numbers.
    1. An example tag would be "v120.00rc1".
    1. The comment of the tag will be used as the build description on the web site, and should be as descriptive as possible.
    1. If the comment of the tag starts with the word "Hidden" then the binary will be built, push to the synthetos.github.io repo, but the existence of the binary will be hidden from the we site. (TBD: Determine how to show it. Special URL to send a person to?)
