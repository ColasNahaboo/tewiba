# Tewiba v 1.5.0 documentation
Usage: `tewiba [options] [tests...]`

Tewiba (**TE**sting **WI**th **BA**sh) is a simple test suite, in the spirit of shell scripting. It runs the tests files written in bash in arguments, or on all tests in the tests/ subdir or the current dir (default) if none given.
- Tests are given as arguments, and executed in order, they can be either:
  - an executable file.
  - a directory, all non-empty executable files in it will be run as tests. Files with names beginning with a . # _ or ending with "~" are ignored. No recursion is done, subdirs will not be explicitely scanned, except the ones ending in .subtests that will be also run.
    If a file `__INIT__` is found in the directory, it will be sourced into the shell (read) so that its definitions will be available for all the tests in it. In the same way, if an `__END__` file is found, it will be sourced after the tests.
- Nothing is printed if all tests OK. On failure, the stderr is printed.
- Tests exit code must be the number of failed tests (0 means OK).
- A warning is shown if the test was OK but printed on stderr.
- Tests are run in their directory.
- Tewiba exit code is the total number of failed tests.

## How to write tests
- Tests are executable files (in any language), that exit with code 0 if OK, and the number of failures if not. Since tewiba adds them to determine the final fails count, do not use negative status, e.g. no `exit -1`.
- Test file names can be meaningful, e.g: "Test for empty input".
- When running on a whole dir, tewiba ignores subdirectories and files beginning with `.` (a dot), `_` (underscore), and `#` (sharp), or ending with `~`, or empty, or not executable, or non-bash scripts (tewiba checks that they contain `#!/bin/bash` as their first line), containing the string `#TEWIBA_IGNORE#`, or listed via the `TEWIBA_IGNORE` function (see below).
- But any file can run as a test if explicitely given as argument to tewiba, ignoring the above restrictions that apply only when running on a whole directory.
- They must print on stderr an explanation of the error, or a warning if exit code is 0. Prints on stdout are only shonw in verbose mode.
- Tests can create temporary files and dirs prefixed by `$tmp.`, as they will be automatically cleaned afterwards by a `rm -rf $tmp $tmp.*`.
- Test data can be put in subdirectories, as subdirectories are not tested.

## Convenience functions
Although you can write test files without any tewiba-specific tools, tewiba provides some useful function to help write your tests.
Note that all tewiba functions print on a separate file descriptor than stdout and stderr, but end up on stderr at the top level (terminal).

**traps** If you redefine trap 0 in your tests, or in a script you source, you should make your trap function also call `TCLEANUP` (see below), and redo a `trap TCLEANUP 0` when you do not use a trap 0 anymore.

- `TEST message` prints the message if in verbose mode (option `-v`), that can be seen as a sub-test name (the file name is the test name itself), and use it as label in in `TERR` messages.
- `TERR message`  raises an error, but continues the tests.
- `FERR message`  fatal error: raises an error, but also aborts the current test file.
- `TEND`          MANDATORY: should terminates your test script. (Actually just an alias for: `exit $TFAILS`). This is the only function really mandatory in a test file.
- `DOTEST [options] command parameters..` A convenience function that runs the command with parameters, and compare its result(s) to the expected result(s) in options, and trigger a `TERR` for each mismatch. Returns the number of failures (0 to 3). Its options are:
  - `-l label`     prefixes TERR messages by label.
  - `-o string`    expects output to be exactly string.
  - `-O regexp`    expects output to match regexp with `[[ =~ ]]`.
  - `-f reffile`   expects output to be the contents of reffile.
  - `-c outfile`   with `-f`, on error, saves a copy of the output.
  - `into outfile`
  - `-e string`    expects stderr to be exactly string.
  - `-E regexp`    expects stderr to match regexp with `[[ =~ ]]`.
  - `-s value`     expects exit status to be value (reminder: 0 = success). If value is not a number, expects an error of any non-zero status.
  - `-S let-expr`  expects let arithmetic expression to be true, with the variable `status` holding the status.
- `TECHO [options] text`    An "echo" that prints on stderr, only in verbose mode, but that tewiba does not confuse with code errors in subtests. Options:
  - `-n`      does not terminate by a newline, as with `echo -n`.
  - `-v`      (default) only prints in verbose move (i.e. when TV == true).
  - `-f`      forces printing even in non-verbose mode.
  - `-c key`  if the output is on a terminal, colors the text as defined for key in `$TEWIBA_COLORS` key can be just the first letter(s) of the predefined keys, or any key you added to `$TEWIBA_COLORS`.
- `TINIT id...`   For each id (composed of letters, numbers, and `_` `-` `.`) ensures that files `__INIT__id` and `__END__id` are sourced, if they exist, before and after all tests of the file, after and before `__INIT__` and `__END__` respectively.
- `TLEVEL chars`  only runs if a level in chars is in the ones given by -l, or -l not specified. Each level is an alphanumeric char. TLEVEL is a simpler alternative to TCLASS below. Option:
  - `-x`      does not run if the tewiba option -l is not used.
