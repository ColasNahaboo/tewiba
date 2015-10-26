Tewiba: TEst WIth BAsh: a simple test suite, in the spirit of shell scripting

Features
========
   * Each test or series of tests is a shell script, or any executable file:
     This system is more geared to test unix programs the unix way - handling
     them via a shell and looking at the exit code and sterr, rather than
     testing only bash scripts. (hence the WIth BAsh)
   * No API to learn to write scripts. Just use the normal unix conventions:
      * Success is indicated by exiting with the 0 exit code
      * Description of problems in stderr
      * Mundane info on stdout
   * Some convenience functions are provided however: TEST, TERR, TEND, ...
   * Tewiba is a self-contained script, with no dependencies, easily 
     embeddable in your source code with a non-viral license
   * Geared for various custom tests in mixed environnements, in silent mode.
     Useful for integration tests where each test can be quite complex and
     do not additional complexity from the test framework.

For another shell test framework, more specialised in unit tests for
shell scripts, please see SHunit2 at https://github.com/kward/shunit2

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
