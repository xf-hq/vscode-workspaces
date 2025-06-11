# Test-driven development guidelines

When writing tests or being asked to review existing tests, review the code and any associated jsdoc documentation and ensure that the associated test suite is a comprehensive reflection of what the documentation outlines. If you see something being tested that hasn't been documented, ask yourself: "Does the test touch on important behaviour that is missing from the documentation?" If YES, you should update the documentation accordingly, keeping it concise and to the point, but also comprehensive and unambiguous. If NO - that is, if the test is inappropriately focusing on internal implementation details rather than outward behaviour, you can either change or remove the test, depending on what's appropriate. Testing internal implementation details is only okay if it is more of a benefit to ongoing iteration of the code than it is a hindrance to efforts to evolve or refactor the implementation. Remember, the goal is that the code works as expected when it executes. If a change to some code would maintain correct behaviour but result in breakages to tests focusing on internal implementation details, then the test probably doesn't need to exist.

## Comments

When you're writing tests, it's very important that whoever works on the code after you can clearly see what you were doing and why!

- DON'T write comments about things that are blatantly obvious just restate what the code is obviously doing.
- DO explain _why_ you're making certain implementation choices over others.
- DO make sure it's obvious why a given test is important.
- DO make sure it's easy for the next developer to quickly scan through an existing test suite and get up to speed on what it's testing and why, and what the expected behaviour of the test should be.
- DO look back over your test code and ask yourself "if I forget everything and have to review this code in the future, will it be obvious to me what is intended here and why this code works the way it does?"

## Implementing and running tests

- Tests are located in relative `tests` subdirectories within the codebase itself. If there is a file you're writing tests for, check for a `tests` subdirectory relative to the file being tested.
- Tests are written using the Bun testing API. For up to date documentation: #fetch https://bun.sh/docs/cli/test.md
- You can run tests from your terminal. Here is a copy of the bun CLI help output from `bun test -h`:

```
Usage: bun test [flags] [<patterns>]
  Run all matching test files and print the results to stdout

Flags:
      --timeout=<val>              Set the per-test timeout in milliseconds, default is 5000.
  -u, --update-snapshots           Update snapshot files
      --rerun-each=<val>           Re-run each test file <NUMBER> times, helps catch certain bugs
      --only                       Only run tests that are marked with "test.only()"
      --todo                       Include tests that are marked with "test.todo()"
      --coverage                   Generate a coverage profile
      --coverage-reporter=<val>    Report coverage in 'text' and/or 'lcov'. Defaults to 'text'.
      --coverage-dir=<val>         Directory for coverage files. Defaults to 'coverage'.
      --bail=<val>                 Exit the test suite after <NUMBER> failures. If you do not specify a number, it defaults to 1.
  -t, --test-name-pattern=<val>    Run only tests with a name that matches the given regex.
      --reporter=<val>             Specify the test reporter. Currently --reporter=junit is the only supported format.
      --reporter-outfile=<val>     The output file used for the format from --reporter.

Examples:
  Run all test files
  bun test

  Run all test files with "foo" or "bar" in the file name
  bun test foo bar

  Run all test files, only including tests whose names includes "baz"
  bun test --test-name-pattern baz
```
