Tewiba: TEst WIth BAsh: a simple test suite, in the spirit of shell scripting

Features
========
   * Each test or series of tests is a shell script (or any executable)
   * No API to learn to write scripts. Just use the unix conventions:
      * Success is the 0 exit code
      * Description of problems in stderr
      * Mundane info on stdout
   * Some convenience functions are provided however: TEST, TERR, TEND, ...
   * A self-contained script, with no dependencies
   * Geared for various custom tests in mixed environnements, in silent mode.
     Useful for integration tests where each test can be quite complex and
     do not additional complexity from the test framework.
     For more complete frameworrks that can be desirable in for unit tests
     you can look at systems like https://github.com/kward/shunit2

QuickStart
==========

   * copy the "tewiba" script somewhere in your distrib, or put it somwhere
     in your path. It is self-contained and has no dependencies
   * decide of a dir TTT where you will put your tests, and write them there
     For instance, a subdir dir named "tests" of current dir, where you can
     put also tewiba, and run all tests by: cd tests && ./tewiba
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
