Tewiba: TEst WIth BAsh: a simple test suite, in the spirit of shell scripting

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
     we recommend https://github.com/kward/shunit2

QuickStart
==========

   * copy the "tewiba" script somewhere in your distrib, or put it somwhere
     in your path. It is self-contained and has no dependencies
   * decide of a dir TTT where you will put your tests, and write them there
     default is a subdir dir named "tests" of current dir
   * run the tests by: tewiba TTT
     For more info, run: tewiba -?

License
=======
Web site: http://colas.nahaboo.net/Software/Tewiba
Copyright (c) 2013 Colas Nahaboo (http://colas.nahaboo.net)
MIT License: http://opensource.org/licenses/MIT

Test suite
==========

You can run the tests on tewiba itself via: ./tewiba tests

TODO
====
* Web site
? options to customise output messages