- `TLEVEL_ONLY chars`     An alias for `TLEVEL -x` for backwards compatibility.
- `TCLASS classes...`     specify the classes of the test. Which tests are run can then be specified with the -c option of tewiba. Classes can be prefixed by `!` for negation, meaning that the test will not run if the class was specified in `-c`. TCLASS can have the option `-x` to specify that the test should not run by default (without a `-c` option to tewiba), e.g. an "xtra" test, not convenient for general use. 
- `TWEIBA_IGNORE regular expressions...` ignores the file names matching expressions (for `[[ =~ ]]`). Should be used in a `__INIT__` file to apply to the whole directory.
- `TCLEANUP` must be called at the end of the tests, to clean the temporary data. tewiba traps the signal 0 for this, so you have nothing to do if you do not use "trap" in your tests. If you redefine trap 0 in your tests, or in a script you source, you should make your trap function also call TCLEANUP, and redo a "trap TCLEANUP 0" when you do not use a trap 0 anymore.

## Provided variables
You can also use the following variables that tewiba sets:
- `$TV`           is true in tewiba verbose mode, false otherwise.
- `$tmp`          temporary data prefix (see above).
- `$nl`           is the newline character.
- `$TEWIBA_COLORS`    Colors for the tewiba messages, in the same syntax as LS_COLORS (see man dircolors), as colon-separated key=value pairs, where value is a set of color codes separated by semicolons. You can set it at will, the default being:
   `none=0:error=31:fatal=1;31:info=36:title=34:ok=32:allok=32;4:warning=35`
   You can extend it with any extra keys for use in your own TECHO statements. Try to keep the first 2 letters unique, so that you can use only them for TECHO -c.
   The standard tewiba keys are:
  - `none`      no style
  - `error`     error messages
  - `fatal`     fatal or total error messages
  - `info`      misc information (e.g. file skipped)
  - `title`     the title of a section, e.g. a test file name
  - `ok`        test result OK
  - `allok`     final result: all tests OK
  - `warning`   not a test failure, but something is fishy
- `$TEWIBA`       the version number of tewiba. Uses [semantic versioning](https://semver.org/). This allows the trick to put after the shebang `#!/bin/bash` the line:
  `[ -n "$TEWIBA" ] || exec tewiba -v "$0"`
  so that the test file can be executed standalone as it will auto-run tewiba on itself, in verbose mode.  To compare versions in bash, we include a convenience function to use in your code, see [tests/semvers2int.test](../tests/semvers2int.test).
- `$TEWIBADIR`    the original directory where tewiba was run.

## Tewiba coommand line options
 - `-v`      verbose: prints each test name as it is run, as well as its stdout.
 - `-V`      just prints tewiba version number.
 - `-x`      debug: execute each test in set -x mode copying also the output to `/tmp/tewiba.$LOGNAME.out` Note: does not imply `-v`.
 - `-l` levels    runs only tests of the levels (characters) in the string levels.
   `TLEVEL x` in a test file make it run only when given `-l y` if the reguilar expression `[y]` matches `x`. This makes ranges possible: `-l 0-3` matches a statement `TLEVEL 17a`
 - `-c class-expr...`   runs only the tests which have one class specified by TCLASS in them that matches one of the class-expr (a space or comma-separated list of class names, or `[[...]]` posix regepx) class-expr can this be a simple class name, or a regexp matching class names. Matches are made sequentially: `-c short !db` will run all short tests except the db ones, `-c medium !db short` will run all medium non-db ones, but also all the short ones, even with a db.
 - `-s`      standalone: do not read `__INIT__` nor `__END__` files.
 - `-e file` outputs the total number of errors in the file. 
 - `-f`      forces running tests files even without a TEND directive in them. You can also add this line in test files for the same result:
  `#TNOEND`
 - `-h`      This help.

## Examples
### Simple example
Testing that the screen command is installed, minmal version of a `screen-installed.test` file:
```
#!/bin/bash
hash screen || exit 1
```
But the recommended version is:

```
#!/bin/bash
[ -n "$TEWIBA" ] || exec tewiba -v "$0"
TEST screen installed
hash screen || TERR screen command not found
TEND
```
  
### Unit-testing a single function from a file
An useful trick is to have the test file copy only the definition of a bash function into a temporary scritp file, and test this script. This way the test always test the current version of the function, without having to pollute the tested script with extra code line to define an unit test.

This is done for instance in the [tests/tclass.test](../tests/tclass.test) test in the tewiba distrib. A skeleton test script is created, then the initialisation code for TCLASS is copied, then the function body. And the resulting script is tested.

### Real life examples
Some of my published code on GitHub use tewiba (not enough... I know), and you can find the tests in a `test/` subdirectory. For instance:
- [tewiba](../tests) itself, of course
- [bashoptions](https://github.com/ColasNahaboo/bashoptions/tree/main/tests)
- [cgibashoptions](https://github.com/ColasNahaboo/cgibashopts/tree/master/tests)
  
  
## History of releases
- **v1.5.0** (2021-12-21): Major rewrite, and first publication on GitHub
- v1.5.0-pre.1 (2020-04-19): Final steps to 1.5
- v1.5.0-beta.1 (2020-04-12): major code rewrite started for cleaner internals.
- v1.4 (2020-04-08): Various cleanups.
- v1.2 (2020-03-28): TCLEANUP added.
- **v1.1** (2017-09-06): First public release, on my (now defunct) public Mercurial repository).
- v1.0 (2015-09-01): Renamed to tewiba, as testshell was an existing test framework, and I like having names easily searchable in Google.
- **v0.1** (2013-08-07): Initial version, was named "testshell". Not public, only used at home and at work.

More technical details can be found in the git history.
