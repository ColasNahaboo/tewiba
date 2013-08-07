testshell: A simple test suite, in the spirit of shell scripting

Features
========
   * Each test or series of tests is a shell script (or any executable)
   * No API to learn to write scripts. Just use the unix conventions:
      * Success is the 0 exit code
      * Description of problems in stderr
      * Mundane info on stdout
   * A self-contained script, with no dependencies
   * Geared for various custom tests in mixed environnements, in silent mode.
     For a more formal and systematic way, such as Unit testing,
     we recommend https://code.google.com/p/shunit2/

QuickStart
==========

   * copy the "testshell" script somewhere in your distrib, or put it somwhere
     in your path. It is self-contained and has no dependencies
   * decide of a dir TTT where you will put your tests, and write them there
     default is a subdir dir named "tests" of current dir
   * run the tests by: testshell -d TTT
     For more info, run: testshell -?

License
=======
V0.1 - Web site: http://colas.nahaboo.net/Software/TestShell
Copyright (c) 2013 Colas Nahaboo (http://colas.nahaboo.net)
MIT License: http://opensource.org/licenses/MIT

TODO
====
* Doc
* Web site
? self testsuite
? options to customise output messages
