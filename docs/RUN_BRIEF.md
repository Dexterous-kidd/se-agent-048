# Run Brief: Patchwork

## What we want
A command-line tool that fixes small bugs in a small Python project without calling any AI model. The user gives it the project folder and a failing pytest test that shows the bug. It ranks the most suspicious lines, tries small rule-based fixes and saves the first one that passes every test as a diff for a person to review.

## Users
A student or developer on a laptop with a small, trusted Python project that already has pytest tests. Output is plain text, so it works with a screen reader.

## Must do
1. Work on a copy of the project and never write inside the original folder.
2. Run the tests once and stop with a clear message if the named test does not fail.
3. Rank lines by Ochiai score, using the lines each test runs.
4. Try six fix templates on the 20 highest-ranked lines, one change at a time: swap a comparison operator, swap an arithmetic operator, shift a number or range bound by 1, negate a condition, swap "and" with "or", and add a guard for an empty or None input.
5. Accept a candidate only when the named test passes and every test that passed before still passes.
6. Save a unified diff and a report: ranked lines with scores, template used, candidates tried, candidates that did not parse, and seconds taken.
7. Stop after 200 candidates or 120 seconds and report "not fixed" with the lines tried.
8. Include an evaluation script that runs the tool on our sample project with 10 seeded bugs and prints one row per bug: ID, fixed, matches the known fix, template, candidates, seconds.

## Should do
- Open a pull request instead of only saving a diff.

## Constraints
- Python 3.12, standard library plus pytest.
- No AI at run time and no API key. Cost: $0 per use.
- Input: a folder, a pytest test name and an optional note (300 characters or fewer).
- Coverage: a pytest plugin using sys.settrace records the lines each test runs. Lines no failing test runs score 0 and are skipped. Ties sort by file name, then line number.
- Edits: find targets with the ast module, so strings and comments never change. Skip a candidate that does not parse. Restore the copy before the next candidate.
- Time: kill any test run after 10 seconds. A run that hangs, crashes or cannot collect tests counts as rejected.
- Same input gives the same ranked lines, candidate order, template and diff. Seconds may differ.
- Patchwork runs only pytest, opens no network connection and writes only inside the copy and the output folder.
- Not a sandbox: pytest runs the project's own code, so use only trusted projects such as our sample project with fictional data. Tests run with no key or token variables, a throwaway home folder and sockets blocked.

## Out of scope
Languages other than Python, projects with no tests or more than 50 Python files, untrusted repositories, bugs that need more than one change, writing the failing test for the user, a web or chat interface, and more than one failing test per run.

## Quality expectations
Separate modules for the test runner, coverage collector, fault localizer, patch generator, validator and reporter. Each step is a small function with a docstring and a unit test that checks it. Each fix template is its own function. The scoring function is tested alone with hand-worked numbers.

## Acceptance criteria
- On the sample project, the failing test for "average() crashes on an empty list" gives a ranked line list and a diff where all tests pass.
- After any run, the original project folder is unchanged.
- Running the same input twice gives the same diff.
- For the two seeded bugs no template covers, the tool reports "not fixed" and still saves its report.
- The evaluation script prints a row for each seeded bug.

## Open questions
- How many of the 10 seeded bugs do the templates fix, and how many fixes are correct?
- Is sys.settrace fast enough for the 120-second limit?
- Should the guard return a value or raise an error?
- Decided: no OS sandbox, so trusted projects only.
