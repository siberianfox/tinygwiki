This page discusses how to test TinyG to make sure everything is working properly.
_(Obviously this page is still in work)_

You can use the built-in tests to check that TinyG is working properly. Type $test for a list of tests.

$test=1 is the basic test that will show if the motors and output bits are running. This test is pest performed with motors that are not installed in a machine. Other tests are for installed machines.

From here you may want to look at 
* [TinyG Configuration](TinyG-Configuration) 
* [TinyG Tuning](TinyG-Tuning)
* [Sending Gcode files to TinyG](TinyG-Sending-Files)

##Self Tests

The following self tests can be run from the command line. Format is $test=1 for text mode, {test:1} for JSON mode
<pre>
  $test=1  smoke test    (best performed on a machine with a large table or motors not connected to a machine)
  $test=2  homing test   (you must trip homing switches)
  $test=3  square test   (a series of squares)
  $test=4  arc test      (some large circles)
  $test=5  dwell test    (moves spaced by 1 second dwells)
  $test=6  feedhold test (enter ! and ~ to hold and restart, respectively)
  $test=7  M codes test  (M codes intermingled with moves)
  $test=8  JSON test     (motion test run using JSON commands)
  $test=9  inverse time test
  $test=10 rotary motion test
  $test=11 small moves test
  $test=12 slow moves test
  $test=13 coordinate system offset test (G92, G54-G59)
</pre>