# Run Brief: Patchwork

## What we want
A command-line tool that fixes small bugs in a small Python project without calling any AI model. The user gives it the project folder and a failing pytest test that shows the bug. It ranks the most suspicious lines, tries small rule-based fixes and saves the first one that passes every test as a diff for a person to review.

## Users
A student or developer on a laptop with a small, trusted Python project that already has pytest tests. They run it when a test starts failing. Output is plain text, so it works with a screen reader.

## Must do
1. Take a project folder, the name of a failing pytest test and an optional note of 300 characters or fewer. Stop with a clear message if that test does not fail.
2. Work on a copy of the project and never write inside the original folder.
3. Rank lines by Ochiai score, using the lines each test runs.
4. Try six fix templates on the 20 highest-ranked lines, one change at a time: swap a comparison operator, swap an arithmetic operator, shift a number or range bound by 1, negate a condition, swap "and" with "or", and add a guard for an empty or None input.
5. Accept a candidate only when the named test passes and every test that passed before still passes.
6. Save a unified diff and a report: ranked lines with scores, template used, candidates tried, candidates that did not parse, and seconds taken.
7. Stop after 200 candidates or 120 seconds, report "not fixed" with the lines tried if no candidate passed, and still save the report.
8. Include an evaluation script that runs the tool on our sample project with 10 seeded bugs and prints one row per bug: bug ID, fixed or not, matches the known correct fix or not, template, candidates, seconds.

## Should do
- Open a pull request on a new branch instead of only saving a diff.

## Constraints
- Python 3.12, standard library plus pytest. No web server, no database.
- No AI at run time: no model calls and no API key. Cost: $0 per use.
- Coverage: a small pytest plugin using sys.settrace records the lines each test runs. Lines no failing test runs score 0 and are skipped. Ties sort by file name, then line number.
- Edits: find targets with the ast module, so strings and comments never change. Skip a candidate that does not parse. Restore the original text before the next candidate.
- Time: kill any single test run after 10 seconds and count it as rejected.
- Same input gives the same ranked lines, candidate order, template and diff. Seconds may differ.
- Patchwork runs only pytest, opens no network connection and writes only inside the copy and the output folder.
- Not a sandbox: pytest runs the project's own code, so use only trusted projects such as our sample project with fictional data. Tests get no environment variables holding keys or tokens.
- MIT license, like se-agent.

## Out of scope
Languages other than Python, projects with no tests or more than 50 Python files, untrusted repositories, bugs that need more than one change, writing the failing test for the user, a web or chat interface, and more than one failing test per run.

## Quality expectations
Separate modules for the test runner, coverage collector, fault localizer, patch generator, validator and reporter. Each step is a small function with a docstring and a unit test of what the docstring promises. Each fix template is its own function. The scoring function is tested alone with hand-worked numbers.

## Acceptance criteria
- On the sample project, the failing test for "average() crashes on an empty list" gives a ranked line list and a diff where all tests pass.
- After any run, the original project folder is unchanged.
- Running the same input twice gives the same diff.
- Two of the 10 seeded bugs are kinds no template covers. For those, the tool reports "not fixed" and still saves its report.
- The evaluation script prints a results table for all 10 seeded bugs.
