# Tewiba

**Tewiba**, **Te**sting **Wi**th **Ba**sh,  is a simple integration and functional test suite, in the unix spirit. It is written in bash but aims at testing any linux tools the unix way.

## Features

- Each test or series of tests is a shell script, or any executable file: This system is more geared to test unix programs the **unix way** - handling them via a shell and looking at the exit code and stderr, rather than testing only bash scripts. (hence the WIth BAsh). Tewiba just runs programs and observe them externally, it does not require to insert code in them, so it works with anything.
- No API to learn to write scripts. No [DSL](https://en.wikipedia.org/wiki/Domain-specific_language). Just use the **normal unix conventions**:
  - Success is indicated by exiting with the 0 exit code
  - Description of problems in stderr
  - Mundane info on stdout 
- Does not try to **reinvent the wheel**. If you want to write sophisticated rules to test the results, there is already all the power of Bash, no need to invent another language, like most test suite do.
- Some **convenience** functions are provided however: TEST, TERR, TEND, ...
- Tewiba is geared towards **integration and functional testing**: complex, large grain, where the difficulty is to setup the testing environment (a database, a web server, a subversion server, ...), and allows grouping tests in directories, with specialized code to set up and clean up before and after the tests. But since each test consists in running an unix program, it is not adapted for fine-grained unit testing.
- Tewiba is a **self-contained** single script, with **no dependencies**, easily **included** in your source code with a non-viral license (MIT), and executable on target systems without any installation.
- Geared for various custom tests in mixed environments, in silent mode. Useful for integration tests where each test can be quite complex and do not additional complexity from the test framework. 

For other shell test frameworks, you can look at [GitHub - dodie/testing-in-bash: Bash test framework comparison](https://github.com/dodie/testing-in-bash).

## Requirements

- An unix-like system with the GNU utilities (e.g, any Linux but not vanilla MacOS nor freebsd)
- bash at least version 4.3

## Getting Started

1. Extract the distribution somewhere, cd to it and run `./tewiba` that should exit silently, meaning all its internal tests passed.
2. Create a `tests` directory inside your project directory
3. Copy the`tewiba` shell script in it. You can also copy it somewhere  in your PATH (e.g. `/usr/local/bin`, `~/bin`)
4. Write your tests as executable files with a `.test` extension, in bash or in any language, as long as they return the number of errors as their status (0 meaning success)
5. Run `tewiba` to launch your tests. it will exit silently if everything is OK.
6. Read more info in the [Tewiba documentation](doc/tewiba-doc.md).

A simple example to test the available `date` version:

```
#!/bin/bash
[ -n "$TEWIBA" ] || exec tewiba -v "$0"  #autorun tewiba

TEST "date is installed?"
hash date || TERR date command not found

TEST "check that date is GNU date, at least version 8"
version=$(date --version)
[[ $version =~ "date (GNU coreutils)" ]] || TERR date is not the GNU version of date
[[ $version =~ "date (GNU coreutils) *"([0-9]+) ]] TERR could not determine date version
let "${BASH_REMATCH[1]} >= 8" || TERR date version is not at least v8

TEST "just check date works as intended on a simple case"
[ $(date -d @1234567890 +%Y-%m-%d) = 2009-02-13 ] || TERR date error converting time 1234567890
[ $(date -d 2009-02-13T23:31:30Z +%s) = 1234567890 ] || TERR date error converting date 2009-02-13T23:31:30Z

TEND
```

## License

© 2013-2021 Colas Nahaboo http://colas.nahaboo.net

Free to use, MIT License.

Site: https://github.com/ColasNahaboo/tewiba

## Test suite

The tests for tewiba itself are in the `tests` directory, of course. Run them by running `tewiba`. You can look at them for examples of tests, look at the `README.md` in the `tests` directory.
