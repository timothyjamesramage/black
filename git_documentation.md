# Git Documentation

## Commit Messages

| Commit Message | Number of Commits |
| --- | --- |
| add change log entry | 3 |
| Add new release template | 2 |
| Fix changelog entries in the wrong release (#2825) | 2 |
| fix doc generation | 2 |
| [pre-commit.ci] pre-commit autoupdate (#4297) | 1 |
| Improve AST safety check (#4290)

Fixes #4288, regressed by #4270 | 1 |
| fix: Stop moving multiline strings to a new line unless inside brackets (#4289)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com> | 1 |
| Remove mocking from tests (#4287)

Fixes #4275 | 1 |
| Fix two logging calls in the test helper (#4286)

They were missing formatting interpolation operators. | 1 |
| Bump pypa/cibuildwheel from 2.16.5 to 2.17.0 (#4283)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.16.5 to 2.17.0.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.16.5...v2.17.0)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix formatting for `if` clauses in `match-case` blocks (#4269)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Add new change template | 1 |
| Prepare release 24.3.0 (#4279) | 1 |
| Fix catastrophic performance in lines_with_leading_tabs_expanded() (#4278) | 1 |
| Fix --line-ranges behavior when ranges are at EOF (#4273)

Fixes #4264 | 1 |
| Use regex where we ignore case on windows (#4252)

On windows the path `FoObAR` is the same as `foobar`, so the output
of `black` on a windows machine could output the path to `.gitignore`
with an upper or lower-case drive letter. | 1 |
| Fix 4227: Improve documentation for --quiet --check (#4236)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| update plugin url for Thonny (#4259) | 1 |
| Fix AST safety check false negative (#4270)

Fixes #4268

Previously we would allow whitespace changes in all strings, now
only in docstrings.

Co-authored-by: Shantanu <12621235+hauntsaninja@users.noreply.github.com> | 1 |
| Ensure `blib2to3.pygram` is initialized before use (#4224) | 1 |
| fix: Don't move comments while splitting delimiters (#4248)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com> | 1 |
| Make trailing comma logic more concise (#4202)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com> | 1 |
| chore: Refactor `delimiter_split()` (#4257)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com> | 1 |
| Remove usage of `pkg_resources` in `docs/conf.py` (#4251) | 1 |
| Update empty line documentation (#4239)

Reflects status quo following #4043

Fixes #4238 | 1 |
| Add new release template (#4228) | 1 |
| Prepare release 24.2.0 (#4226) | 1 |
| fix: Don't remove comments along with parens (#4218)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com> | 1 |
| Bump pre-commit/action from 3.0.0 to 3.0.1 (#4225)

Bumps [pre-commit/action](https://github.com/pre-commit/action) from 3.0.0 to 3.0.1.
- [Release notes](https://github.com/pre-commit/action/releases)
- [Commits](https://github.com/pre-commit/action/compare/v3.0.0...v3.0.1)

---
updated-dependencies:
- dependency-name: pre-commit/action
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix ignoring input files for symlink reasons (#4222)

This relates to #4015, #4161 and the behaviour of os.getcwd()

Black is a big user of pathlib and as such loves doing `.resolve()`,
since for a long time it was the only good way of getting an absolute
path in pathlib. However, this has two problems:

The first minor problem is performance, e.g. in #3751 I (safely) got rid
of a bunch of `.resolve()` which made Black 40% faster on cached runs.

The second more important problem is that always resolving symlinks
results in unintuitive exclusion behaviour. For instance, a gitignored
symlink should never alter formatting of your actual code. This kind of
thing was reported by users a few times.

In #3846, I improved the exclusion rule logic for symlinks in
`gen_python_files` and everything was good.

But `gen_python_files` isn't enough, there's also `get_sources`, which
handles user specified paths directly (instead of files Black
discovers). So in #4015, I made a very similar change to #3846 for
`get_sources`, and this is where some problems began.

The core issue was the line:
```
root_relative_path = path.absolute().relative_to(root).as_posix()
```
The first issue is that despite root being computed from user inputs, we
call `.resolve()` while computing it (likely unecessarily). Which means
that `path` may not actually be relative to `root`. So I started off
this PR trying to fix that, when I ran into the second issue. Which is
that `os.getcwd()` (as called by `os.path.abspath` or `Path.absolute` or
`Path.cwd`) also often resolves symlinks!
```
>>> import os
>>> os.environ.get("PWD")
'/Users/shantanu/dev/black/symlink/bug'
>>> os.getcwd()
'/Users/shantanu/dev/black/actual/bug'
```
This also meant that the breakage often would not show up when input
relative paths.

This doesn't affect `gen_python_files` / #3846 because things are always
absolute and known to be relative to `root`.

Anyway, it looks like #4161 fixed the crash by just swallowing the error
and ignoring the file. Instead, we should just try to compute the actual
relative path. I think this PR should be quite safe, but we could also
consider reverting some of the previous changes; the associated issues
weren't too popular.

At the same time, I think there's still behaviour that can be improved
and I kind of want to make larger changes, but maybe I'll save that for
if we do something like #3952

Hopefully fixes #4205, fixes #4209, actual fix for #4077 | 1 |
| Simplify check for symlinks that resolve outside root (#4221)

This PR does not change any behaviour.

There have been 1-2 issues about symlinks recently. Both over and under
resolving can cause problems. This makes a case where we resolve more
explicit and prevent a resolved path from leaking out via the return. | 1 |
| Remove redundant parentheses in `case` statement `if` guards (#4214)

A follow up to #4024 but for `if` guards in `case` statements. I noticed this
when #4024 was made stable, and noticed I had some code that had extra parens
around the `if` guard. | 1 |
| fix: bug where the doublestar operation had inconsistent formatting. (#4154)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| fix: additional newline added to docstring when the previous line length is less than the line length limit minus 1 (#4185)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump furo from 2023.9.10 to 2024.1.29 in /docs (#4211)

Bumps [furo](https://github.com/pradyunsg/furo) from 2023.9.10 to 2024.1.29.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2023.09.10...2024.01.29)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump pypa/cibuildwheel from 2.16.4 to 2.16.5 (#4212) | 1 |
| docs: Refactor pycodestyle/Flake8 compatibility docs (#4194)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com>
Co-authored-by: Shantanu <12621235+hauntsaninja@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Move hug_parens_with_braces_and_square_brackets into the unstable style (#4198)

Primarily because of #4036 (a crash) but also because of the feedback
in #4098 and #4099. | 1 |
| Ignore pyproject.toml missing tool.black section (#4204)

Fixes #2863

This is pretty desirable in a monorepo situation where you have
configuration in the root since it will mean you don't have to
reconfigure every project.

The good news for backward compatibility is that `find_project_root`
continues to stop at any git or hg root, so in all cases repo root
coincides with a pyproject.toml missing tool.black, we'll continue to
have the project root as before and end up using default config
(i.e. we're unlikely to randomly start using the user config).

The other thing we need to be a little careful about is that changing
find_project_root logic affects what `exclude` is relative to.  Since we
only change in cases where there is no config, this only applies where
users were using `exclude` via command line arg (and had pyproject.toml
missing tool.black in a dir that was not repo root).

Finally, for the few who could be affected, the fix is to put an empty
`[tool.black]` in pyproject.toml | 1 |
| fix: minor issue with schemastore part of script (#4195)

Signed-off-by: Henry Schreiner <henryschreineriii@gmail.com> | 1 |
| Test that preview/unstable features are documented (#4187)

In #4096 I added a list of current preview/unstable features to the docs. I think
this is important for publicizing what's in our preview style. This PR adds an
automated test to ensure the list stays up to date in the future. | 1 |
| Bump peter-evans/find-comment from 2.4.0 to 3.0.0 (#4190)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 2.4.0 to 3.0.0.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/a54c31d7fa095754bfef525c0c8e5e5674c4b4b1...d5fe37641ad8451bdd80312415672ba26c86575e)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump peter-evans/create-or-update-comment from 3.1.0 to 4.0.0 (#4192)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 3.1.0 to 4.0.0.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/23ff15729ef2fc348714a3bb66d2f655ca9066f2...71345be0265236311c031f5c7866368bd1eff043)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| feat: add schema and validate-pyproject support (#4181)

Signed-off-by: Henry Schreiner <henryschreineriii@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump pypa/cibuildwheel from 2.16.2 to 2.16.4 (#4191)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.16.2 to 2.16.4.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.16.2...v2.16.4) | 1 |
| Swallow warnings when performing AST checks (#4189)

Fixes #4188 | 1 |
| Prepare release 24.1.1 (#4186) | 1 |
| chore: ignore node_modules (produced by a pre-commit check) (#4184)

Signed-off-by: Henry Schreiner <henryschreineriii@gmail.com> | 1 |
| Consistently add trailing comma on typed parameters (#4164)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix missing space in option description (#4182) | 1 |
| Fix cache file length (#4176)

- Ensure total file length stays under 96
- Hash the path only if it's too long
- Proceed normally (with a warning) if the cache can't be read

Fixes #4172 | 1 |
| New changelog | 1 |
| Prepare release 24.1.0 (#4170) | 1 |
| Add --unstable flag (#4096) | 1 |
| Show warning on invalid toml configuration (#4165)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Describe 2024 module docstring more accurately (#4168) | 1 |
| Simplify code in lines.py (#4167)

This has been getting a little messy. These changes neaten things up, we
don't have to keep guarding against `self.previous_line is not None`, we
make it clearer what logic has side effects, we reduce the amount of
code that tricky `before` could touch, etc | 1 |
| Remove reference (#4169)

This is out-of-date and just a chore. I don't think this is useful to
contributors and Black doesn't even have a public API. | 1 |
| fix: Don't normalize whitespace before fmt:skip comments (#4146)

Signed-off-by: RedGuy12 <paul@reid-family.org> | 1 |
| Create the 2024 stable style (#4106) | 1 |
| fix pathlib exception handling with symlinks (#4161)

Fixes #4077 | 1 |
| Bump actions/cache from 3 to 4 (#4162)

Bumps [actions/cache](https://github.com/actions/cache) from 3 to 4.
- [Release notes](https://github.com/actions/cache/releases)
- [Changelog](https://github.com/actions/cache/blob/main/RELEASES.md)
- [Commits](https://github.com/actions/cache/compare/v3...v4)

---
updated-dependencies:
- dependency-name: actions/cache
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix unnecessary nesting when wrapping long dict (#4135)

Fixes #4129 | 1 |
| Update using_black_with_other_tools.md to ensure flake8 configuration examples are consistant (#4157) | 1 |
| fix: Don't allow unparenthesizing walruses (#4155)

Signed-off-by: RedGuy12 <61329810+RedGuy12@users.noreply.github.com>
Signed-off-by: RedGuy12 <paul@reid-family.org> | 1 |
| Docs: Add note on `--exclude` about possibly verbose regex (#4145)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Make `blank_line_after_nested_stub_class` work for methods (#4141)

Fixes #4113

Authored by dhruvmanila | 1 |
| Fix comment handling when parenthesising conditional expressions (#4134)

Fixes #3555 | 1 |
| Remove is_function_or_class helper footgun (#4133)

This is a no-op change.

That function was not a good way to tell if something is a function or a
class, since it basically only worked for async functions by accident
(the parent of a suite / simple_stmt of async function body is a
funcdef). | 1 |
| Clean up dead code in magic trailing comma logic (#4131) | 1 |
| Remove empty lines before docstrings in async functions (#4132) | 1 |
| Revert "confine pre-commit to stages (#3940)" (#4137)

This reverts commit 7686989fc89aad5ea235a34977ebf8c81c26c4eb. | 1 |
| Allow empty lines at beginnings of more blocks (#4130)

Fixes #4043, fixes #619

These include nested functions and methods.

I think the nested function case quite clearly improves readability. I
think the method case improves consistency, adherence to PEP 8 and
resolves a point of contention. | 1 |
| [pre-commit.ci] pre-commit autoupdate (#4139) | 1 |
| Unify docstring detection (#4095)

Co-authored-by: hauntsaninja <hauntsaninja@gmail.com> | 1 |
| Do not round cache mtimes (#4128)

Fixes #4116

This logic was introduced in #3821, I believe as a result of copying
logic inside mypy that I think isn't relevant to Black | 1 |
| Treat walruses like other binary operators in subscripts (#4109)

Fixes #4078 | 1 |
| Fix nits, chain comparisons, unused params, hyphens (#4114) | 1 |
| Add new changelog template (#4125) | 1 |
| Prepare release 23.12.1 (#4124) | 1 |
| Adds paren to deps for hidden extra constraint (#4108)

Fix #4107 | 1 |
| Add new changelog template | 1 |
| Prepare release 23.12.0 (#4105) | 1 |
| Fix feature detection for parenthesized context managers (#4104) | 1 |
| Fix another case where we format dummy implementation for non-functions/classes (#4103) | 1 |
| Fix path in test message (#4102) | 1 |
| Only use dummy implementation logic for functions and classes (#4066)

Fixes #4063 | 1 |
| Bump actions/setup-python from 4 to 5 (#4101) | 1 |
| Add dedicated preview feature for East Asian Width (#4097) | 1 |
| Allow empty lines at beginning of blocks (again) (#4060) | 1 |
| docs: Move `fmt: off` docs (#4090) | 1 |
| docs: Unify option descriptions between `--help` and `the_basics.md` (#4076) | 1 |
| docs: Clarify include/exclude documentation (#4072) | 1 |
| test preview cases with line-length 1 unless explicitly skipped (#4087)

* Add new flag for tests, --no-preview-line-length-1, to be used for test cases known to not work in preview mode with line-length=1. Also split out the problematic cases in three cases to separate files. Removed now redundant file which explicitly tested preview annotations with line-length=1

* mode.preview -> preview_mode, mark pep_572_remove_parens as failing with ll1 | 1 |
| fix crash in preview mode with --line-length=1 (#4086) | 1 |
| Fix:  --line-ranges dedents a # fmt: off in the middle of a decorator (#4084)

Fixes #4068 | 1 |
| Fix minor typos in docstrings (#4085) | 1 |
| Build mypycified wheels for Python 3.12 (#4070) | 1 |
| Bump mypy to 1.7.1 (#4069) | 1 |
| Prefer more equal signs before a break when splitting chained assignments (#4010)

Fixes #4007 | 1 |
| Run lint job on Ubuntu only (#4061) | 1 |
| Disable the stability check with --line-ranges for now. (#4034)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Permit standalone form feed characters at the module level (#4021)

Co-authored-by: Stephen Morton <git@tungol.org>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| [docker ci] Revert "parallel" builds in seperate actions (#4057)

- Broke tagging images together
- Saved only a few mins
  - x86_64 build is fast, time is all spent on cross compile of arm64
- Also remove evil copy pasta ... which is nice

Was worth an attempt. | 1 |
| [docker ci] Split up amd64 (x86_64) and arm64 builds (#4054)

* [docker ci] Split up amd64 (x86_64) and arm64 builds

- Lets run them seperately to cut down total time
- Will also more clearly show if either arch has specific problems
- Kept amd64 (x86_64) using qemu actions so if GitHub ever offers arm64 boxes it could stay working too

Fixes #3971

* Add CHANGES entry

---------

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| [ci] Move 'lint' to 3.12 (#4053)

- Add to run on MacOS + Windows too
- Do not install [d] dependecies as blackd is not actually run / checked
- Move to default GitHub action version - which is 3.12 today | 1 |
| [docker] Build with 3.12 image (#4055)

Test:
```
crl-m1:black cooper$ docker build --tag black_3_12 .
...
 => [stage-1 2/2] COPY --from=builder /opt/venv /opt/venv                                                                                                                                                  0.2s
 => exporting to image                                                                                                                                                                                     0.1s
 => => exporting layers                                                                                                                                                                                    0.1s
 => => writing image sha256:bd66acc9d76d2c40d287b0684ce6601401631e0468204c4e6a81f8f1eebaf1dd                                                                                                               0.0s
 => => naming to docker.io/library/black_3_12

crl-m1:black cooper$ docker image ls | grep black_3_12
black_3_12                     latest            bd66acc9d76d   59 seconds ago   193MB
``` | 1 |
| Make black[d] install + test run with 3.12 (#4035)

* Make black[d] install + test run with 3.12

- With aiohttp >= 3.9.0 we can now install all dependencies with 3.12
- Add actions to run 3.12
- Lint still needs to be 3.11

Test:
- `python3.12 -m venv /tmp/tb --upgrade-deps`
- `/tmp/tb/bin/pip install tox`
- `/tmp/tb/bin/pip install .[d]`
- `/tmp/tb/bin/tox -e py312`
```
  py312: OK (37.61=setup[3.98]+cmd[3.83,0.36,19.54,6.46,3.00,0.44] seconds)
  congratulations :) (37.63 seconds)
```

* Move to pypy-3.9

---------

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Handle more huggable immediately nested parens/brackets. (#4012)

Fixes #4011 | 1 |
| Upgrade mypy to 1.6.1 (#4049) | 1 |
| Make flake8 pass when run with Python 3.12 (#4050) | 1 |
| Block aiohttp==3.9.0 from being installed in CI on Windows/pypy (#4051) | 1 |
| Document target version inference (#4048) | 1 |
| Improve annotations for `black.concurrency.cancel` (#4047) | 1 |
| Prepare release 23.11.0 (#4032) | 1 |
| Remove redundant condition from `has_magic_trailing_comma` (#4023)

The second `if` cannot be true at its execution point, because it is
already covered by the first `if`. The condition
`comma.parent.type == syms.subscriptlist` always holds if
`closing.parent.type == syms.trailer` holds, because `subscriptlist`
only appears inside `trailer` in the grammar:

```
trailer: '(' [arglist] ')' | '[' subscriptlist ']' | '.' NAME
subscriptlist: (subscript|star_expr) (',' (subscript|star_expr))* [',']
``` | 1 |
| Preserve visible quote types for f-string debug expressions (#4005)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| docs: fix minor typo (#4030)

Replace "E950" with "B950" | 1 |
| Apply force exclude logic before symlink resolution (#4015) | 1 |
| [563] Fix standalone comments inside complex blocks crashing Black (#4016)

Bracket depth is not an accurate indicator of standalone comment position inside more complex blocks because bracket depth can be virtual (in loops' and lambdas' parameter blocks) or from optional parens. Here we try to stop cumulating lines upon standalone comments in complex blocks, and try to make standalone comment processing more simple. The fundamental idea is, that if we have a standalone comment, it needs to go on its own line, so we always have to split.

This is not perfect, but at least a first step. | 1 |
| Fix long case blocks not split into multiple lines (#4024)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Support formatting specified lines (#4020) | 1 |
| Fix crash with f-string docstrings (#4019)

Python does not consider f-strings to be docstrings, so we probably
shouldn't be formatting them as such

Fixes #4018

Co-authored-by: Alex Waygood <Alex.Waygood@Gmail.com> | 1 |
| Preview: Keep requiring two empty lines between module-level docstring and first function or class definition (#4028)

Fixes #4027. | 1 |
| Fix arm wheels on macOS (#4017) | 1 |
| Enable branch coverage (#4022)

When trying to understand the code logic, and looking at coverage
reports, branch coverage is very helpful. | 1 |
| Fix crash on await (a ** b) (#3994) | 1 |
| Minor refactoring in get_sources and gen_python_files (#4013) | 1 |
| Fix bytes strings being treated as docstrings (#4003)

Fixes #4002 | 1 |
| Produce equivalent code for docstrings containing backslash followed by whitespace(s) before newline (#4008)

Fixes #3727 | 1 |
| Hug parens also with multiline unpacking (#3992) | 1 |
| Add release tool (#3974)

* Add release tool

- Add tool for release managers to use to generate commits
  - I'm trying to only use stdlib so we have no depdencies ...
- Default is to change date strings in hard coded documentation files + CHANGES.md
  - I write directly to files cause we have SCM to fix any screw ups ...
- We hackily convert calver to ints to sort (all for better ideas here)
  - If we hit a ValueError we just set to 0 for sorting - This is alhpa + beta release we can safely ignore these days
- Add new CI to only run release unittests in 3.12 only on all platforms
- Update release docs

- Checked with `mypy --strict` + ensure we are `black --preview` formatted :D

Tests:
- Run it to generate template PR
  - `python3.12 release.py --debug --add-changes-template`
- Run it to cleanup CHANGE.md + change version in specified doc files
```
crl-m1:black cooper$ python3.12 release.py -d
[2023-10-23 23:39:38,414] INFO: Current version detected to be 23.10.1 (release.py:221)
[2023-10-23 23:39:38,414] INFO: Next version will be 23.10.2 (release.py:222)
[2023-10-23 23:39:38,414] INFO: Cleaning up /Users/cooper/repos/black/CHANGES.md (release.py:127)
[2023-10-23 23:39:38,416] DEBUG: Finished Cleaning up /Users/cooper/repos/black/CHANGES.md (release.py:147)
[2023-10-23 23:39:38,416] INFO: Updating black version to 23.10.2 in /Users/cooper/repos/black/docs/integrations/source_version_control.md (release.py:173)
[2023-10-23 23:39:38,416] DEBUG: Finished updating black version to 23.10.2 in /Users/cooper/repos/black/docs/integrations/source_version_control.md (release.py:185)
[2023-10-23 23:39:38,416] INFO: Updating black version to 23.10.2 in /Users/cooper/repos/black/docs/usage_and_configuration/the_basics.md (release.py:173)
[2023-10-23 23:39:38,417] DEBUG: Finished updating black version to 23.10.2 in /Users/cooper/repos/black/docs/usage_and_configuration/the_basics.md (release.py:185)
```
- Add tests around some key logic

* [pre-commit.ci] auto fixes from pre-commit.com hooks

for more information, see https://pre-commit.ci

* Fix lints + add git to release CI

- Remove black + mypy as linting already runs it ...
- Ignore delete param to TemporaryDirectory as we can't set mypy to 3.12 :D

* Only run CI on linux/ubuntu for now

* Add lots of debug printing + directly run unitests (not via coverage)

* Overloading __str__ is bad on a TestCase

* Add more logging around git tag

* Print where git is in a step

* Rollback creating a fake black repo as we were not using it - I did plan to but I can't get it working on GitHub actions

* Do a deep checkout

* Add noqa for E701,E761 ... maybe we need this in our flake8 config now?

* Fix action to have correct workflow yaml to action on
- Also add fix to not double run when we push directly to psf/black

* All jelle suggestions
- Fix bug missing lines ending with --> in CHANGES.md to delete ...
- Update ci to run out of scripts dir too
- Update test_tuple_calver

---------

Co-authored-by: pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Update README.md (#3997)

Fixed a grammatical error in README.md | 1 |
| Regression test for match variable inside match (#3993) | 1 |
| Fix typo in README.md (#3995) | 1 |
| confine pre-commit to stages (#3940)

See https://pre-commit.com/#confining-hooks-to-run-at-certain-stages

> If you are authoring a tool, it is usually a good idea to provide an appropriate `stages` property. For example a reasonable setting for a linter or code formatter would be `stages: [pre-commit, pre-merge-commit, pre-push, manual]`.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Update CHANGES.md (#3988)

Fixed a grammatical mistake | 1 |
| Add trailing comma test case for hugging parens (#3991) | 1 |
| Update current_style.md (#3990)

Fix small typo | 1 |
| Fix matching of absolute paths in `--include` (#3976) | 1 |
| docs: fix typos in change log and documentations (#3985) | 1 |
| Fix CI by running on Python 3.11 (#3984)

aiohttp doesn't yet support 3.12 | 1 |
| Fix typo in future_style.md (#3979)

parantheses -> parentheses | 1 |
| [2213] Add support for single line format skip with other comments on the same line (#3959) | 1 |
| [925] Improve multiline dictionary and list indentation for sole function parameter (#3964) | 1 |
| Add Unreleased template to CHANGES.md (#3973)

Add Unreleased template to CHANGES.md - Did this via tool working on in another branch | 1 |
| Prepare release 23.10.1 (#3969)

* Prepare release 23.10.1

* Update docs/usage_and_configuration/the_basics.md

Add missed version string

We need to automate or remove this from docs ... It's painful.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>

---------

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix CI failing (#3957)

* Fix CI failing

* [pre-commit.ci] auto fixes from pre-commit.com hooks

for more information, see https://pre-commit.ci

* docs: update CHANGES.md

* docs: fix changelog location to unreleased

---------

Co-authored-by: pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com> | 1 |
| docs: specifies the use of the .git-blame-ignore-revs file (#3961) | 1 |
| Add summary parameter to action (#3958) | 1 |
| Move Docker image to hatch + compile (#3965) | 1 |
| Bump peter-evans/create-or-update-comment from 3.0.2 to 3.1.0 (#3966)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 3.0.2 to 3.1.0.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/c6c9a1a66007646a28c153e2a8580a5bad27bcfa...23ff15729ef2fc348714a3bb66d2f655ca9066f2)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Allow empty line after block open before a comment or compound statement (#3967) | 1 |
| Fix typos in CHANGES.md (#3963) | 1 |
| Fix merging implicit multiline strings that have inline comments (#3956)

* Fix test behaviour

* Add new test cases

* Skip merging strings that have inline comments

* Don't merge lines with multiline strings with inline comments

* Changelog entry

* Document implicit multiline string merging rules

* Fix PR number | 1 |
| Prepare release 23.10.0 (#3951) | 1 |
| Fix parser bug where "type" was misinterpreted as a keyword inside a match (#3950)

Fixes #3790

Slightly hacky, but I think this is correct and it should also improve performance somewhat. | 1 |
| Fix grammar for type alias support (#3949)

Fixes #3948 | 1 |
| Treat raw strings like other docstrings (#3947)

Fixes #3944 | 1 |
| Fix long lines with power operator(s) getting splitted before line length (#3942)

Fixes #3889 | 1 |
| Migrate mypy config to pyproject.toml (#3936)

Co-authored-by: Charles Patel <charles.patel@apkudo.com> | 1 |
| CI Test: Deprecating 'Healthcheck.all()' from Hypothesis in fuzz.py (#3945) | 1 |
| Fix test that was not being run (#3939) | 1 |
| Standardise newlines after module-level docstrings (#3932)

Co-authored-by: jpy-git <josephyoung.jpy@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Report all stacktraces in verbose mode (#3938)

Previously these were swallowed (unlike the ones in black/__init__.py) | 1 |
| Fix cache versioning when BLACK_CACHE_DIR is set (#3937) | 1 |
| Use inline flags for test cases (#3931)

Co-authored-by: Shantanu <12621235+hauntsaninja@users.noreply.github.com> | 1 |
| Drop support for parsing Python 2 (#3933) | 1 |
| Bump pypa/cibuildwheel from 2.16.1 to 2.16.2 (#3934)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.16.1 to 2.16.2.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.16.1...v2.16.2)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Set Docker to use 3.11 for now (#3927)

Until we get new aiohttp wheels we need to build with 3.11.
You can see an example of a fail here:

Workaround for #3919 - Will leave it open until we can move to 3.21 | 1 |
| Update output display to job summary (#3914)

* Update output display to job summary

* fix: handled exit-code of script

* added changelog message | 1 |
| Remove `$`, `>>>` and other prompt prefixes when code copied from the… (#3884)

Adding configurations for sphinx-copybutton in conf.py
https://sphinx-copybutton.readthedocs.io/en/latest/use.html#using-regexp-prompt-identifiers | 1 |
| exclude tests/data/.* from mypy (#3917) | 1 |
| Update link to VS Code formatting instructions (#3921)

Update link | 1 |
| respect magic trailing commas in return types (#3916) | 1 |
| [pre-commit.ci] pre-commit autoupdate (#3915)

updates:
- [github.com/pre-commit/mirrors-mypy: v1.5.0 → v1.5.1](https://github.com/pre-commit/mirrors-mypy/compare/v1.5.0...v1.5.1)
- [github.com/pre-commit/mirrors-prettier: v3.0.1 → v3.0.3](https://github.com/pre-commit/mirrors-prettier/compare/v3.0.1...v3.0.3)

Co-authored-by: pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com> | 1 |
| docs: use LSP for SublimeText 4 (#3913) | 1 |
| Bump pypa/cibuildwheel from 2.16.0 to 2.16.1 (#3911) | 1 |
| Fix up changelog (#3910) | 1 |
| Fix comments getting removed from inside parenthesized strings (#3909)

Since the id of the old leaf may be
the key to comments, the new leaf
must adopt the old comments | 1 |
| Try newer clang in diff-shades job (#3904) | 1 |
| add support for printing the diff of AST trees when running tests (#3902)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump pypa/cibuildwheel from 2.15.0 to 2.16.0 (#3901)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.15.0 to 2.16.0.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.15.0...v2.16.0)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| added the py311 to target-version config (#3898) | 1 |
| fix indentation of line breaks in long type hints by adding parens (#3899)

* fix indentation of line breaks in long type hints by adding parentheses, and remove unnecessary parentheses

* add entry in CHANGES.md, make the style change only in preview mode | 1 |
| Remove outdated mentions of runtime support of Python 3.7 (#3896)

Remove mentions of runtime support of Python 3.7

Runtime support of Python 3.7 was removed in
b4dca26c7d93f930bbd5a7b552807370b60d4298 but a few mentions of it being
supported have remained until now. | 1 |
| Bump actions/checkout from 3 to 4 (#3893)

Bumps [actions/checkout](https://github.com/actions/checkout) from 3 to 4.
- [Release notes](https://github.com/actions/checkout/releases)
- [Changelog](https://github.com/actions/checkout/blob/main/CHANGELOG.md)
- [Commits](https://github.com/actions/checkout/compare/v3...v4)

---
updated-dependencies:
- dependency-name: actions/checkout
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/setup-qemu-action from 2 to 3 (#3890)

Bumps [docker/setup-qemu-action](https://github.com/docker/setup-qemu-action) from 2 to 3.
- [Release notes](https://github.com/docker/setup-qemu-action/releases)
- [Commits](https://github.com/docker/setup-qemu-action/compare/v2...v3)

---
updated-dependencies:
- dependency-name: docker/setup-qemu-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/setup-buildx-action from 2 to 3 (#3892)

Bumps [docker/setup-buildx-action](https://github.com/docker/setup-buildx-action) from 2 to 3.
- [Release notes](https://github.com/docker/setup-buildx-action/releases)
- [Commits](https://github.com/docker/setup-buildx-action/compare/v2...v3)

---
updated-dependencies:
- dependency-name: docker/setup-buildx-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/login-action from 2 to 3 (#3891)

Bumps [docker/login-action](https://github.com/docker/login-action) from 2 to 3.
- [Release notes](https://github.com/docker/login-action/releases)
- [Commits](https://github.com/docker/login-action/compare/v2...v3)

---
updated-dependencies:
- dependency-name: docker/login-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/build-push-action from 4 to 5 (#3894)

Bumps [docker/build-push-action](https://github.com/docker/build-push-action) from 4 to 5.
- [Release notes](https://github.com/docker/build-push-action/releases)
- [Commits](https://github.com/docker/build-push-action/compare/v4...v5)

---
updated-dependencies:
- dependency-name: docker/build-push-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump sphinx from 7.2.5 to 7.2.6 in /docs (#3895)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 7.2.5 to 7.2.6.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/master/CHANGES.rst)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v7.2.5...v7.2.6)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Document disabling E704 (#3888)

Linking #3887 | 1 |
| mypyc build improvements (#3881)

Build in separate jobs. This makes it clearer if e.g. a single Python
version is failing. It also potentially gets you more parallelism.

Build everything on push to master.

Only build Linux 3.8 and 3.11 wheels on PRs. | 1 |
| Bump docutils from 0.19 to 0.20.1 in /docs (#3699)

Bumps [docutils](https://docutils.sourceforge.io/) from 0.19 to 0.20.1. | 1 |
| Bump sphinx from 7.2.3 to 7.2.5 in /docs (#3882)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 7.2.3 to 7.2.5.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/master/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v7.2.3...v7.2.5) | 1 |
| Fix broken url in editors.md (#3885)

* Fix broken url in editors.md

Update a link pointing to the Arch Linux repos.

* [pre-commit.ci] auto fixes from pre-commit.com hooks

for more information, see https://pre-commit.ci

---------

Co-authored-by: pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com> | 1 |
| Bump actions/checkout from 3 to 4 (#3883)

Bumps [actions/checkout](https://github.com/actions/checkout) from 3 to 4.
- [Release notes](https://github.com/actions/checkout/releases)
- [Changelog](https://github.com/actions/checkout/blob/main/CHANGELOG.md)
- [Commits](https://github.com/actions/checkout/compare/v3...v4)

---
updated-dependencies:
- dependency-name: actions/checkout
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Prepare release 23.9.1 (#3878) | 1 |
| mypyc builds on PRs, skip mypyc wheels for 3.12 (#3870)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Pickle raw tuples in FileData cache (#3877)

Co-authored-by: Marc Mueller <30130371+cdce8p@users.noreply.github.com> | 1 |
| Re-export black.Mode (#3875) | 1 |
| Ignore aiohttp DeprecationWarning for 3.12 (#3876)

Co-authored-by: Marc Mueller <30130371+cdce8p@users.noreply.github.com> | 1 |
| Upgrade to Furo 2023.9.10 to fix docs build (#3873) | 1 |
| Add mypyc test marks to new tests that patch (#3871)

This is enough for me to get a clean test run on Python 3.9 with mypyc.
I have not been able to repro the pickle failures on either Linux or
macOS. | 1 |
| Bump RTD Python version from 3.8 to 3.11 (#3868)

Recent ReadTheDocs builds have been failing as our documentation dependencies (notably Sphinx) require Python 3.9+. | 1 |
| Add classifier for 3.12 (#3866) | 1 |
| Upgrade mypy (#3864) | 1 |
| Prepare release 23.9.0 (#3863) | 1 |
| Blank line between nested and function def in stub files. (#3862)

The idea behind this change is that we stop looking into previous body to determine if there should be a blank before a function or class definition.

Input:

```python
import sys

if sys.version_info > (3, 7):
    class Nested1:
        assignment = 1
        def function_definition(self): ...
    def f1(self) -> str: ...
    class Nested2:
        def function_definition(self): ...
        assignment = 1
    def f2(self) -> str: ...

if sys.version_info > (3, 7):
    def nested1():
        assignment = 1
        def function_definition(self): ...
    def f1(self) -> str: ...
    def nested2():
        def function_definition(self): ...
        assignment = 1
    def f2(self) -> str: ...
```

Stable style
```python
import sys

if sys.version_info > (3, 7):
    class Nested1:
        assignment = 1
        def function_definition(self): ...

    def f1(self) -> str: ...

    class Nested2:
        def function_definition(self): ...
        assignment = 1
    def f2(self) -> str: ...

if sys.version_info > (3, 7):
    def nested1():
        assignment = 1
        def function_definition(self): ...

    def f1(self) -> str: ...
    def nested2():
        def function_definition(self): ...
        assignment = 1
    def f2(self) -> str: ...
```

In the stable formatting, we have a blank line sometimes, not depending on the previous statement on the same level, but on the last (potentially nested) statement in the previous body.

#2783/#3564 fixes this for classes in preview style:

```python
import sys

if sys.version_info > (3, 7):
    class Nested1:
        assignment = 1
        def function_definition(self): ...

    def f1(self) -> str: ...

    class Nested2:
        def function_definition(self): ...
        assignment = 1

    def f2(self) -> str: ...

if sys.version_info > (3, 7):
    def nested1():
        assignment = 1
        def function_definition(self): ...

    def f1(self) -> str: ...
    def nested2():
        def function_definition(self): ...
        assignment = 1
    def f2(self) -> str: ...
```

This PR additionally fixes this for function definitions:

```python
if sys.version_info > (3, 7):
    if sys.platform == "win32":
        assignment = 1
        def function_definition(self): ...

    def f1(self) -> str: ...
    if sys.platform != "win32":
        def function_definition(self): ...
        assignment = 1

    def f2(self) -> str: ...

if sys.version_info > (3, 8):
    if sys.platform == "win32":
        assignment = 1
        def function_definition(self): ...

    class F1: ...
    if sys.platform != "win32":
        def function_definition(self): ...
        assignment = 1

    class F2: ...
```

You can see the effect of this change on typeshed in https://github.com/konstin/typeshed/pull/1/files. As baseline, the preview mode changes without this PR are at https://github.com/konstin/typeshed/pull/2.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Avoid removing whitespace for walrus operators within subscripts (#3823)

Co-authored-by: hauntsaninja <hauntsaninja@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Add Black PyCharm 2023.2 integration instructions (#3839) | 1 |
| blackd: fix mishandling of single character input (#3558) | 1 |
| Apply ignore logic before symlink resolution (#3846)

This means, for instance, that a gitignored symlink cannot affect your
formatting. Fixes #3527, fixes #3826 | 1 |
| Move coverage configurations to `pyproject.toml` (#3858) | 1 |
| Bump furo from 2023.7.26 to 2023.8.19 in /docs + sphinx to 7.2.3 (#3848)

* Bump furo from 2023.7.26 to 2023.8.19 in /docs

Bumps [furo](https://github.com/pradyunsg/furo) from 2023.7.26 to 2023.8.19.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2023.07.26...2023.08.19)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

* Move to sphinx 7.2.3 + fix intersphinx_mapping

---------

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Co-authored-by: Cooper Ry Lees <me@cooperlees.com> | 1 |
| Fix download badge link (#3853) | 1 |
| Improve handling of root to get_sources (#3847)

This is a little more type safe and a little cleaner | 1 |
| Remove tox pin (#3844) | 1 |
| Improve caching by comparing file hashes as fallback for mtime and size (#3821)

Co-authored-by: Shantanu <12621235+hauntsaninja@users.noreply.github.com> | 1 |
| Pin tox to fix CI (#3843) | 1 |
| [pre-commit.ci] pre-commit autoupdate (#3837) | 1 |
| Make pre-commit do less (#3838) | 1 |
| Bump pypa/cibuildwheel from 2.14.1 to 2.15.0 (#3836) | 1 |
| Remove ENV_PATH on Black action completion (#3759) | 1 |
| [pre-commit.ci] pre-commit autoupdate (#3833)

updates:
- [github.com/pre-commit/mirrors-prettier: v3.0.0 → v3.0.1](https://github.com/pre-commit/mirrors-prettier/compare/v3.0.0...v3.0.1)

Co-authored-by: pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com> | 1 |
| More concise formatting for dummy implementations (#3796) | 1 |
| Document pre-commit mirror (#3828) | 1 |
| [pre-commit.ci] pre-commit autoupdate (#3825) | 1 |
| Bump furo from 2023.5.20 to 2023.7.26 in /docs (#3824)

Bumps [furo](https://github.com/pradyunsg/furo) from 2023.5.20 to 2023.7.26.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2023.05.20...2023.07.26)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Add Lyft to organizations using black (#3818) | 1 |
| Rewrite mostly useless assert in test_trans.py (#3810)

This PR updates an assert statement that checks the bounds of a
string-slicing operation. The updated assertion provides more accurate
and informative error handling by specifically checking the relative
values of the indices and the string length.

The original assertion was essentially checking if Python's string
slicing was behaving as expected. However, it wasn't providing any
guarantees or useful information about the bounds i and j themselves.

The updated assertion checks that the indices used for slicing are
within the bounds of the string. It will throw an AssertionError if the
indices are out of bounds or if i > j, providing a more specific and
informative error. | 1 |
| Fix typo in `target-version` param wrongly used in plural (#3817) | 1 |
| Fix unintentionally swapped words in index.md (#3809)

Fix unintentionally swapped words in index.md

I think the intent was to say "large changes in formatting", because it doesn't make sense to say "large formatting in changes". | 1 |
| Fixing pre-commit using pyyaml with broken version (#3804) | 1 |
| Simplify empty line tracker (#3797) | 1 |
| Improvements to contributing docs (#3753) | 1 |
| Fix diff-shades comment missing newlines (#3799)

Preserving newlines is done differently when writing to $GITHUB_OUTPUT
over the deprecated :set-output: command. | 1 |
| Bump pypa/cibuildwheel from 2.13.1 to 2.14.1 (#3795)

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix most blib2to3 lint (#3794) | 1 |
| Maintainers += Shantanu Jain (hauntsaninja) (#3792) | 1 |
| Avoid importing `IPython` if notebook cells do not contain magics (#3782)

Co-authored-by: hauntsaninja <hauntsaninja@gmail.com>
Co-authored-by: Shantanu <12621235+hauntsaninja@users.noreply.github.com> | 1 |
| Continue to avoid Click typing issue (#3791) | 1 |
| Document shebang comment behaviour (#3787) | 1 |
| [pre-commit.ci] pre-commit autoupdate (#3780)

updates:
- [github.com/pycqa/flake8: 4.0.1 → 6.0.0](https://github.com/pycqa/flake8/compare/4.0.1...6.0.0)
- [github.com/pre-commit/mirrors-prettier: v2.7.1 → v3.0.0](https://github.com/pre-commit/mirrors-prettier/compare/v2.7.1...v3.0.0)
- [github.com/pre-commit/pre-commit-hooks: v4.3.0 → v4.4.0](https://github.com/pre-commit/pre-commit-hooks/compare/v4.3.0...v4.4.0) | 1 |
| Fix lint in test_ipynb (#3781)

Unblocks #3780 | 1 |
| Remove unneeded mypy dependencies (#3783) | 1 |
| Remove Python 3.7 from classifiers (#3784)

Follow-up on https://github.com/psf/black/pull/3765 | 1 |
| Fix typo in CITATION.cff (#3779)

Fix tiny typo in CITATION.cff | 1 |
| Prepare release 23.7.0 (#3776) | 1 |
| Unpin pytest-xdist (#3772) | 1 |
| Disable coverage on pypy tests (#3777)

The pypy tests are reeeeaaally slow. Maybe this will help. | 1 |
| Upgrade to latest mypy (#3775) | 1 |
| Fix crash on type comment with trailing space (#3773) | 1 |
| Fix removed comments in stub files (#3745) | 1 |
| Improve performance by skipping unnecessary normalisation (#3751)

This speeds up black by about 40% when the cache is full | 1 |
| Add CITATION.cff file (#3723) | 1 |
| Run pyupgrade on blib2to3 and src (#3771) | 1 |
| Remove click patch (#3768)

Apparently this was only needed on Python 3.6. We've now dropped support
for 3.6 and 3.7. It's also not needed on new enough click. | 1 |
| Fix CI for Click typing issue (#3770)

https://github.com/pallets/click/issues/2558 | 1 |
| Drop support for Python 3.7 (#3765) | 1 |
| Better error message for invalid exclude types (#3764) | 1 |
| Enable `PYTHONWARNDEFAULTENCODING = 1` in CI (#3763) | 1 |
| CI Test: Deprecating 'set-output' command  (#3757) | 1 |
| Doc: Developer reference update (#3755) | 1 |
| Fix a magical comment caused internal error (#3740)

`is_type_comment` now specifically deals with general type comments for a leaf.
`is_type_ignore_comment` now handles type comments contains ignore annotation for a leaf
`is_type_ignore_comment_string` used to determine if a string is an ignore type comment | 1 |
| Decrease cost of ipynb code path when unneeded (#3748)

IPython is a very expensive import, like, at least 300ms. I'd also
venture that it's much more common than tokenize-rt, which is like 30ms.
I work in a repo where I use black, have IPython installed and there
happen to be a couple notebooks (that we don't want formatted). I know I
can force exclude ipynb, but this change doesn't really have a cost. | 1 |
| Check self format for the whole repo (#3750)

`black .` is changing things in gallery and scripts for me | 1 |
| Integrate verbose logging with get_sources (#3749)

Currently the verbose logging for "Sources to be formatted" is a little
suspect in that it is a completely different code path from
`get_sources`.

This can result in bugs like https://github.com/psf/black/pull/3216#issuecomment-1213557359
and generally limits the value of these logs.

This does change the "when" of this log, but the colours help separate
it from the even more verbose logs. | 1 |
| Allow specifying `--workers` via environment variable (#3743) | 1 |
| Build with mypyc 1.3 (#3697)

Several new versions of mypyc has been released since the last upgrade, and they include some performance improvements which could make the compiled version of Black run faster.

https://mypy-lang.org/news.html

The latest version of hatch-mypyc allows being installed next the 1.x series of mypy. | 1 |
| Fix not honouring pyproject.toml when using stdin and calling black from parent directory (#3719)

Co-authored-by: Renan Rodrigues <renan.rodrigues@appliedbiomath.com> | 1 |
| Doc: updating url link (#3739) | 1 |
| Bump myst-parser from 1.0.0 to 2.0.0 in /docs (#3738)

Bumps [myst-parser](https://github.com/executablebooks/MyST-Parser) from 1.0.0 to 2.0.0.
- [Release notes](https://github.com/executablebooks/MyST-Parser/releases)
- [Changelog](https://github.com/executablebooks/MyST-Parser/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/MyST-Parser/compare/v1.0.0...v2.0.0)

---
updated-dependencies:
- dependency-name: myst-parser
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Do not add trailing commas to return type annotations using PEP 604 unions (#3735)

Fix #3638: Do not add trailing commas to return type annotations using PEP 604 unions. | 1 |
| Max line length with bugbear (#3731)

* Make phrasing for flake8 users more concise

max-line-length should be 80 with flake8-bugbear
Fixes #3716

* Re-add rationale and an explanation for

disabling E203

* Run pre-commit | 1 |
| Bump peter-evans/create-or-update-comment from 3.0.1 to 3.0.2 (#3730)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 3.0.1 to 3.0.2.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/ca08ebd5dc95aa0cd97021e9708fcd6b87138c9b...c6c9a1a66007646a28c153e2a8580a5bad27bcfa)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump pypa/cibuildwheel from 2.13.0 to 2.13.1 (#3729)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.13.0 to 2.13.1.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.13.0...v2.13.1)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Use aware datetimes to represent UTC (#3728)

Avoids a Python 3.12 deprecation warning.

Subtle difference: previously, timestamps in diff filenames had the
`+0000` separated from the timestamp by space. With this, the space is
there no more, and there is a colon, as in `+00:00`. | 1 |
| Add support for PEP 695 syntax (#3703) | 1 |
| blackd: show default values for options (#3712)

* blackd: show default values for options

Reference: https://click.palletsprojects.com/en/8.1.x/api/#click.Option

* Fix spacing in CHANGES.md | 1 |
| Bump pypa/cibuildwheel from 2.12.3 to 2.13.0 (#3710)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.12.3 to 2.13.0.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.12.3...v2.13.0)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix docs formatting (#3704) | 1 |
| Document each configuration option in more detail (#2839) | 1 |
| docs: update note on GitHub .git-blame-ignore-revs support (#3655) | 1 |
| Change example from `%%writeline` to `%%writefile` (#3673) | 1 |
| Bump furo from 2023.3.27 to 2023.5.20 in /docs (#3698)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Avoid EncodingWarning in blib2to3 (#3696) | 1 |
| Remove blank lines before class docstring (#3692) | 1 |
| Sort DEFAULT_EXCLUDES and add .vscode, .pytest_cache and .ruff_cache (#3691)

Co-authored-by: Ray Bell <ray.bell@dtn.com> | 1 |
| Bump peter-evans/find-comment from 2.3.0 to 2.4.0 (#3670)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 2.3.0 to 2.4.0.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/034abe94d3191f9c89d870519735beae326f2bdb...a54c31d7fa095754bfef525c0c8e5e5674c4b4b1)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| [github action] display black result in job summary (#3688)

* send output to $GITHUB_STEP_SUMMARY

* update CHANGES.md

* update CHANGES.md with PR number

* implement PR feedback

* fix pre-commit issues (prettier/trailing whitespace) | 1 |
| Bump peter-evans/create-or-update-comment from 2.1.1 to 3.0.1 (#3683)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 2.1.1 to 3.0.1.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/67dcc547d311b736a8e6c5c236542148a47adc3d...ca08ebd5dc95aa0cd97021e9708fcd6b87138c9b)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| blib2to3: add a few annotations (#3675) | 1 |
| Fix new mypy error in blib2to3 (#3674)

See python/mypy#15174 | 1 |
| Do not wrap implicitly concatenated strings used as func args in parens (#3640) | 1 |
| Bump pypa/cibuildwheel from 2.12.1 to 2.12.3 (#3657)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.12.1 to 2.12.3.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.12.1...v2.12.3)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Document black-jupyter hook (#3650)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump sphinx-copybutton from 0.5.1 to 0.5.2 in /docs (#3651)

Bumps [sphinx-copybutton](https://github.com/executablebooks/sphinx-copybutton) from 0.5.1 to 0.5.2.
- [Release notes](https://github.com/executablebooks/sphinx-copybutton/releases)
- [Changelog](https://github.com/executablebooks/sphinx-copybutton/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/sphinx-copybutton/compare/v0.5.1...v0.5.2)

---
updated-dependencies:
- dependency-name: sphinx-copybutton
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix two more mypyc issues with mypyc v1.2.0. (#3648) | 1 |
| Explicitly annotate this with `Final[str]` to make it work in mypyc 1.0.0+. (#3645) | 1 |
| Fix an example for 'Improved parentheses management' in the (future of the) Black code style (#3635) | 1 |
| Bump furo from 2023.3.23 to 2023.3.27 in /docs (#3636)

Bumps [furo](https://github.com/pradyunsg/furo) from 2023.3.23 to 2023.3.27.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2023.03.23...2023.03.27)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fixup the changelog (#3628) | 1 |
| Prepare release 23.3.0 (#3625) | 1 |
| Specify Python exec path with minor version if available (#3508)

Fixes #3507 | 1 |
| Use GH action version when version argument not specified (#3543) | 1 |
| Bump furo from 2022.12.7 to 2023.3.23 in /docs (#3624)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Let string splitters respect `East_Asian_Width` property (#3445)

This patch changes the preview style so that string splitters respect
Unicode East Asian Width[^1] property.  If you are not familiar to CJK
languages it is not clear immediately.  Let me elaborate with some
examples.

Traditionally, East Asian characters (including punctuation) have
taken up space twice than European letters and stops when they are
rendered in monospace typeset.  Compare the following characters:

```
abcdefg.
글、字。
```

The characters at the first line are half-width, and the second line
are full-width.  (Also note that the last character with a small
circle, the East Asian period, is also full-width.)  Therefore, if we
want to prevent those full-width characters to exceed the maximum
columns per line, we need to count their *width* rather than the number
of characters.  Again, the following characters:

```
글、字。
```

These are just 4 characters, but their total width is 8.

Suppose we want to maintain up to 4 columns per line with the following
text:

```
abcdefg.
글、字。
```

How should it be then?  We want it to look like:

```
abcd
efg.
글、
字。
```

However, Black currently turns it into like this:

```
abcd
efg.
글、字。
```

It's because Black currently counts the number of characters in the line
instead of measuring their width. So, how could we measure the width?
How can we tell if a character is full- or half-width? What if half-width
characters and full-width ones are mixed in a line? That's why Unicode
defined an attribute named `East_Asian_Width`. Unicode grouped every
single character according to their width in fixed-width typeset.

This partially addresses #1197, but only for string splitters. The other
parts need to be fixed as well in future patches.

This was implemented by copying rich's own approach to handling wide
characters: generate a table using wcwidth, check it into source
control, and use in to drive helper functions in Black's logic. This
gets us the best of both worlds: accuracy and performance (and let's us
update as per our stability policy too!).

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump sphinx from 5.3.0 to 6.1.3 in /docs (#3499)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump myst-parser from 0.18.1 to 1.0.0 in /docs (#3601)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Co-authored-by: Richard Si <sichard26@gmail.com> | 1 |
| Support files with type comment syntax errors (#3594) | 1 |
| Fix bug introduced in #3564. (#3615) | 1 |
| Enforce a blank line after a nested class in stubs (#3564) | 1 |
| Update documentation regarding isort compatibility (#3567) | 1 |
| Add SECURITY.md (#3612) | 1 |
| Bump peter-evans/create-or-update-comment from 2.1.0 to 2.1.1 (#3548)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 2.1.0 to 2.1.1.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/5adcb0bb0f9fb3f95ef05400558bdb3f329ee808...67dcc547d311b736a8e6c5c236542148a47adc3d)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Do not add an extra blank line to an import line that has fmt disabled (#3610) | 1 |
| Consistently format async statements similar to their non-async version. (#3609) | 1 |
| Bump pypa/cibuildwheel from 2.11.4 to 2.12.1 (#3602)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.11.4 to 2.12.1.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.11.4...v2.12.1)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Correct spelling mistakes (#3599) | 1 |
| Consistently wrap two context managers in parens (in --preview). (#3589)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Improve multiline string handling (#1879)

Co-authored-by: Olivia Hong <ohong@lyft.com>
Co-authored-by: Olivia Hong <24500729+olivia-hong@users.noreply.github.com> | 1 |
| Bump peter-evans/find-comment from 2.2.0 to 2.3.0 (#3584)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 2.2.0 to 2.3.0.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/81e2da3af01c92f83cb927cf3ace0e085617c556...034abe94d3191f9c89d870519735beae326f2bdb)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Update Action example to use checkout@v3 (#3563)

Latest version of `actions/checkout` is v3 (or rather v3.3) so let's
use that in the example now. | 1 |
| Fix typos in comments: assignement -> assignment (#3556) | 1 |
| Bump docker/build-push-action from 3 to 4 (#3549)

Bumps [docker/build-push-action](https://github.com/docker/build-push-action) from 3 to 4.
- [Release notes](https://github.com/docker/build-push-action/releases)
- [Commits](https://github.com/docker/build-push-action/compare/v3...v4)

---
updated-dependencies:
- dependency-name: docker/build-push-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Rename design label to style because it's clearer (#3547) | 1 |
| Actually add trailing commas to collection literals even if there are terminating comments (#3393)


Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <sichard26@gmail.com> | 1 |
| Document the future style changes introduced in #3489 and #3440 (#3541) | 1 |
| Fix import of blib2to3.pgen2.driver (#3546) | 1 |
| Prepare release 23.1.0 (#3536)

Co-authored-by: Richard Si <sichard26@gmail.com> | 1 |
| Infer target version based on project metadata (#3219)

Co-authored-by: Richard Si <sichard26@gmail.com> | 1 |
| Draft for Black 2023 stable style (#3418) | 1 |
| Fix unsafe cast in linegen.py w/ await yield handling (#3533)

Fixes #3532. | 1 |
| Upgrade isort (#3534)

See PyCQA/isort#2077. | 1 |
| Remove Python version in the_basics.md (#3528) | 1 |
| Fix `black --help` output for `--python-cell-magics` option to be reproducible (#3516) | 1 |
| Update document now that paren wrapping CMs on Python 3.9+ is implemented (#3520) | 1 |
| Fix an invalid quote escaping bug in f-string expressions (#3509)

Fixes #3506

We can't simply escape the quotes in a naked f-string when merging string groups, because backslashes are invalid.

The quotes in f-string expressions should be toggled (this is safe since quotes can't be reused).

This fix also means implicitly concatenated f-strings with different quotes can now be merged or quote-normalized by changing the quotes used in expressions. e.g.:

```diff
         raise sa_exc.UnboundExecutionError(
             "Could not locate a bind configured on "
-            f'{", ".join(context)} or this Session.'
+            f"{', '.join(context)} or this Session."
         )
``` | 1 |
| Format hex code in unicode escape sequences in string literals (#2916)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Use dashes for pycodestyle max line length config (#3513)

The option is `max-line-length` with dashes, not underscores. The config option name is given in the output of `pycodestyle -h`, which can also be checked on https://pep8.readthedocs.io/en/stable/intro.html#example-usage-and-output:
```
Configuration:
    The project options are read from the [pycodestyle] section of the
    tox.ini file or the setup.cfg file located in any parent folder of the
    path(s) being processed.  Allowed options are: exclude, filename,
    select, ignore, max-line-length, max-doc-length, hang-closing, count,
    format, quiet, show-pep8, show-source, statistics, verbose
``` | 1 |
| Reenable macOS mypyc wheel build (#3511)

Hatchling implemented a workaround for the 'technically right tag but no
one understands it, including pip' issue so this should work now. | 1 |
| Wrap multiple context managers in parentheses when targeting Python 3.9+ (#3489) | 1 |
| Fix false symlink detection claims in verbose output (#3385)

When trying to format a project from the outside, the verbose output
shows says that there are symbolic links that points outside of the
project, but displays the wrong project path, meaning that these
messages are false positives.

This bug is triggered when the command is executed from outside a
project on a folder inside it, causing an inconsistency between the
path to the detected project root and the relative path to the target
contents.

The fix is to normalize the target path using the project root before
processing the sources, which removes the presence of the incorrect
messages.

---

The test attemps to emulate the behavior of the CLI as closely as
posible by patching some `pathlib.Path` methods and passing certain
reference paths to the context object and `black.get_sources`.

Before the associated fix was introduced, this test failed because
some of the captured files reported the presence of a symlink due to
an incorrectly formated path. The test also asserts that only a single
file is reported as ignored, which is part of the expected behavior.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Fix crash with walrus + await + with (#3473)

Fixes #3472 | 1 |
| Add flake8-bugbear B907 to ignore list (#3503) | 1 |
| Fix two docstring crashes (#3451) | 1 |
| Add IntelliJ docs on external tools and file watcher (#3365)

Revert deleted documentation on setting up Black using IntelliJ
external tool or file watcher utilities. These are still worth keeping
because some peole might not want to use a third-party plugin or
install Blackd's extra dependencies.

Co-authored-by: Richard Si <sichard26@gmail.com> | 1 |
| Documentation: clarify the state of multiple context managers (#3488)

Clarify that the backslash & paren-wrapping formatting for multiple
context managers aren't yet implemented. | 1 |
| Remove misleading phrase in Usage and Configuration (#3492)

The CLI options were already shown in the "Command line options" in the same page. | 1 |
| Add email for Richard Si (#3478) | 1 |
| Fail lint CI if the PR doesn't target main (#3477)

Let's skip the check if we're running on a fork just in case someone
opens a PR against a branch on said fork as part of a PR review
upstream. | 1 |
| Parenthesize conditional expressions (#2278)

Co-authored-by: Jordan Ephron <JEphron@users.noreply.github.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump peter-evans/find-comment from 2.1.0 to 2.2.0 (#3476)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 2.1.0 to 2.2.0.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/f4499a714d59013c74a08789b48abe4b704364a0...81e2da3af01c92f83cb927cf3ace0e085617c556)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump pypa/cibuildwheel from 2.11.3 to 2.11.4 (#3475)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.11.3 to 2.11.4.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.11.3...v2.11.4)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix some typos (#3474) | 1 |
| Significantly speedup ESP on large expressions that contain many strings (#3467) | 1 |
| Add latest_prerelease Docker Hub tag for following the latest alpha release (#3465)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix an issue where extra empty lines are added. (#3470) | 1 |
| Vim plugin docs improvements (#3468)

* Organize vim plugin section with headers to separate out Installation, Usage, and Troubleshooting for readability and easy linking

* Add missing plugin configuration options, with current defaults

* Add installation note for Arch Linux, now that the plugin is shipped with the python-black package (ref: https://bugs.archlinux.org/task/73024)

* Fix vim-plug specification to follow stable releases. Moving the same tag is an antipattern that doesn't re-resolve with vim-plug, see this discussion for more detail (https://github.com/junegunn/vim-plug/pull/720\#issuecomment-1105829356). Per vim-plug's maintainer's recommendation, use the 'tag' key instead with a shell wildcard. Wildcard should be '*.*.*' as that follows Black's versioning detailed here (https://black.readthedocs.io/en/latest/contributing/release_process.html\#cutting-a-release) and doesn't include current alpha releases. | 1 |
| Fix a crash in ESP where a standalone comment is placed before a dict's value (#3469) | 1 |
| Exclude string type annotations from ESP (#3462) | 1 |
| Fix an f-string crash in ESP. (#3463) | 1 |
| Do not move docker `latest_release` tag for Pre-Releases (#3461)

* Do not move docker `latest_release` tag for Pre-Releases

- When we do a pre-release lets not move the latest_release tag
  - This tag should only move on official real releases

Fixes #3453

* Make it prettier - TIL we format our yaml | 1 |
| tomli: Don't worry about specific alpha releases (#3448)

This prevents bugs due to pypa/packaging#522.

Fixes #3447. | 1 |
| Fix syntax error in match test (#3426) | 1 |
| Check stability for both preview and non-preview styles (#3423)

And fix parens-related test failures this found.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Remove separate 3.11 CI now deps support 3.11 (#3446)

* Remove separate 3.11 CI now deps support 3.11

- We can run everything now like all other stable versions of Python
- test in a 3.11 vent: `/tmp/tb/bin/tox -e py311,ci-py311`

```
  py311: OK (28.99=setup[7.90]+cmd[5.29,0.66,6.94,6.08,1.89,0.24] seconds)
  ci-py311: OK (30.33=setup[3.20]+cmd[3.66,0.31,17.43,4.60,0.90,0.23] seconds)
  congratulations :) (59.35 seconds)
```

* Add to CHANGES.md

* Add fuzz run in 3.11 | 1 |
| Fix an infinite recursion error exposed by #3440 (#3444) | 1 |
| Prefer splitting right hand side of assignment statements. (#3368) | 1 |
| Improve long values in dict literals (#3440) | 1 |
| Fix a crash when a colon line is marked between `# fmt: off` and `# fmt: on` (#3439) | 1 |
| Do not put the closing quotes in a docstring on a separate line (#3430)

Fixes #3320. Followup from #3044. | 1 |
| Bump furo from 2022.9.29 to 2022.12.7 in /docs (#3433)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.9.29 to 2022.12.7.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.09.29...2022.12.07)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump pypa/cibuildwheel from 2.11.2 to 2.11.3 (#3434)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.11.2 to 2.11.3.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.11.2...v2.11.3)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump mypy[c] from 0.971 to 0.991 (#3380) | 1 |
| Adding pyproject.toml configuration output to verbose logging (#3392) | 1 |
| make black[jupyter] installation cross-shell (#3394) | 1 |
| Fix a crash in preview style with assert + parenthesized string. (#3415)

The bug is in the `get_leaves_inside_matching_brackets` on the third line below:

```python
assert xxxxxxxxx.xxxxxxxxx.xxxxxxxxx(
    xxxxxxxxx
).xxxxxxxxxxxxxxxxxx(), (
    "xxx {xxxxxxxxx} xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
)
```

Including the invisible paren, third line is `).xxxxxxxxxxxxxxxxxx()), (`, that it has a matched pair then an unmatched closing paren afterwards. This PR ensures the returned leaves are actually matched.

Fixes #3414. | 1 |
| Fix type annotation for gitignore pathspec (#3416) | 1 |
| Prepare release 22.12.0 (#3413) | 1 |
| release: skip bad macos wheels for now (#3411)

Workaround for #3312 | 1 |
| Bump peter-evans/find-comment from 2.0.1 to 2.1.0 (#3404)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix CI with latest flake8-bugbear (#3412) | 1 |
| Bump sphinx-copybutton from 0.5.0 to 0.5.1 in /docs (#3390)

Bumps [sphinx-copybutton](https://github.com/executablebooks/sphinx-copybutton) from 0.5.0 to 0.5.1.
- [Release notes](https://github.com/executablebooks/sphinx-copybutton/releases)
- [Changelog](https://github.com/executablebooks/sphinx-copybutton/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/sphinx-copybutton/compare/v0.5.0...v0.5.1)

---
updated-dependencies:
- dependency-name: sphinx-copybutton
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Wordsmith current_style.md (#3383)

"realtime" doesn't make sense in this context. | 1 |
| Remove whitespaces of whitespace-only files (#3348)

Currently, empty and whitespace-only (with or without newlines) are
not modified. In some discussions (issues and pull requests) consensus
was to reformat whitespace-only files to empty or single-character
files, preserving line endings when possible. With that said, this
commit introduces the following behaviors:

* Empty files are left as is
* Whitespace-only files (no newline) reformat into empty files
* Whitespace-only files (1 or more newlines) reformat into a single
newline character

To implement these changes, we moved the initial check at
`format_file_contents` that raises `NothingChanged` if the source
(with no whitespaces) is an empty string. In the case of *.ipynb
files, `format_ipynb_string` checks a similar condition and removed
whitespaces. In the case of Python files, `format_str_once` includes a
check on the output that returns the correct newline character if
possible or an empty string otherwise.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Clarify that Black runs with --safe by default (#3378) | 1 |
| Correctly handle trailing commas that are inside a line's leading non-nested parens (#3370)

- Fixes #1671
- Fixes #3229 | 1 |
| Compare each .gitignore found with an appropiate relative path (#3338)

* Apply .gitignore files considering their location

When a .gitignore file contains the special rule to ignore every
subfolder content (`*/*`) and the file is located in a subfolder
relative to where the command is executed (root), the rule is
incorrectly applied and ignores every file at the same level of the
.gitignore file.

The reason for this is that the `gitignore` variable accumulates the
rules found in each .gitignore while traversing files and directories
recursively. This makes sense and, in general, works as expected. The
problem is that the gitignore rules are applied using as the relative
path from root to target directory as a reference. This is the cause
of the bug.

The implemented solution keeps track of every .gitignore file found
while traversing the targets and the absolute location of each
.gitignore file. Then, when matching files to the .gitignore rules,
compare each set of rules with the appropiate relative path to the
candidate target file.

To make this possible, we changed the single `gitignore` object with a
dictionary of similar objects, where the corresponding key is the
absolute path to the folder that contains that .gitignore file. This
required changing the signature of the `get_sources` function. Also, we
introduce a `is_ignored` function that compares a file with every set
of rules. Finally, some tests required an update to pass the gitignore
object in the new format.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Test .gitignore with `*/*` is applied correctly

The test contains three cases: 1) when the .gitignore with the special
rule to ignore every subfolder and its contents (*/*) is in the root,
2) when the file is inside a subfolder relative to root (nested), and
3) when the target folder contains the .gitignore and root is a parent
folder of the target. In all of these cases, we compare the files that
are visible by Black with a known list of paths containing the
expected values.

Before the fix introduced in the previous commit, these tests failed
when the .gitignore file was nested (second case). Now, the test is
passed for all cases.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Update CHANGES.md

Add entry about fixed bug and changes introduced: ignore files by
considering the location of each .gitignore file and the relative path
of each target

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Small refactor to improve code readability

These changes are small improvements to improve code readability:
rename a variable to a more descriptive name (from `exclude_is_None`
to `using_default_exclude`), use a better syntax to include the type
annotation for `gitignore` variable (from typing comment to
Python-style typing annotation), and replace an if-else block with a
single dictionary definition (in this case, we need to compare keys
instead of values, meaning that the change works)

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Make nested function a top-level function

The function to match a given path with every discovered .gitignore
file does not need to be a nested function and can be a top-level
function. The arguments did not change, but the naming of local
variables was improved for readability.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Apply .gitignore correctly in every source entry (#3336)

When passing multiple src directories, the root gitignore was only
applied to the first processed source. The reason is that, in the
first source, exclude is `None`, but then the value gets overridden by
`re_compile_maybe_verbose(DEFAULT_EXCLUDES)`, so in the next iteration
where the source is a directory, the condition is not met and sets the
value of `gitignore` to `None`.

To fix this problem, we store a boolean indicating if `exclude` is
`None` and set the value of `exclude` to its default value if that's
the case. This makes sure that the flow enters the correct condition on
following iterations and also keeps the original value if the condition
is not met.

Also, the value of `gitignore` is initialized as `None` and overriden
if necessary. The value of `root_gitignore` is always calculated to
avoid using additional variables (at the small cost of additional
computations).

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Update docs to include pycodestyle (#3369)

* Add pycodestyle to using_black_with_other_tools.md | 1 |
| Bump pypa/cibuildwheel from 2.10.2 to 2.11.2 (#3367)

Bumps [pypa/cibuildwheel](https://github.com/pypa/cibuildwheel) from 2.10.2 to 2.11.2.
- [Release notes](https://github.com/pypa/cibuildwheel/releases)
- [Changelog](https://github.com/pypa/cibuildwheel/blob/main/docs/changelog.md)
- [Commits](https://github.com/pypa/cibuildwheel/compare/v2.10.2...v2.11.2)

---
updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump peter-evans/create-or-update-comment from 2.0.1 to 2.1.0 (#3366)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 2.0.1 to 2.1.0.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/2b2c85d0bf1b8a7b4e7e344bd5c71dc4b9196e9f...5adcb0bb0f9fb3f95ef05400558bdb3f329ee808)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Vim plugin: allow using system black rather than virtualenv (#3309)

Provide a configuration parameter to the Vim plugin which will allow the
plugin to skip setting up a virtualenv. This is useful when there is a
system installation of black (e.g. from a Linux distribution) which the
user prefers to use.

Using a virtualenv remains the default.

- Fixes #3308 | 1 |
| Add Archer Aviation to organizations in readme (#3361) | 1 |
| Wrap concatenated strings used as function args in parens (#3307)

Fixes #3292 | 1 |
| Update README.md (#3284)

Minor typo | 1 |
| Exclude pytest-xdist 3.0.2 (#3356)

We're getting warnings like https://github.com/psf/black/actions/runs/3325521041/jobs/5498291478 and I'm not sure how to fix them. I'll open an issue for a long-term solution, but for now avoid 3.0.2 to unbreak CI. | 1 |
| Enforce empty lines before classes/functions with sticky leading comments. (#3302)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump peter-evans/create-or-update-comment from 2.0.0 to 2.0.1 (#3354)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 2.0.0 to 2.0.1.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/c9fcb64660bc90ec1cc535646af190c992007c32...2b2c85d0bf1b8a7b4e7e344bd5c71dc4b9196e9f)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump peter-evans/find-comment from 2.0.0 to 2.0.1 (#3353)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 2.0.0 to 2.0.1.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/1769778a0c5bd330272d749d12c036d65e70d39d...b657a70ff16d17651703a84bee1cb9ad9d2be2ea)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump sphinx from 5.2.3 to 5.3.0 in /docs (#3333)

updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-minor

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Clarify check argument is needed for github action workflow (#3325)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| remove unreachable code (#3328)

fixes #3321 | 1 |
| Fix license metadata to follow PEP 621 (#3326) | 1 |
| Add support for named exprs inside function calls as gen-exps (#3327) | 1 |
| Remove redundant 3.6 code and bump mypy's python_version to 3.7 (#3313) | 1 |
| Prepare release 22.10.0 (#3311) | 1 |
| Add option to skip the first line of source code (#3299)

* Add option to skip the first line in source file

This commit adds a CLi option to skip the first line in the source
files, just like the Cpython command line allows [1]. By enabling the
flag, using `-x` or `--skip-source-first-line`, the first line is
removed temporarilly while the remaining contents are formatted. The
first line is added back before returning the formatted output.

[1]: https://docs.python.org/dev/using/cmdline.html#cmdoption-x

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Add tests for `--skip-source-first-line` option

When the flag is disabled (default), black formats the entire source
file, as in every line. In the other hand, if the flag is enabled, by
using `-x` or `--skip-source-first-line`, the first line is retained
while the rest of the source is formatted and then is added back.

These tests use an empty Python file that contains invalid syntax in
its first line (`invalid_header.py`, at `miscellaneous/`). First,
Black is invoked without enabling the flag which should result in an
exit code different than 0. When the flag is enabled, Black is
expected to return a successful exit code and the header is expected
to be retained (even if its not valid Python syntax).

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Support skip source first line option for blackd

The recently added option can be added as an acceptable header for
blackd. The arguments are passed in such a way that using the new
header will activate the skip source first line behaviour as expected

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Add skip source first line option to blackd docs

The new option can be passed to blackd as a header. This commit
updates the blackd docs to include the new header.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Update CHANGES.md

Include the new Black option to skip the first line of source code in
the configuration section

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Update skip first line test including valid syntax

Including valid Python syntax help us make sure that the file is still
actually valid after skipping the first line of the source file (which
contains invalid Python syntax)

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Skip first source line at `format_file_in_place`

Instead of skipping the first source line at `format_file_contents`,
do it before. This allow us to find the correct newline and encoding
on the actual source code (everything that's after the header).

This change is also applied at Blackd: take the header before passing
the source to `format_file_contents` and put the header back once we
get the formatted result.

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Test output newlines when skipping first line

When skipping the first line of source code, the reference newline must
be taken from the second line of the file instead of the first one, in
case that the file mixes more than one kind of newline character

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Test that Blackd also skips first line correctly

Simliarly to the Black tests, we first compare that Blackd fails when
the first line is invalid Python syntax and then check that the result
is the expected when tha flag is activated

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl>

* Use the content encoding to decode the header

When decoding the header to put it back at the top of the contents of
the file, use the same encoding used in the content. This should be a
better "guess" that using the default value

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Preserve crlf line endings in blackd (#3257)

Co-authored-by: KotlinIsland <kotlinisland@users.noreply.github.com> | 1 |
| Bump docutils from 0.18.1 to 0.19 in /docs (#3161)

Bumps [docutils](https://docutils.sourceforge.io/) from 0.18.1 to 0.19.

---
updated-dependencies:
- dependency-name: docutils
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com> | 1 |
| Bump sphinx from 5.2.1 to 5.2.3 in /docs (#3305)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 5.2.1 to 5.2.3.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/5.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v5.2.1...v5.2.3)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump furo from 2022.9.15 to 2022.9.29 in /docs (#3304)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.9.15 to 2022.9.29.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.09.15...2022.09.29)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Co-authored-by: Cooper Lees <cooper@fb.com> | 1 |
| Bump myst-parser from 0.18.0 to 0.18.1 in /docs (#3303)

Bumps [myst-parser](https://github.com/executablebooks/MyST-Parser) from 0.18.0 to 0.18.1.
- [Release notes](https://github.com/executablebooks/MyST-Parser/releases)
- [Changelog](https://github.com/executablebooks/MyST-Parser/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/MyST-Parser/compare/v0.18.0...v0.18.1)

---
updated-dependencies:
- dependency-name: myst-parser
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Add .ipynb_checkpoints to DEFAULT_EXCLUDES (#3293)

Jupyter creates a checkpoint file every single time you create an .ipynb
file, and then it updates the checkpoint file every single time you
manually save your progress for the initial .ipynb. These checkpoints
are stored in a directory named `.ipynb_checkpoints`.

Co-authored-by: Batuhan Taskaya <isidentical@gmail.com> | 1 |
| Enable build isolation under CIWB (#3297)

No idea how this is still here after the Hatchling PR, but it is no
longer useful and is breaking the build. | 1 |
| Bump pypa/cibuildwheel from 2.10.0 to 2.10.2 (#3290)

updated-dependencies:
- dependency-name: pypa/cibuildwheel
  dependency-type: direct:production
  update-type: version-update:semver-patch

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Mention CHANGES.md in PR template explicitly (#3295)

This makes the location more explicit which hopefully makes the PR
process smoother for other first time contributors.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Add option to format Jupyter Notebooks in GitHub Action (#3282)

To run the formatter on Jupyter Notebooks, Black must be installed
with an extra dependency (`black[jupyter]`). This commit adds an
optional argument to install Black with this dependency when using the
official GitHub Action [1]. To enable the formatter on Jupyter
Notebooks, just add `jupyter: true` as an argument. Feature requested
at [2].

[1]: https://black.readthedocs.io/en/stable/integrations/github_actions.html
[2]: https://github.com/psf/black/issues/3280

Signed-off-by: Antonio Ossa Guerra <aaossa@uc.cl> | 1 |
| Bump sphinx from 5.1.1 to 5.2.1 in /docs (#3288)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 5.1.1 to 5.2.1.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/5.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v5.1.1...v5.2.1)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump actions/upload-artifact from 2 to 3 (#3289)

updated-dependencies:
- dependency-name: actions/upload-artifact
  dependency-type: direct:production
  update-type: version-update:semver-major

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Always call freeze_support() if sys.frozen is True (#3275) | 1 |
| Fix outdated references to 3.6 and run pyupgrade (#3286)

I also missed the accidental removal of the 3.11 classifier in the PR. | 1 |
| Switch build backend to Hatchling (#3233)

This implements PEP 621, obviating the need for `setup.py`, `setup.cfg`,
and `MANIFEST.in`. The build backend Hatchling (of which I am a
maintainer in the PyPA) is now used as that is the default in the
official Python packaging tutorial. Hatchling is available on all the
major distribution channels such as Debian, Fedora, and many more.

## Python support

The earliest supported Python 3 version of Hatchling is 3.7, therefore
I've also set that as the minimum here. Python 3.6 is EOL and other
build backends like flit-core and setuptools also dropped support.
Python 3.6 accounted for 3-4% of downloads in the last month.

## Plugins 

Configuration is now completely static with the help of 3 plugins:

### Readme

hynek's hatch-fancy-pypi-readme allows for the dynamic construction of
the readme which was previously coded up in `setup.py`. Now it's simply:

```toml
[tool.hatch.metadata.hooks.fancy-pypi-readme]
content-type = "text/markdown"
fragments = [
  { path = "README.md" },
  { path = "CHANGES.md" },
]
```

### Versioning

hatch-vcs is currently just a wrapper around setuptools-scm (which
despite the legacy naming is actually now decoupled from setuptools):

```toml
[tool.hatch.version]
source = "vcs"

[tool.hatch.build.hooks.vcs]
version-file = "src/_black_version.py"
template = '''
version = "{version}"
'''
```

### mypyc

hatch-mypyc offers many benefits over the existing approach:

- No need to manually select files for inclusion
- Avoids the need for the current CI workaround for https://github.com/mypyc/mypyc/issues/946
- Intermediate artifacts (like `build/`) from setuptools and mypyc
  itself no longer clutter the project directory
- Runtime dependencies required at build time no longer need to be
  manually redeclared as this is a built-in option of Hatchling

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Make README logo link to docs (#3285)

docs: Make README logo link to docs | 1 |
| Fix a crash when `# fmt: on` is used on a different block level than `# fmt: off` (#3281)

Previously _Black_ produces invalid code because the `# fmt: on` is used on a different block level.

While _Black_ requires `# fmt: off` and `# fmt: on` to be used at the same block level, incorrect usage shouldn't cause crashes.

The formatting behavior this PR introduces is, the code below the initial `# fmt: off` block level will be turned off for formatting, when `# fmt: on` is used on a different level or there is no `# fmt: on`. This also matches the current behavior when `# fmt: off` is used at the top-level without a matching `# fmt: on`, it turns off formatting for everything below `# fmt: off`.

- Fixes #2567
- Fixes #3184
- Fixes #2985
- Fixes #2882
- Fixes #2232
- Fixes #2140
- Fixes #1817
- Fixes #569 | 1 |
| Support version specifiers in GH action (#3265)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Make context manager examples in future style docs consistent (#3274) | 1 |
| Build mypyc wheels for CPython 3.11 (#3276)

Bumps cibuildwheel from 2.8.1 to 2.10.0 which has 3.11 building enabled
by default. Unfortunately mypyc errors out on 3.11:

src/black/files.py:29:9: error: Name "tomllib" already defined (by an import)  [no-redef]

... so we have to also hide the fallback import of tomli on older 3.11
alphas from mypy[c]. | 1 |
| Bump furo from 2022.6.21 to 2022.9.15 in /docs (#3277)

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix mypyc build errors on newer manylinux2014_x86_64 images (#3272)

Make sure `gcc` is installed in the build env

The mypyc build requires `gcc` to be installed even if it's being built with `clang`, otherwise `clang` fails to find `libgcc`. | 1 |
| Improve order of paragraphs on line splitting (#3270)

These two paragraphs were tucked away at the end of the section, after
the diversion on backslashes. I nearly missed the first paragraph and
opened a nonsense issue, and I think the second belongs higher up with
it too. | 1 |
| Fix a crash on dicts with paren-wrapped long string keys (#3262)

Fix a crash when formatting some dicts with parenthesis-wrapped long
string keys. When LL[0] is an atom string, we need to check the atom
node's siblings instead of LL[0] itself, e.g.:

    dictsetmaker
      atom
        STRING '"This is a really long string that can\'t be expected to fit in one line and is used as a nested dict\'s key"'
      /atom
      COLON ':'
      atom
        LSQB ' ' '['
        listmaker
          STRING '"value"'
          COMMA ','
          STRING ' ' '"value"'
        /listmaker
        RSQB ']'
      /atom
      COMMA ','
    /dictsetmaker | 1 |
| [FIX] migrate-black.py: don't fail on binary files (#3266) | 1 |
| Move 3.11 tests to install aiohttp without C extensions (#3258)

* Move 311 tests to install aiohttp without C extensions

- Configure tox to install aiohttp without extensions
  - i.e. use `AIOHTTP_NO_EXTENSIONS=1` for pip install
  - This allows us to reenable blackd tests that use aiohttp testing helpers etc.
- Had to ignore `cgi` module deprecation warning
  - Filed issue for aiohttp to fix: https://github.com/aio-libs/aiohttp/issues/6905

Test:
- `/tmp/tb/bin/tox -e 311`

* Fix formatting + linting

* Add latest aiohttp for loop fix + Try to exempt deprecation warning but failed - will ask for help

* Remove unnecessary warning ignore

Co-authored-by: Cooper Ry Lees <me@wcooperlees.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Mitigate deprecation of aiohttp's `@middleware` decorator (#3259)

This is deprecated since aiohttp 4.0. If it doesn't exist just define a
no-op decorator that does nothing (after the other aiohttp imports
though!). By doing this, it's safe to ignore the DeprecationWarning
without needing to require the latest aiohttp once they remove
`@middleware`. | 1 |
| Add preview flag to Vim plugin (#3246)

This allows the configuration of the --preview flag in the Vim plugin. | 1 |
| docs: adds ExitStack alternative to future_style.md (#3247)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Prepare docs for release 22.8.0 (#3248) | 1 |
| Update stable branch after publishing to PyPI (#3223)

We've decided to a) convert stable back into a branch and b) to update
it immediately as part of the release process. We may as well automate
it. And about going back to a branch ...

Git tags are not the right tool, at all[^1]. They come with the
expectation that they will never change. Things will not work as
expected if they do change, doubly so if they change regularly. Once
you pull stable from the remote and it's copied in your local
repository, no matter how many times you run git pull you'll never see
it get updated automatically. Your only recourse is to delete the tag
via `git tag -d stable` before pulling.

This gets annoying really quickly since stable is supposed to be the
solution for folks "who want to move along as Black developers deem
the newest version reliable."[^2] See this comment for how this impacts
users using our Vim plugin[^3]. It also affects us developers[^4]. If
you have stable locally, once we cut a new release and update the stable
tag, a simple `git pull` / `git fetch` will not pull down the updated
stable tag. Unless you remember to delete stable before pulling, stable
will become stale and useless.

You can argue this is a good thing ("people should explicitly opt into
updating stable"), but IMO it does not match user expectations nor
developer expectations[^5]. Especially since not all our integrations
that use stable are bound by this security measure, for example our
GitHub Action (since it does a clean fetch of the repository every time
it's used). I believe consistency would be good here.

Finally, ever since we switched to a tag, we've been facing issues with
ReadTheDocs not picking up updates to stable unless we force a rebuild.
The initial rebuild on the stable update just pulls the commit the tag
previously pointed to. I'm not sure if switching back to a branch will
fix this, but I'd wager it will.

[^1]: https://git-scm.com/docs/git-tag#_on_re_tagging

[^2]: https://black.readthedocs.io/en/stable/contributing/release_process.html#moving-the-stable-tag

[^3]: https://github.com/psf/black/issues/2503#issuecomment-1196357379

[^4]: In fairness, most folks working on Black probably don't use the
      `stable` ref anyway, especially us maintainers who'd know what is
      the latest version by heart, but it'd still be nice to make it
      usable for local dev though.

[^5]: Also what benefit does a `stable` ref have over explicit version
      tags like `22.6.0`? If you're going to opt into some odd pin
      mechanism, might as well use explicit version tags for clarity
      and consistency. | 1 |
| Improve & update release process to reflect recent changes (#3242)

- Formalise release cadence guidelines
- Overhaul release steps to be easier to follow and more thorough
- Reorder changelog template to something more sensible
- Update release automation docs to reflect recent improvements (notably
  the addition of in-repo mypyc wheel builds)

Co-authored-by: Felix Hildén <felix.hilden@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Use .gitignore files in the initial source directories (#3237)

Solves https://github.com/psf/black/issues/2598 where Black wouldn't
use .gitignore at folder/.gitignore if you ran `black folder` for
example.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Use strict mypy checking (#3222)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Add parens around implicit string concatenations where it increases readability (#3162)

Adds parentheses around implicit string concatenations when it's inside
a list, set, or tuple. Except when it's only element and there's no trailing
comma.

Looking at the order of the transformers here, we need to "wrap in
parens" before string_split runs. So my solution is to introduce a
"collaboration" between StringSplitter and StringParenWrapper where the
splitter "skips" the split until the wrapper adds the parens (and then
the line after the paren is split by StringSplitter) in another pass.

I have also considered an alternative approach, where I tried to add a
different "string paren wrapper" class, and it runs before string_split.
Then I found out it requires a different do_transform implementation
than StringParenWrapper.do_transform, since the later assumes it runs
after the delimiter_split transform. So I stopped researching that
route.

Originally function calls were also included in this change, but given
missing commas should usually result in a runtime error and the scary
amount of changes this cause on downstream code, they were removed in
later revisions. | 1 |
| Delay worker count determination

os.cpu_count() can return None (sounds like a super arcane edge case
though) so the type annotation for the `workers` parameter of
`black.main` is wrong. This *could* technically cause a runtime
TypeError since it'd trip one of mypyc's runtime type checks so we
might as well fix it.

Reading the documentation (and cross-checking with the source code),
you are actually allowed to pass None as `max_workers` to
`concurrent.futures.ProcessPoolExecutor`. If it is None, the pool
initializer will simply call os.cpu_count() [^1] (defaulting to 1 if it
returns None [^2]). It'll even round down the worker count to a level
that's safe for Windows.

... so theoretically we don't even need to call os.cpu_count()
ourselves, but our Windows limit is 60 (unlike the stdlib's 61) and I'd
prefer not accidentally reintroducing a crash on machines with many,
many CPU cores.

[^1]: https://docs.python.org/3/library/concurrent.futures.html#concurrent.futures.ProcessPoolExecutor
[^2]: https://github.com/python/cpython/blob/a372a7d65320396d44e8beb976e3a6c382963d4e/Lib/concurrent/futures/process.py#L600 | 1 |
| Load .gitignore and exclude regex at time of use

Loading .gitignore and compiling the exclude regex can take more than
15ms. We shouldn't and don't need to pay this cost if we're simply
formatting files given on the command line directly.

I would've loved to lazily import pathspec, but the patch won't be clean
until the file collection and discovery logic is refactored first.

Co-authored-by: Fabio Zadrozny <fabiofz@gmail.com> | 1 |
| Lazily import parallelized format modules

`black.reformat_many` depends on a lot of slow-to-import modules. When
formatting simply a single file, the time paid to import those modules
is totally wasted. So I moved `black.reformat_many` and its helpers
to `black.concurrency` which is now *only* imported if there's more
than one file to reformat. This way, running Black over a single file
is snappier

Here are the numbers before and after this patch running `python -m
black --version`:

- interpreted: 411 ms +- 9 ms -> 342 ms +- 7 ms: 1.20x faster
- compiled: 365 ms +- 15 ms -> 304 ms +- 7 ms: 1.20x faster

Co-authored-by: Fabio Zadrozny <fabiofz@gmail.com> | 1 |
| Fix misdetection of project root with `--stdin-filename` (#3216)

There are a number of places this behaviour could be patched, for
instance, it's quite tempting to patch it in `get_sources`. However
I believe we generally have the invariant that project root contains all
files we want to format, in which case it seems prudent to keep that
invariant.

This also improves the accuracy of the "sources to be formatted" log
message with --stdin-filename.

Fixes GH-3207. | 1 |
| Remove hacky subprocess call in action.yml (#3226)

Updates action.yml to use the alternative $GITHUB_ACTION_PATH variable
instead of the original ${{ github.action_path }} which caused issues
with bash on the Windows runners. This removes the need for a Python
subprocess to call the main.py script. | 1 |
| Fix a string merging/split issue caused by standalone comments. (#3227)

Fixes #2734: a standalone comment causes strings to be merged into one far too long (and requiring two passes to do so).

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Add passing 3.11 CI by exempting blackd tests (#3234)

- Had to exempt blackd tests for now due to aiohttp
  - Skip by using `sys.version_info` tuple
  - aiohttp does not compile in 3.11 yet - refer to #3230
- Add a deadsnakes ubuntu workflow to run 3.11-dev to ensure we don't regress
  - Have it also format ourselves

Test:
- `tox -e 311`

Co-authored-by: Cooper Ry Lees <me@wcooperlees.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Update email (#3235)

This file gets scraped a lot, so create a distinct email for potential
spam. | 1 |
| Strip trailing commas in subscripts with -C (#3209)

Fixes #2296, #3204 | 1 |
| Port & upstream mypyc wheel build workflow  (#3197) | 1 |
| add preview option support for blackd (#3217)

Fixes #3195

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Use --no-implicit-optional for type checking (#3220)

This makes type checking PEP 484 compliant (as of 2018).
mypy will change its defaults soon.

See:
https://github.com/python/mypy/issues/9091
https://github.com/python/mypy/pull/13401 | 1 |
| Use debug f-strings for feature detection (#3215)

Fixes GH-2907. | 1 |
| Remove invalid syntax in docstrings -S --preview test (#3205)

uR is not a legal string prefix, so this test breaks (AssertionError:
cannot use --safe with this file; failed to parse source file AST:
invalid syntax) if changed to one in which the file is changed. I've
changed the last test to have u alone, and added an R to the test above
instead. | 1 |
| makes install available for all users in docker image (#3202)

* makes install available for all users in docker image

moves the installation path from /root/.local to a
virtualenv. this way we still get the lightweight
multistage build without excluding non-root users.

* adds changelog entry for docker-image fix

A changelog entry has been added under the Integration
subheader

* changes dockerfile to use the venv activate script

we are now using the inbuilt venv activate script, as well
as explicitly mentioning the binary location in the entrypoint
cmd.

Co-authored-by: Nicolò <nicolo.intrieri@spinforward.it>
Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Bump sphinx from 5.1.0 to 5.1.1 in /docs (#3201)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 5.1.0 to 5.1.1.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/5.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v5.1.0...v5.1.1)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Move fuzz.py to scripts/ (#3199) | 1 |
| Add sanity check to executable CD + more (#3190)

Building executables without any testing is quite sketchy, let's at
least verify they won't crash on startup and format Black's own
codebase.

Also replaced "binaries" with "executables" since it's clearer and
won't be confused with mypyc.

Finally, I added colorama so all Windows users can get colour. | 1 |
| Remove blib2to3 grammar cache logging (#3193)

As error logs are emitted often (they happen when Black's cache
directory is created after blib2to3 tries to write its cache) and cause
issues to be filed by users who think Black isn't working correctly.

These errors are expected for now and aren't a cause for concern so
let's remove them to stop worrying users (and new issues from being
opened). We can improve the blib2to3 caching mechanism to write its
cache at the end of a successful command line invocation later. | 1 |
| Vim plugin: prefix messages with "Black: " (#3194)

As mentioned in GH-3185, when using Black as a Vim plugin, especially
automatically on save, the plugin's messages can be confusing, as
nothing indicates that they come from Black. | 1 |
| Consolidate test CI and add concurrency limits (#3189) | 1 |
| Bump pre-commit hooks (#3191) | 1 |
| Reformat codebase with isort | 1 |
| Add isort to linting toolchain

Co-authored-by: Shivansh-007 <shivansh-007@outlook.com> | 1 |
| Bump sphinx from 5.0.2 to 5.1.0 in /docs (#3183)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 5.0.2 to 5.1.0.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/5.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v5.0.2...v5.1.0)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Use underscores instead of a space in a test file's name (#3180)

... for *consistency* | 1 |
| Fix an infinite loop when using `# fmt: on/off` ... (#3158)

... in the middle of an expression or code block by adding a missing return.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix the handling of `# fmt: skip` when it's at a colon line (#3148)

When the Leaf node with `# fmt: skip` is a NEWLINE inside a `suite`
Node, the nodes to ignore should be from the siblings of the parent
`suite` Node.

There is a also a special case for the ASYNC token, where it expands
to the grandparent Node where the ASYNC token is.

This fixes GH-2646, GH-3126, GH-2680, GH-2421, GH-2339, and GH-2138. | 1 |
| Improve warning filtering in tests (#3175) | 1 |
| Add pypy-3.8 to test matrix (#3174) | 1 |
| configure strict pytest and filterwarnings=['error', ... (#3173)

* configure strict pytest

* ignore current warnings | 1 |
| Fix typo in config docs for --extend-exclude (#3170)

The old regex in the example was invalid and caused an error on startup. | 1 |
| Actually disable docstring prefix normalization with -S + fix instability (#3168)

The former was a regression I introduced a long time ago. To avoid
changing the stable style too much, the regression is only fixed if
--preview is enabled

Annoyingly enough, as we currently always enforce a second format pass if
changes were made, there's no good way to prove the existence of the
docstring quote normalization instability issue. For posterity, here's
one failing example:

    --- source
    +++ first pass
    @@ -1,7 +1,7 @@
     def some_function(self):
    -    ''''<text here>
    +    """ '<text here>

         <text here, since without another non-empty line black is stable>

    -    '''
    +    """
         pass
    --- first pass
    +++ second pass
    @@ -1,7 +1,7 @@
     def some_function(self):
    -    """ '<text here>
    +    """'<text here>

         <text here, since without another non-empty line black is stable>

         """
         pass

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Move to explicitly creating a new loop (#3164)

* Move to explicitly creating a new loop

- >= 3.10 add a warning that `get_event_loop` will not automatically create a loop
- Move to explicit API

Test:
- `python3.11 -m venv --upgrade-deps /tmp/tb`
  - `/tmp/tb/bin/pip install -e .`
  - Install deps and no blackd as aiohttp + yarl can't build still with 3.11
  - https://github.com/aio-libs/aiohttp/issues/6600
- `export PYTHONWARNINGS=error`
```
cooper@l33t:~/repos/black$ /tmp/tb/bin/black .
All done! ✨ 🍰 ✨
44 files left unchanged.
```

Fixes #3110

* Add to CHANGES.md

* Fix a cooper typo yet again

* Set default asyncio loop to our explicitly created one + unset on exit

* Update CHANGES.md

Fix my silly typo.

Co-authored-by: Thomas Grainger <tagrain@gmail.com>

Co-authored-by: Cooper Ry Lees <me@wcooperlees.com>
Co-authored-by: Thomas Grainger <tagrain@gmail.com> | 1 |
| Add warning to not run blackd publicly in docs (#3167)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Don't (ever) put a single-char closing docstring quote on a new line (#3166)

Doing so is invalid. Note this only fixes the preview style since the
logic putting closing docstring quotes on their own line if they violate
the line length limit is quite new.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Copy over comments when hugging power ops  (#2874)

Otherwise they'd be deleted which was a regression in 22.1.0 (oops! my
bad!). Also type comments are now tracked in the AST safety check on all
compatible platforms to error out if this happens again.

Overall the line rewriting code has been rewritten to do "the right
thing (tm)", I hope this fixes other potential bugs in the code (fwiw I
got to drop the bugfix in blib2to3.pytree.Leaf.clone since now bracket
metadata is properly copied over).

Fixes #2873 | 1 |
| Recommend using BlackConnect in IntelliJ IDEs (#3150)

* Recommend using BlackConnect in IntelliJ IDEs

* IntelliJ IDEs integration docs: improve formatting

* Add changelog for recommending BlackConnect

* IntelliJ IDEs integration docs: improve formatting

* Apply suggestions from code review

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

* Fix indentation

* Apply italic to Black name

Consequently with other places in the document

* Move CHANGELOG entry to Unreleased section

* IntelliJ IDEs integration docs: bring back a point with formatting a file

* IntelliJ IDEs integration docs: fix extra whitespace and linebreak

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Stability policy: permit exceptional changes for unformatted code (#3155) | 1 |
| Use RTD's new build process and config (#3149)

See the deprecation notice:
https://docs.readthedocs.io/en/stable/config-file/v2.html#python-version | 1 |
| Fix typo in CHANGES.md (#3142) | 1 |
| Prepare docs for release 22.6.0 (#3139) | 1 |
| Update preview style docs to include recent changes (#3136)

Covers GH-2926, GH-2990, GH-2991, and GH-3035.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Bump furo from 2022.6.4.1 to 2022.6.21 in /docs (#3138)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.6.4.1 to 2022.6.21.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.06.04.1...2022.06.21)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Only call get_future_imports when needed (#3135) | 1 |
| Bump sphinx from 5.0.1 to 5.0.2 in /docs (#3128)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 5.0.1 to 5.0.2.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/5.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v5.0.1...v5.0.2)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Replace link to Requests documentation (#3125) | 1 |
| Test run black on self (#3114)

* Add run_self environment in tox

* Add run_self task as part of the lint CI flow

* Remove hard coded sources list

* Remove black from pre-commit

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Bump actions/setup-python from 3 to 4 (#3121)

Bumps [actions/setup-python](https://github.com/actions/setup-python) from 3 to 4.
- [Release notes](https://github.com/actions/setup-python/releases)
- [Commits](https://github.com/actions/setup-python/compare/v3...v4)

---
updated-dependencies:
- dependency-name: actions/setup-python
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com> | 1 |
| Use is_number_token instead of assertion (#3069) | 1 |
| Update documentation dependencies (#3118)

Furo, myst-parser, and Sphinx (had to pin docutils due to sphinx breakage) | 1 |
| Remove newline after code block open (#3035)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Bump pre-commit/action from 2.0.3 to 3.0.0 (#3108)

Bumps [pre-commit/action](https://github.com/pre-commit/action) from 2.0.3 to 3.0.0.
- [Release notes](https://github.com/pre-commit/action/releases)
- [Commits](https://github.com/pre-commit/action/compare/v2.0.3...v3.0.0)

---
updated-dependencies:
- dependency-name: pre-commit/action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Fix minor typo (#3096) | 1 |
| Add script to ease migration to black (#3038)

* Add script to ease migration to black

* Update CHANGES.md

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Implement support for PEP 646 (#3071) | 1 |
| Add more examples to exclude files in addition to the defaults (#3070)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Document new Microsoft Black Formatter extension for VSCode (#3063)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Remove hard coded test cases (#3062) | 1 |
| Bump docker/setup-buildx-action from 1 to 2 (#3058)

Bumps [docker/setup-buildx-action](https://github.com/docker/setup-buildx-action) from 1 to 2.
- [Release notes](https://github.com/docker/setup-buildx-action/releases)
- [Commits](https://github.com/docker/setup-buildx-action/compare/v1...v2)

---
updated-dependencies:
- dependency-name: docker/setup-buildx-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/login-action from 1 to 2 (#3059)

Bumps [docker/login-action](https://github.com/docker/login-action) from 1 to 2.
- [Release notes](https://github.com/docker/login-action/releases)
- [Commits](https://github.com/docker/login-action/compare/v1...v2)

---
updated-dependencies:
- dependency-name: docker/login-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/build-push-action from 2 to 3 (#3057)

Bumps [docker/build-push-action](https://github.com/docker/build-push-action) from 2 to 3.
- [Release notes](https://github.com/docker/build-push-action/releases)
- [Commits](https://github.com/docker/build-push-action/compare/v2...v3)

---
updated-dependencies:
- dependency-name: docker/build-push-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump docker/setup-qemu-action from 1 to 2 (#3056)

Bumps [docker/setup-qemu-action](https://github.com/docker/setup-qemu-action) from 1 to 2.
- [Release notes](https://github.com/docker/setup-qemu-action/releases)
- [Commits](https://github.com/docker/setup-qemu-action/compare/v1...v2)

---
updated-dependencies:
- dependency-name: docker/setup-qemu-action
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Read simple data cases automatically (#3034)

Co-authored-by: Felix Hildén <felix.hilden@gmail.com> | 1 |
| Put closing quote on a separate line if docstring is too long (#3044)

Fixes #1632

Co-authored-by: Felix Hildén <felix.hilden@gmail.com> | 1 |
| Docs: clarify fmt:on/off requirements (#2985) (#3048) | 1 |
| Move imports of `ThreadPoolExecutor` into `reformat_many()`, allowing Black-in-the-browser (#3046)

This is a slight perf win for use-cases that don't invoke `reformat_many()`, but more importantly to me today it means I can use Black in pyscript. | 1 |
| chore: Set permissions for GitHub actions (#3043)

Restrict the GitHub token permissions only to the required ones; this way, even if the attackers will succeed in compromising your workflow, they won’t be able to do much.

- Included permissions for the action. https://github.com/ossf/scorecard/blob/main/docs/checks.md#token-permissions

https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#permissions

https://docs.github.com/en/actions/using-jobs/assigning-permissions-to-jobs

[Keeping your GitHub Actions and workflows secure Part 1: Preventing pwn requests](https://securitylab.github.com/research/github-actions-preventing-pwn-requests/)

Signed-off-by: naveen <172697+naveensrinivasan@users.noreply.github.com> | 1 |
| Bump myst-parser from 0.16.1 to 0.17.2 in /docs (#3019)

Bumps [myst-parser](https://github.com/executablebooks/MyST-Parser) from 0.16.1 to 0.17.2.
- [Release notes](https://github.com/executablebooks/MyST-Parser/releases)
- [Changelog](https://github.com/executablebooks/MyST-Parser/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/MyST-Parser/compare/v0.16.1...v0.17.2)

---
updated-dependencies:
- dependency-name: myst-parser
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Stop pinning lark-parser (#3041)

- Latest version works more

Test: `tox -e fuzz` | 1 |
| Fix strtobool function (#3025)

* Fix strtobool function for vim plugin
* Update CHANGES.md

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Updated Black Docker Hub link in docs (#3023)

Fixes #3022 | 1 |
| Support 3.11 / PEP 654 syntax (#3016) | 1 |
| Make ipynb tests compatible with ipython 8.3.0+ (#3008) | 1 |
| Quote black[jupyter] and black[d] in installation docs (#3006)

We just got someone on Discord who was confused because the command as
written caused their shell to try to do command expansion.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Bump furo from 2022.3.4 to 2022.4.7 in /docs (#3003)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.3.4 to 2022.4.7.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.03.04...2022.04.07)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Quote "black[jupyter]" in README.md (#3007) | 1 |
| Bump actions/upload-artifact from 2 to 3 (#3004)

Bumps [actions/upload-artifact](https://github.com/actions/upload-artifact) from 2 to 3.
- [Release notes](https://github.com/actions/upload-artifact/releases)
- [Commits](https://github.com/actions/upload-artifact/compare/v2...v3)

---
updated-dependencies:
- dependency-name: actions/upload-artifact
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Explain our use of mypyc in the FAQ (#3002)

I realized we don't have a FAQ entry about this, let's change that so
compiled: yes/no doesn't surprise as many people :)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Correctly handle fmt: skip comments without internal spaces (#2970)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Remove redundant parentheses around awaited coroutines/tasks (#2991)

This is a tricky one as await is technically an expression and therefore
in certain situations requires brackets for operator precedence.
However, the vast majority of await usage is just await some_coroutine(...)
and similar in format to return statements. Therefore this PR removes
redundant parens around these await expressions.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Better manage return annotation brackets (#2990)

Allows us to better control placement of return annotations by:

a) removing redundant parens
b) moves very long type annotations onto their own line

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Output python version and implementation as part of `--version` flag (#2997)

Example:

black, 22.1.1.dev56+g421383d.d20220405 (compiled: no)
Python (CPython) 3.9.12

Co-authored-by: Batuhan Taskaya <isidentical@gmail.com> | 1 |
| Top PyPI Packages: Use 30-days data, 365 is no longer available (#2995) | 1 |
| Update FAQ: Mention formatting of custom jupyter cell magic (#2982)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Update test_black.shhh_click test for click 8+ (#2993)

The 8.0.x series renamed its "die on LANG=C" function and the 8.1.x
series straight up deleted it.

Unfortunately this makes this test type check cleanly hard, so we'll
just lint with click 8.1+ (the pre-commit hook configuration was changed
mostly to just evict any now unsupported mypy environments) | 1 |
| Remove unnecessary parentheses from `with` statements (#2926)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Fix broken link in README.md (#2989)

Broken when we converted some more RST docs to MyST | 1 |
| try-except tomllib import (#2987)

See #2965 

I left the version check in place because mypy doesn't generally like try-excepted imports. | 1 |
| Bump peter-evans/create-or-update-comment from 1.4.5 to 2 (#2961)

Bumps [peter-evans/create-or-update-comment](https://github.com/peter-evans/create-or-update-comment) from 1.4.5 to 2.
- [Release notes](https://github.com/peter-evans/create-or-update-comment/releases)
- [Commits](https://github.com/peter-evans/create-or-update-comment/compare/a35cf36e5301d70b76f316e867e7788a55a31dae...c9fcb64660bc90ec1cc535646af190c992007c32)

---
updated-dependencies:
- dependency-name: peter-evans/create-or-update-comment
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump peter-evans/find-comment from 1.3.0 to 2 (#2960)

Bumps [peter-evans/find-comment](https://github.com/peter-evans/find-comment) from 1.3.0 to 2.
- [Release notes](https://github.com/peter-evans/find-comment/releases)
- [Commits](https://github.com/peter-evans/find-comment/compare/d2dae40ed151c634e4189471272b57e76ec19ba8...1769778a0c5bd330272d749d12c036d65e70d39d)

---
updated-dependencies:
- dependency-name: peter-evans/find-comment
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Remove click pin in diff-shades workflow (#2979)

Click 8.1.1 was released with a fix for pallets/click#2227. | 1 |
| Add # type: ignore for click._unicodefun import (#2981) | 1 |
| Convert `index.rst` and `license.rst` to markdown (#2852)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Keep tests working w/ upcoming aiohttp 4.0.0 (#2974)

aiohttp.test_utils.unittest_run_loop was deprecated since aiohttp 3.8
and aiohttp 4 (which isn't a thing quite yet) removes it. To maintain
compatibility with the full range of versions we declare to support,
test_blackd.py will now define a no-op replacement if it can't be
imported.

Also, mypy is painfully slow to use without a cache, let's reenable it. | 1 |
| Bump actions/cache from 2.1.7 to 3 (GH-2962)

Bumps [actions/cache](https://github.com/actions/cache) from 2.1.7 to 3.
- [Release notes](https://github.com/actions/cache/releases)
- [Commits](https://github.com/actions/cache/compare/v2.1.7...v3)

---
updated-dependencies:
- dependency-name: actions/cache
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Prepare release 22.3.0 (#2968) | 1 |
| Fix _unicodefun patch code for Click 8.1.0 (#2966)

Fixes #2964 | 1 |
| Bump sphinx from 4.4.0 to 4.5.0 in /docs (GH-2959)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 4.4.0 to 4.5.0.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/4.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v4.4.0...v4.5.0)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Enforce no formatting changes for PRs via CI (GH-2951)

Now PRs will run two diff-shades jobs, "preview-changes" which formats
all projects with preview=True, and "assert-no-changes" which formats
all projects with preview=False. The latter also fails if any changes
were made.

Pushes to main will only run "preview-changes"

Also the workflow_dispatch feature was dropped since it was
complicating everything for little gain. | 1 |
| Remove unnecessary parentheses from `except` clauses (#2939)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Resolve new flake8-bugbear errors (B020) (GH-2950)

Fixes a couple places where we were using the same variable name as we
are iterating over.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Remove unnecessary parentheses from tuple unpacking in `for` loops (#2945) | 1 |
| Avoid magic-trailing-comma in single-element subscripts (#2942)

Closes #2918. | 1 |
| Github now supports .git-blame-ignore-revs (GH-2948)

It's in beta.

https://docs.github.com/en/repositories/working-with-files/using-files/viewing-a-file#ignore-commits-in-the-blame-view | 1 |
| stub style: remove some possible future changes (#2940)

Fixes #2938.

All of these suggested future changes are out of scope for an autoformatter and should be handled by a linter instead. | 1 |
| dont skip formatting #%% (#2919)

Fixes #2588 | 1 |
| Update pylint config docs (#2931) | 1 |
| Remove power hugging formatting from preview (#2928)

It is falsely placed in preview features and always formats the power operators, it was added in #2789 but there is no check for formatting added along with it. | 1 |
| Farewell black-primer, it was nice knowing you (#2924)

Enjoy your retirement at https://github.com/cooperlees/black-primer | 1 |
| Bump mypy, flake8, and pre-commit-hooks in pre-commit (GH-2922) | 1 |
| Use tomllib on Python 3.11 (#2903) | 1 |
| Fix handling of Windows junctions in normalize_path_maybe_ignore (#2904)

Fixes #2569 | 1 |
| Bump actions/setup-python from 2 to 3 (#2908)

Bumps [actions/setup-python](https://github.com/actions/setup-python) from 2 to 3.
- [Release notes](https://github.com/actions/setup-python/releases)
- [Commits](https://github.com/actions/setup-python/compare/v2...v3)

---
updated-dependencies:
- dependency-name: actions/setup-python
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Bump actions/checkout from 2 to 3 (#2909)

Bumps [actions/checkout](https://github.com/actions/checkout) from 2 to 3.
- [Release notes](https://github.com/actions/checkout/releases)
- [Changelog](https://github.com/actions/checkout/blob/main/CHANGELOG.md)
- [Commits](https://github.com/actions/checkout/compare/v2...v3)

---
updated-dependencies:
- dependency-name: actions/checkout
  dependency-type: direct:production
  update-type: version-update:semver-major
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Allow `for`'s target expression to be starred (#2879)

Fixes #2878 | 1 |
| Bump furo from 2022.2.14.1 to 2022.3.4 in /docs (#2906)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.2.14.1 to 2022.3.4.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.02.14.1...2022.03.04)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Move test for g:load_black to improve plugin performance (GH-2896)

If a vim/neovim user wishes to suppress loading the vim plugin by
setting g:load_black in their VIMRC (for me, Arch linux automatically
adds the plugin to Neovim's RTP, even though I'm not using it), the
current location of the test comes after a call to has('python3'). This
adds, in my tests, between 35 and 45 ms to Vim load time (which I know
isn't a lot but it's also unnecessary). Moving the call to
`exists('g:load_black')` to before the call to `has('python3')` removes
this unnecessary test and speeds up loading.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| replace md5 with sha256 (#2905)

MD5 is unavailable on systems with active FIPS mode. That makes black
crash when run on such systems. | 1 |
| README: fix "Pragmatism" link target (#2901)

Fixes #2897 | 1 |
| fix new formatting issue (#2895)

Race between #2889 and another PR. | 1 |
| separate CHANGELOG section for preview style (#2890) | 1 |
| Format ourselves in preview mode (#2889) | 1 |
| Bump furo from 2022.1.2 to 2022.2.14.1 in /docs (GH-2892)

Bumps [furo](https://github.com/pradyunsg/furo) from 2022.1.2 to 2022.2.14.1.
- [Release notes](https://github.com/pradyunsg/furo/releases)
- [Changelog](https://github.com/pradyunsg/furo/blob/main/docs/changelog.md)
- [Commits](https://github.com/pradyunsg/furo/compare/2022.01.02...2022.02.14.1)

---
updated-dependencies:
- dependency-name: furo
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>
Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Add special config verbose log case when black is using user-level config (#2861) | 1 |
| correct Vim integration code (#2853)

- use `Black` directly: the commands an autocommand runs are Ex commands, so no
  execute or colon is necessary.
- use an `augroup` (best practice) to prevent duplicate autocommands from
  hindering performance. | 1 |
| Isolate command line tests for notebooks from user-level config (#2854) | 1 |
| Fix typo in file_collection_and_discovery.md (GH-2860)

"you your" -> "your"

Co-authored-by: Felix Hildén <felix.hilden@gmail.com> | 1 |
| Order the disabled error codes for pylint (GH-2870)

Just make them alphabetical. | 1 |
| Avoid crashing when the user has no homedir (#2814) | 1 |
| Add Django in 'used by' section in Readme (#2875)

* Add Django in 'used by' section in Readme

* Fix Readme issue | 1 |
| Bump sphinx-copybutton from 0.4.0 to 0.5.0 in /docs (#2871)

Bumps [sphinx-copybutton](https://github.com/executablebooks/sphinx-copybutton) from 0.4.0 to 0.5.0.
- [Release notes](https://github.com/executablebooks/sphinx-copybutton/releases)
- [Changelog](https://github.com/executablebooks/sphinx-copybutton/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/sphinx-copybutton/compare/v0.4.0...v0.5.0)

---
updated-dependencies:
- dependency-name: sphinx-copybutton
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Create indentation FAQ entry (#2855)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Update description for GitHub Action `options:` argument (GH-2858)

It was missing --diff as one of the default arguments passed. | 1 |
| Isolate command line tests from user-level config (#2851) | 1 |
| Surface links to Stability Policy (GH-2848) | 1 |
| release process: formalize the changelog template (#2837)

I did this manually for the last few releases and I think it's going to be
helpful in the future too. Unfortunately this adds a little more work during
the release (sorry @cooperlees).

This change will also improve the merge conflict situation a bit, because
changes to different sections won't merge conflict.

For the last release, the sections were in a kind of random order. In the
template I put highlights and "Style" first because they're most important
to users, and alphabetized the rest. | 1 |
| Soft comparison of --required-version (#2832)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Felix Hildén <felix.hilden@gmail.com> | 1 |
| Exclude __pypackages__ by default (GH-2836)

PDM uses this as part of not-accepted-yet PEP 582. | 1 |
| Adjust `--preview` documentation (#2833) | 1 |
| Prepare docs for release 22.1.0 (GH-2826) | 1 |
| Remove test suite from setup.py (#2824)

We no longer use it | 1 |
| Update classifiers to reflect stable (#2823) | 1 |
| Add a test case to torture.py (#2822)

Co-authored-by: hauntsaninja <> | 1 |
| Fix instability due to trailing comma logic (#2572)

It was causing stability issues because the first pass
could cause a "magic trailing comma" to appear, meaning
that the second pass might get a different result. It's
not critical.

Some things format differently (with extra parens) | 1 |
| Fix arithmetic stability issue (#2817)

It turns out "simple_stmt" isn't that simple: it can contain multiple
statements separated by semicolons. Invisible parenthesis logic for
arithmetic expressions only looked at the first child of simple_stmt.
This causes instability in the presence of semicolons, since the next
run through the statement following the semicolon will be the first
child of another simple_stmt.

I believe this along with #2572 fix the known stability issues. | 1 |
| Formalise style preference description (#2818)

Closes #1256: I reworded our style docs to be more explicit about the style we're aiming for and how it is changed (or isn't). | 1 |
| torture test (#2815)

Fixes #2651. Fixes #2754. Fixes #2518. Fixes #2321.

This adds a test that lists a number of cases of unstable formatting
that we have seen in the issue tracker. Checking it in will ensure
that we don't regress on these cases. | 1 |
| Treat blank lines in stubs the same inside top-level `if` statements (#2820) | 1 |
| Elaborate on Python support policy (#2819) | 1 |
| reorganize release notes for 22.1.0 (#2790)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Tests for unicode identifiers (#2816) | 1 |
| Use parentheses on method access on float and int literals (#2799)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Felix Hildén <felix.hilden@gmail.com> | 1 |
| more trailing comma tests (#2810) | 1 |
| black-primer: stop running it (#2809)

At the moment, it's just a source of spurious CI failures and busywork
updating the configuration file.

Unlike diff-shades, it is run across many different platforms and
Python versions, but that doesn't seem essential. We already run unit
tests across platforms and versions.

I chose to leave the code around for now in case somebody is using it,
but CI will no longer run it. | 1 |
| Fix crash on some power hugging cases (#2806)

Found by the fuzzer. Repro case:

	python -m black -c 'importA;()<<0**0#' | 1 |
| properly run ourselves twice (#2807)

The previous run-twice logic only affected the stability checks but not the output. Now, we actually output the twice-formatted code. | 1 |
| Hug power operators if its operands are "simple" (#2726)

Since power operators almost always have the highest binding power in expressions, it's often more readable to hug it with its operands. The main exception to this is when its operands are non-trivial in which case the power operator will not hug, the rule for this is the following:

> For power ops, an operand is considered "simple" if it's only a NAME, numeric CONSTANT, or attribute access (chained attribute access is allowed), with or without a preceding unary operator. 

Fixes GH-538.
Closes GH-2095.

diff-shades results: https://gist.github.com/ichard26/ca6c6ad4bd1de5152d95418c8645354b

Co-authored-by: Diego <dpalma@evernote.com>
Co-authored-by: Felix Hildén <felix.hilden@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Make SRC or code mandatory and mutually exclusive (#2360) (#2804)

Closes #2360: I'd like to make passing SRC or `--code` mandatory and the arguments mutually exclusive. This will change our (partially already broken) promises of CLI behavior, but I'll comment below. | 1 |
| Use `magic_trailing_comma` and `preview` for `FileMode` in `fuzz` (#2802)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Remove Beta mentions in README + Docs (#2801)

- State we're now stable and that we'll uphold our formatting changes as per policy
- Link to The Black Style doc.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Allow blackd to be run as a package (#2800) | 1 |
| Enable pattern matching by default (#2758)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Mention "skip news" label in CHANGELOG action (#2797)

Co-authored-by: hauntsaninja <> | 1 |
| Refactor logic for stub empty lines (#2796)

This PR is intended to have no change to semantics.

This is in preparation for #2784 which will likely introduce more logic
that depends on `current_line.depth`.

Inlining the subtraction gets rid of offsetting and makes it much easier
to see what the result will be. | 1 |
| Mark Felix and Batuhan as maintainers (#2794)

Y'all deserve it :) | 1 |
| Allow setting custom cache directory on all platforms (#2739)

Fixes #2506

``XDG_CACHE_HOME`` does not work on Windows. To allow for users to set a custom cache directory on all systems I added a new environment variable ``BLACK_CACHE_DIR`` to set the cache directory. The default remains the same so users will only notice a change if that environment variable is set.

The specific use case I have for this is I need to run black on in different processes at the same time. There is a race condition with the cache pickle file that made this rather difficult. A custom cache directory will remove the race condition.

I created ``get_cache_dir`` function in order to test the logic. This is only used to set the ``CACHE_DIR`` constant. | 1 |
| Switch to Furo (#2793)

- Add Furo dependency to docs/requirements.txt
- Drop a fair bit of theme configuration
- Fix the toctree declarations in index.rst
- Move stuff around as Furo isn't 100% compatible with Alabaster

Furo was chosen as it provides excellent mobile support, user
controllable light/dark theming, and is overall easier to read | 1 |
| add wind technology software projects using black (#2792) | 1 |
| Set `click` lower bound to `8.0.0` (#2791)

Closes #2774 | 1 |
| Add support for custom python cell magics (#2744)

Fixes #2742.

This PR adds the ability to configure additional python cell magics. This
will allow formatting cells in Jupyter Notebooks that are using custom (python)
magics. | 1 |
| Hint at likely cause of ast parsing failure in error message (#2786)

Co-authored-by: Batuhan Taskaya <isidentical@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Deprecate ESP and move the functionality under --preview (#2789) | 1 |
| Fix and speedup diff-shades integration  (#2773) | 1 |
| Create --preview CLI flag (#2752) | 1 |
| Fix typo in diff_shades.yml workflow (#2778) | 1 |
| Bump sphinx from 4.3.2 to 4.4.0 in /docs (#2776)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 4.3.2 to 4.4.0.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/4.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v4.3.2...v4.4.0)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| [trivial] Use proper test cases on `unittest` (#2775) | 1 |
| Dont require typing-extensions in 3.10 (GH-2772)

3.10 ships with TypeGuard which is the newest feature we need.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| CI: add diff-shades integration (#2725)

Hopefully this makes it much easier to gauge the impacts of future
changes! | 1 |
| Added decent coloring (#2712) | 1 |
| Don't make redundant copies of the DFA (#2763) | 1 |
| Normalise string prefix order (#2297)

Closes #2171 | 1 |
| don't expect changes on poetry (#2769)

They just made themselves ESP-compliant in https://github.com/python-poetry/poetry/commit/ecb030e1f0b7c13cc11971f00ee5012e82a892bc | 1 |
| Change installation url to comply with git security change (#2765)

Co-authored-by: Jeffrey Lazar <jlazar@MacBook-Pro-2.local> | 1 |
| Change git url for pip installation in README (#2761)

* Change git url for pip installation in README

Unauthenticated git protocol was disabled recently by Github and should not be used anymore.

https://github.blog/2021-09-01-improving-git-protocol-security-github/#no-more-unauthenticated-git

* Update CHANGES.md | 1 |
| Fix handling of standalone match/case with newlines/comments (#2760)

Resolves #2759 | 1 |
| Speed up new backtracking parser (#2728) | 1 |
| Enhance `--verbose` (#2526)

Black would now echo the location that it determined as the root path
for the project if `--verbose` is enabled by the user, according to
which it chooses the SRC paths, i.e. the absolute path of the project
is `{root}/{src}`.

Closes #1880 | 1 |
| Remove Python 2 support (#2740)

*blib2to3's support was left untouched because: 1) I don't want to touch
parsing machinery, and 2) it'll allow us to provide a more useful error
message if someone does try to format Python 2 code. | 1 |
| Fix call patterns that contain as-expression on the kwargs (#2749) | 1 |
| Action: Support running in a docker container (#2748)

see: https://github.com/actions/runner/issues/716 | 1 |
| Stubs: preserve blank line between attributes and methods (#2736) | 1 |
| Improve CLI reference wording (#2753) | 1 |
| Documentation: include Wing IDE 8 integrations (GH-2733)

Wing IDE 8 now supports autoformatting w/ Black natively 🎉

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Primer: exclude crashing sqlalchemy file for now (GH-2735)

Until we can properly look into and fix it.
-> https://github.com/psf/black/issues/2734 | 1 |
| Drop upper version bounds on dependencies (GH-2718)

They mostly cause unnecessary trouble.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Support pytest 7 by fixing broken imports (GH-2705)

The tmp_path related changes are not necessary to make pytest 7 work,
but it feels more complete this way. | 1 |
| remove all type: ignores in src/black (GH-2720)

Excet
;t | 1 |
| Update contributing wording (#2719)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Support multiple top-level as-expressions on case statements (#2716) | 1 |
| Remove usage of Pipenv, rely on good ol' `pip` and `virtualenv` in docs (#2717) | 1 |
| Define is_name_token (and friends) to resolve some `type: ignore`s (GH-2714)

Gets rid of a few # type: ignores by using TypeGuard. | 1 |
| Disable universal newlines when reading TOML (#2408) | 1 |
| Bump myst-parser from 0.16.0 to 0.16.1 in /docs (#2710)

Bumps [myst-parser](https://github.com/executablebooks/MyST-Parser) from 0.16.0 to 0.16.1.
- [Release notes](https://github.com/executablebooks/MyST-Parser/releases)
- [Changelog](https://github.com/executablebooks/MyST-Parser/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/MyST-Parser/compare/v0.16.0...v0.16.1)

---
updated-dependencies:
- dependency-name: myst-parser
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump sphinx from 4.3.1 to 4.3.2 in /docs (#2709)

Bumps [sphinx](https://github.com/sphinx-doc/sphinx) from 4.3.1 to 4.3.2.
- [Release notes](https://github.com/sphinx-doc/sphinx/releases)
- [Changelog](https://github.com/sphinx-doc/sphinx/blob/4.x/CHANGES)
- [Commits](https://github.com/sphinx-doc/sphinx/compare/v4.3.1...v4.3.2)

---
updated-dependencies:
- dependency-name: sphinx
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Imply 3.8+ when annotated assigments used with unparenthesized tuples (#2708) | 1 |
| Use 'python -m build' to build wheel and source distributions (#2701) | 1 |
| Unpacking on flow constructs (return/yield) now implies 3.8+ (#2700) | 1 |
| Bump pre-commit/action from 2.0.2 to 2.0.3 (GH-2695)

Bumps [pre-commit/action](https://github.com/pre-commit/action) from 2.0.2 to 2.0.3.
- [Release notes](https://github.com/pre-commit/action/releases)
- [Commits](https://github.com/pre-commit/action/compare/v2.0.2...v2.0.3)

---
updated-dependencies:
- dependency-name: pre-commit/action
  dependency-type: direct:production
  update-type: version-update:semver-patch
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Bump myst-parser from 0.15.2 to 0.16.0 in /docs (GH-2696)

Bumps [myst-parser](https://github.com/executablebooks/MyST-Parser) from 0.15.2 to 0.16.0.
- [Release notes](https://github.com/executablebooks/MyST-Parser/releases)
- [Changelog](https://github.com/executablebooks/MyST-Parser/blob/master/CHANGELOG.md)
- [Commits](https://github.com/executablebooks/MyST-Parser/compare/v0.15.2...v0.16.0)

---
updated-dependencies:
- dependency-name: myst-parser
  dependency-type: direct:production
  update-type: version-update:semver-minor
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Include underlying error when AST safety check parsing fails (#2693) | 1 |
| use valid package-ecosystem key (#2694) | 1 |
| chore: dump docs deps and pre-commit hooks (#2676) | 1 |
| Don't colour diff headers white, only bold (GH-2691)

So people with light themed terminals can still read 'em. | 1 |
| `from __future__ import annotations` now implies 3.7+ (#2690) | 1 |
| Support as-expressions on dict items (GH-2686) | 1 |
| Show details when a regex fails to compile (GH-2678) | 1 |
| no longer expect changes on pyanalyze (#2674)

https://github.com/quora/pyanalyze/pull/316 | 1 |
| Prepare for release 21.12b0 (GH-2673)

Let's do this! | 1 |
| perf: drop the initial stack copy (#2670)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| blib2to3 can raise TokenError and IndentationError too (#2671) | 1 |
| Reorganize changelog (#2669)

I believe it would be useful to split up the long list of changes a bit more.

Specific changes:
- Removed the entry for new flake8 plugins; this is purely internal and not of interest to users
- Put regex in the packaging section
- New section for Jupyter Notebook
- New section for Python 3.10, mostly match/case stuff | 1 |
| tell users to use -t py310 (#2668) | 1 |
| Don't let TokenError bubble up from lib2to3_parse (GH-2343)

error: cannot format <string>: ('EOF in multi-line statement', (2, 0))
   
 ▲ before ▼ after

error: cannot format <string>: Cannot parse: 2:0: EOF in multi-line statement

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Make star-expression spacing consistent in match/case (#2667) | 1 |
| Remove regex dependency (GH-2663)

We were no longer using it since GH-2644 and GH-2654. This should hopefully
make using Black easier to use as there's one less compiled dependency.
The core team also doesn't have to deal with the surprisingly frequent fires
the regex packaging setup goes through.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Ensure match/case are recognized as statements (#2665) | 1 |
| Treat functions/classes in blocks as if they're nested (GH-2472)

* Treat functions/classes in blocks as if they're nested

One curveball is that we still want two preceding newlines before blocks
that are probably logically disconnected. In other words:

    if condition:

        def foo():
            return "hi"
                             # <- aside: this is the goal of this commit
    else:

        def foo():
            return "cya"
                             # <- the two newlines spacing here should stay
                             #    since this probably isn't related
    with open("db.json", encoding="utf-8") as f:
        data = f.read()

Unfortunately that means we have to special case specific clause types
instead of just being able to just for a colon leaf. The hack used here
is to check whether we're adding preceding newlines for a standalone or
dependent clause. "Standalone" being a clause that doesn't need another
clause to be valid (eg. if) and vice versa.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| slightly better example link (#2617)

Since we also need to update two places in the docs | 1 |
| Fix determination of f-string expression spans (#2654)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| fix error message for match (#2649)

Fixes #2648.

Co-authored-by: Batuhan Taskaya <isidentical@gmail.com> | 1 |
| Reduce usage of regex (#2644)

This removes all but one usage of the `regex` dependency. Tricky bits included:
- A bug in test_black.py where we were incorrectly using a character range. Fix also submitted separately in #2643.
- `tokenize.py` was the original use case for regex (#1047). The important bit is that we rely on `\w` to match anything valid in an identifier, and `re` fails to match a few characters as part of identifiers. My solution is to instead match all characters *except* those we know to mean something else in Python: whitespace and ASCII punctuation. This will make Black able to parse some invalid Python programs, like those that contain non-ASCII punctuation in the place of an identifier, but that seems fine to me.
- One import of `regex` remains, in `trans.py`. We use a recursive regex to parse f-strings, and only `regex` supports that. I haven't thought of a better fix there (except maybe writing a manual parser), so I'm leaving that for now.

My goal is to remove the `regex` dependency to reduce the risk of breakage due to dependencies and make life easier for users on platforms without wheels. | 1 |
| Fix line generation for `match match:` / `case case:` (GH-2661) | 1 |
| add FAQ entry about undetected syntax errors (#2645)

This came up in #2644. | 1 |
| Remove hidden import from PyInstaller build (#2657)

The recent 2021.4 release of pyinstaller-hooks-contrib now contains a
built-in hook for platformdirs. Manually specifying the hidden import
arg should no longer be needed. | 1 |
| Allow top-level starred expression on match (#2659)

Fixes #2647 | 1 |
| Return `NothingChanged` if non-Python cell magic is detected, to avoid tokenize error (#2630)

Fixes https://github.com/psf/black/issues/2627 , a non-Python cell magic such as `%%writeline` can legitimately contain "incorrect" indentation, however this causes `tokenize-rt` to return an error. To avoid this, `validate_cell` should early detect cell magics (just like it detects `TransformerManager` transformations).

Test added too, in the shape of a "badly indented" `%%writefile` within `test_non_python_magics`.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Marco Edward Gorelli <marcogorelli@protonmail.com> | 1 |
| add more flake8 lints (#2653) | 1 |
| add missing f-string (#2650) | 1 |
| Assignment to env var in Jupyter Notebook doesn't round-trip (#2642)

closes #2641 | 1 |
| fix regex (#2643) | 1 |
| README: Add KeepTruckin to the list of orgs (GH-2638)

At KT, we used Black to format all Python code in our Mono-repo.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| grammar: accept open sequences on match subject (GH-2639)

* grammar: accept open sequences on match subject
* give an example about the fixed match subject | 1 |
| Change `cfg` to `ini` for text highlighting (#2632) | 1 |
| Fix process pool fallback on Python 3.10 (GH-2631)

In Python 3.10 the exception generated by creating a process pool on
a Python build that doesn't support this is now `NotImplementedError`

Commit history before merge:

* Fix process pool fallback on Python 3.10
* Update CHANGES.md
* Update CHANGES.md

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix mypyc compat issue w/ AST safety check (GH-2628)

I can't wait for when we drop Python 2 support FWIW :) | 1 |
| prepare release 2021.11b1 (#2622) | 1 |
| Bump regex dependency to 2021.4.4 to fix import of Pattern class (#2621)

Fixes #2620 | 1 |
| prepare release 21.11b0 (#2616) | 1 |
| fix vim plugin (#2615) | 1 |
| Fix 3.10's supported features (#2614) | 1 |
| Implementing mypyc support pt. 2  (#2431) | 1 |
| vim: Parse skip_magic_trailing_comma from pyproject.toml (#2613)

Co-authored-by: Kyle Kovacs <kkovacs@diconfiberoptics.com> | 1 |
| Docker image usage description (#2412)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| black/parser: optimize deepcopying nodes (#2611)

The implementation of the new backtracking logic depends heavily on deepcopying the current state of the parser before seeing one of the new keywords, which by default is an very expensive operations. On my system, formatting these 3 files takes 1.3 seconds.

```
 $ touch tests/data/pattern_matching_*; time python -m black -tpy310 tests/data/pattern_matching_*             19ms
All done! ✨ 🍰 ✨
3 files left unchanged.
python -m black -tpy310 tests/data/pattern_matching_*  2,09s user 0,04s system 157% cpu 1,357 total
```

which can be optimized 3X if we integrate the existing copying logic (`clone`) to the deepcopy system;
```
 $ touch tests/data/pattern_matching_*; time python -m black -tpy310 tests/data/pattern_matching_*              1ms
All done! ✨ 🍰 ✨
3 files left unchanged.
python -m black -tpy310 tests/data/pattern_matching_*  0,66s user 0,02s system 147% cpu 0,464 total
```

This still might have some potential, but that would be way trickier than this initial patch. | 1 |
| Removed distutils import from autoload/black.vim (#2607) (#2610) | 1 |
| Declare support for Python 3.10 (#2562) | 1 |
| black/parser: support as-exprs within call args (#2608) | 1 |
| Allow install under pypy (#2559)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| black/parser: partial support for pattern matching (#2586)

Partial implementation for #2242. Only works when explicitly stated -t py310.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Bump deps in Pipfile.lock (GH-2605)

Mostly because the hashes for typed-ast were valid for 1.4.2 when the
version is pinned to 1.4.3 ... pipenv is pleasant to use /s | 1 |
| Fix typos (#2603) | 1 |
| Improve Python 2 only syntax detection (GH-2592)

* Improve Python 2 only syntax detection

First of all this fixes a mistake I made in Python 2 deprecation PR
using token.* to check for print/exec statements. Turns out that
for nodes with a type value higher than 256 its numeric type isn't
guaranteed to be constant. Using syms.* instead fixes this.

Also add support for the following cases:

    print "hello, world!"

    exec "print('hello, world!')"

    def set_position((x, y), value):
        pass

    try:
        pass
    except Exception, err:
        pass

    raise RuntimeError, "I feel like crashing today :p"

    `wow_these_really_did_exist`

    10L

* Add octal support, more test cases, and fixup long ints

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| primer: Hypothesis now requires Python>=3.8 (GH-2602)

looks like their project dev tooling uses some newer syntax or something | 1 |
| Add a missing space in Python 2 deprecation (GH-2590)

`DEPRECATION: Python 2 support will be removed in the first stable releaseexpected in January 2022` - > `DEPRECATION: Python 2 support will be removed in the first stable release expected in January 2022` | 1 |
| Update CHANGES.md for 21.10b0 release (#2583)

* Update CHANGES.md for 21.10b0 release

* Update version in docs/usage_and_configuration/the_basics.md

* Also update docs/integrations/source_version_control.md ... | 1 |
| install build-essential to compile dependencies and use multi-stage build  (#2582)

- Install build-essential to avoid build issues like #2568 when dependencies don't have prebuilt wheels available
- Use multi-stage build instead of trying to purge packages and cache from the image
  Copying `/root/.local/` installs only black's built Python dependencies (< 20 MB).
  So the image is barely larger than python:3-slim base image | 1 |
| Deprecate Python 2 formatting support (#2523)

* Prepare for Python 2 depreciation

- Use BlackRunner and .stdout in command line test

So the next commit won't break this test. This is in its own commit so
we can just revert the depreciation commit when dropping Python 2
support completely.

* Deprecate Python 2 formatting support | 1 |
| Pin regex in docker to 2021.10.8 (GH-2579)

* Pin regex in docker to 2021.10.8
- This is due to 2021.10.8 having arm wheels and newer versions not

I will go see if I can help restore arm build @ https://bitbucket.org/mrabarnett/mrab-regex/issues/399/missing-wheel-for-macosx-and-the-new-m1 soon.

Test: Build on my M1 mac: `docker build -t cooperlees/black .`

* Add in that the pin is only for docker | 1 |
| Address mypy errors on 3.10 w/ asyncio loop parameter (#2580) | 1 |
| Update bug template (#2538) | 1 |
| Use STDIN project in test_projects to ensure it runs quickly (#2575)

Existing test was actually running a full black-primer
run which could be slow. This goes from 8 seconds to
0.4 seconds on my machine.

Needed to move to top level scope to leverage the caplog
feature of pytest in order to test that the command line
was parsing the bogus arguments and dumping to stderr. | 1 |
| Add Tesla to organizations list (#2577) | 1 |
| fix: allow tests to be run from (hopefully) any directory (GH-2574)

* fix: allow tests to be run from the tests/ directory
* fix: try fixing windows build with MarcoGorelli's suggestion
* Windows hotfix + better respect test's spirit

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| black-primer: Print summary after individual failures (#2570)

If the individual failures are verbose, it's useful to have
the summary at the end. Otherwise, it can be really difficult
to figure out which projects have an issue. | 1 |
| Add --projects cli flag to black-primer (#2555)

* Add --projects cli flag to black-primer

Makes it possible to run a subset of projects on black primer

* Refactor into click callback | 1 |
| Print out line diff on test failure (#2552)

It currently prints both ASTs - this also
adds the line diff, making it much easier to visualize
the changes as well. Not too verbose since it's only a diff. | 1 |
| Refactor Jupyter magic handling (#2545) | 1 |
| Remove some unneeded exceptions from mypy.ini (#2557) | 1 |
| Disallow any Generics on mypy except in black_primer (#2556)

Only black_primer needs the disallowal - means we'll
get better typing everywhere else. | 1 |
| Define a stability policy (#2529)

Fixes #2394. Eventually fixes #517.

This is essentially @pradyunsg's suggestion from #2394. I suggest that at the
same time we start the formal stability policy, we take a few other disruptive steps
and drop Python 2 and the "b" marker.

Co-authored-by: Pradyun Gedam <pradyunsg@gmail.com>
Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| bump sphinx so it works on Python3.10 (#2546) | 1 |
| Fix feature detection for positional-only arguments in lambdas (#2532) | 1 |
| chore(ci): use official Python 3.10 (#2521)

Python 3.10 (final) was released yesterday and is now available on GHA! | 1 |
| Bump typed-ast minimum to 1.4.3 for 3.10 compat (#2519) | 1 |
| MNT: remove unnecessary test deps + some refactoring (GH-2510)

The main goals of this commit include:

* improving consistency on how strict the test suite is -- Jelle has
  seen cases where a test did not fail to an incomplete test setup
  even though it should've
* simplifying tests for both ease of creation and reading via
  parametrization and helpers
* reorganizing the test suite by grouping more tests
* dropping test suite dependencies that aren't strictly necessary

The test suite could definitely do with more refactoring, but this is a
good first pass. Anyway it would've gotten too big to review effectively
if I did continue on this PR.

Commit history before squash merge:

* Drop parameterized dep and refactor format tests

Since the test suite is already using pytest-only features we can drop
the parameterized test dependency in favour of pytest's own offering.

I also added an utility function called assert_format that makes it
even easier to verify Black formats some code correctly. We already
have great tooling if the case is very simple in test_format.py but
any sort of complication makes it hard to use. Also if you're writing
a non-standard test case, you have to be careful to include all of
the steps so issues don't go undetected. assert_format aims to
1) improve consistency, 2) avoid wasted CPU cycles, and 3) avoid
logical errors that hide issues.

Finally, quite a few tests were either moved and/or simplified with
the new setup.

* Move file collection tests
* Add assert_collected_sources helper function

Testing source collection involves a lot of repetitive boilerplate,
something that black.files.get_sources's signature does not help with.
So to cut down on boilerplate like `report=black.Report()` I added
a convenience function to tests/test_black.py which wraps
black.get_sources. Its signature is designed to be much more lax to
make it much easier to use. Somehow this leads to cutting 100 lines!

Also IMO the test cases are much easier to read since it's more
declarative than really procedural now.

* Run isort on some test files
* Move cache tests
* Use pytest-style asserts & add parametrization
* Drop now unnecessary test dependencies

*pytest-cases might be interesting for further refactoring but I
haven't been able to wrap my head around it for the time being. We
can always revisit anyway. | 1 |
| Add --workers CLI parameter (fixes #2513) (#2514)

Fixes #2513 | 1 |
| Allow to pass the FileMode options in the vim plugin (#1319) | 1 |
| Fix python_version markers in Pipfile.lock (#2511)

This took way too much effort but in the end I was able to achieve a
(mostly) functional Pipfile.lock ranging from 3.6 to 3.9 🎉 | 1 |
| Add test to cover when unable to replace magics (#2471)

Another follow-up from #2357, adding a test for uncovered code. | 1 |
| Bump required aiohttp version to 3.7.4 (#2509)

Commit history before merge:

* Bump required aiohttp version to 3.7.4

This release includes an important security fix
(https://github.com/aio-libs/aiohttp/security/advisories/GHSA-v6wp-4m6f-gcjg) and many
other improvements.

* add changelog entry
* Let's not forget about Pipfile

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| re-implement simple CORS middleware for blackd (#2500)

* re-implement simple CORS middleware for blackd
* remove aiohttp-cors from setup.py
* Remove aiohttp-cors from Pipfile.lock

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Add Kedro to project list and QuantumBlack to orgs (#2502) | 1 |
| DOC: cleanup pre-commit instructions following #2430 (#2481) | 1 |
| add check for version in the-basics example (#2459) | 1 |
| fix all b904s (#2501) | 1 |
| Update CHANGES.md for 21.9b0 release (#2494) | 1 |
| Fix missing toml extra w/ setuptools-scm (GH-2475)

Project packaging is using TOML due to pyproject.toml but fails to
mention it, causing installation failures with newer setuptools-scm 6.3.0.

Commit history before merge:

* Fix missing toml extra

Fixed breakage uncovered by setuptools-scm 6.3.0 where installation
would fail for project that missed to mention the toml extra.

* Bump setuptools[-scm] to avoid toml extra

https://github.com/psf/black/pull/2475#issuecomment-912730714

> If you constraint greater than 6.3.0 and setuptools greater than 45
> you can skip the extra,

* Actually for safety reasons, just use the extra

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Remove `blackcellmagic` reference (#2477)

This package seems to be unmaintained (last commit is from > 2 years ago), and `black` now runs on Jupyter Notebooks directly | 1 |
| Add hidden import to PyInstaller build (GH-2466)

Add new platformdirs dependencies as hidden imports when creating
PyInstaller-based binaries.

platformdirs imports the module for each platform dynamically, which
PyInstaller is unable to correctly detect for packing. By adding the
modules as hidden imports, we are telling PyInstaller to include the
modules in the packaged binary.

This issue seems to have been introduce when switching to platformdirs
in #2375. fixes #2464

Commit history before merge:

* Add hidden import to PyInstaller build

Add new platformdirs dependency as a hidden import when creating
PyInstaller based binaries.

* Only include the platformdirs for the relevant os | 1 |
| Change docker workflow for latest_release tag (#2468) | 1 |
| fix: run pypi / docker upload from published draft releases (#2461)

Draft releases don't trigger the workflows (that's good!) but since they only 

Commit history before merge:

* fix: run pypi upload from published draft releases
* Fix broken task list markup in PR template
* change docker workflow to build on release publish

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Exclude broken typing-extensions version + fix import (#2460)

re. import, the ipynb code was assuming that typing-extensions would
always be available, but that's not the case! There's an environment
marker on the requirement meaning it won't get installed on 3.10 or
higher. The test suite didn't catch this issue since aiohttp pulls in
typing-extensions unconditionally. | 1 |
| Prepare CHANGES.md for release 21.8b0 (#2458)

Hopefully my first release doesn't end up in flames 🔥

Commit history before merge:

* Prepare CHANGES.md for release 21.8b0
* I need to add a check for this too. | 1 |
| Pin setuptools-scm build time dependency (#2457)

The setuptools-scm dependency in setup.cfg did not have a version
specified, leading to the issues described in #2449 after a faulty release
of setuptools-scm was published. To avoid this issue in the future, the
last version before that faulty update is now pinned.

Commit history before merge:

* Pin setuptools-scm dependency version (#2449)
* Update CHANGES.md
* Let's pin in pyproject.toml too

Mostly since it's non-build-backend specific configuration and more
widely standardized file. Not sure what benefits pinning in setup.cfg
gives us on top of pyproject.toml but I'd rather not find out during
the release that is supposed to happen today :wink:

Co-authored-by: FiNs <24248249+FabianNiehaus@users.noreply.github.com> | 1 |
| add test which covers stdin filename ipynb (#2454) | 1 |
| set mypy_path in mypy.ini (#2455) | 1 |
| Document jupyter hook (#2416)

This also introduces a script so we can reference the latest version in
the example pre-commit configuration in the docs without forgetting to
update it when doing a release!

Commit history before merge:

* document jupyter hook
* note minimum version
* add check for pre-commit version
* use git tag
* curl api during ci
* parse version from changes file
* fixup script
* rename variables
* Tweak the docs & magical script
* fix couple of typos
* pin additional dependencies in hook
* Add types-PyYAML to lockfile

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| blib2to3: support unparenthesized wulruses in more places (#2447)


Implementation stolen from PR davidhalter/parso#162. Thanks parso!

I could add support for these newer syntactical constructs in the
target version detection logic, but until I get diff-shades up
and running I don't feel very comfortable adding the code. | 1 |
| Stop changing return type annotations to tuples (#2384)


This fixes a bug where a trailing comma would be added to a
parenthesized return annotation changing its type to a tuple.
Here's one case where this bug shows up:

```
def spam() -> (
    this_is_a_long_type_annotation_which_should_NOT_get_a_trailing_comma
):
    pass
```

The root problem was that the type annotation was treated as if it was
a parameter & import list (is_body=True to linegen::bracket_split_build_line)
where a trailing comma is usually fine. Now there's another check in the
aforementioned function to make sure the body it's operating on isn't
a return annotation before truly adding a trailing comma. | 1 |
| MNT: add pull request template (#2443)

So we don't have to request changes on these basic requirements as
often - hopefully :) | 1 |
| Add cpython Lib/ repository config into primer config - Disabled (#2429)

* Add CPython repository into primer runs

- CPython tests is probably the best repo for black to test on as the stdlib's unittests should use all syntax
  - Limit to running in recent versions of the python runtime - e.g. today >= 3.9
    - This allows us to parse more syntax
- Exclude all failing files for now
  - Definitely have bugs to explore there - Refer to #2407 for more details there
  - Some test files on purpose have syntax errors, so we will never be able to parse them
- Add new black command arguments logging in debug mode; very handy for seeing how CLI arguments are formatted

CPython now succeeds ignoring 16 files:
```
Oh no! 💥 💔 💥
1859 files would be reformatted, 148 files would be left unchanged.
```

Testing
- Ran locally with and without string processing - Very little runtime difference BUT 3 more failed files
```
time /tmp/tb/bin/black --experimental-string-processing --check . 2>&1 | tee /tmp/black_cpython_esp
...
Oh no! 💥 💔 💥
1859 files would be reformatted, 148 files would be left unchanged, 16 files would fail to reformat.

real	4m8.563s
user	16m21.735s
sys	0m6.000s
```
- Add unittest for new covienence config file flattening that allows long arguments to be broke up into an array/list of strings

Addresses #2407

---

Commit history before merge:

* Add new `timeout_seconds` support into primer.json
- If present, will set forked process limit to that value in seconds
- Otherwise, stay with default 10 minutes (600 seconds)

* Add new "base_path" concept to black-primer
- Rather than start at the repo root start at a configured path within the repository
  - e.g. for cpython only run black on `Lib`

* Disable by default - It's too much for GitHub Actions. But let's leave config for others to use
* Minor tweak to _flatten_cli_args

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Change sys.exit to raise ImportError (#2440)

The fix for #1688 in #1761 breaks help("modules") introspection and also leads
to unhappy results when inadvertently importing blackd from Python. Basically
the sys.exit(-1) causes the whole Python REPL to exit -- not great to suffice.

Commit history before merge:

* Change sys.exit to Raise.
* Add #2440 to changelog.
* Fix lint error from prettier
* Remove exception chain for more helpful user message.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Add test requirements to Pipfile[.lock] & bump deps (#2436)

While this development environment / requirements situation is a mess,
let's at least make it consistent. We're effectively supporting two
modes of development in this project, 1) tox based dev commands
(e.g. `tox -e fuzz`) that are dead simple to use, and 2) manual dev
commands (e.g. `pytest -n auto`) that give more control and are usually
faster.

Right now the Pipfile.lock based development environment is incomplete
missing the test requirements specified in ./test_requirements.txt.
This is annoying since manual test commands (e.g. `pytest -k fmtonoff`)
fail. Let's fix this by making Pipfile.lock basically a
"everything you need" requirements file (fuzzing not included since
running it locally is not something common).

Oh and let's bump some documentation deps (and bring some requirements
across .pre-commit-config.yaml, Pipfile, and docs/requirement.txt in
alignment again). Don't worry, I tested these changes so they should
be fine (hopefully!). | 1 |
| Improve f-string expression detection regex so ... (#2437)

we don't accidentally add backslashes to them when normalizing quotes
because that's invalid syntax!

The problem this commit fixes is that matches would eat too much
blocking important matches to occur. For example, here's one f-string
body:

    {a}{b}{c}

I know there's no risk of introducing backslashes here, but the regex
already goes sideways with this. Throwing this example at regex101
I get:

    {a}{b}{c}   # The As and Bs are the two matches, and the upper
    ---- ----   # case letters are the groups with those matches.
    aAaa bbBb

... we've missed the middle expression (so if any backslashes in a
more complex example were introduced there we wouldn't bail out
even though we should -- hence the bug). As it stands the regex
needs somesort of extra character (or the start/end of the body)
around the expressions but that isn't always the case as shown
above.

The fix implemented here is to turn the "eat a surrounding non-curly
bracket character" groups ie. `(?:[^{]|^)` and `(?:[^}]|$)` into
negative lookaheads and lookbehinds. This still guarantees the
already specified rules but without problematically eating extra
characters ^^ | 1 |
| Present a more user-friendly error if .gitignore is invalid (#2414)

Fixes #2359.

This commit now makes Black exit with an user-friendly error message if a
.gitignore file couldn't be parsed -- a massive improvement over an opaque
traceback! | 1 |
| Remove `language_version` for pre-commit (#2430)

* Remove `language_version` for pre-commit

At my company, we set the Python version in `default_language_version`
in each repo's `.pre-commit-config.yaml`,
so that all hooks are running with the same Python version.

However, this currently doesn't work for black,
as the `language_version` specified here
in the upstream `.pre-commit-hooks.yaml` takes precedence.
Currently, this requires us to manually set `language_version`
specifically for black,
duplicating the value from `default_language_version`.
The failure mode otherwise is subtle -
black works most of the time,
but try to add a walrus operator and it suddenly breaks!

Given that black's `setup.py` already has `python_requires>=3.6.2`,
specifying that `python3` must be used here isn't needed
as folks inadvertently using Python 2 will get hook-install-time failures anyways.
Remove the `language_version` from these upstream hook configs
so that users of black are able to use `default_language_version`
and have it apply to all their hooks, black included.

Example `.pre-commit-config.yaml` before:
```
default_language_version:
  python: python3.8
repos:
-   repo: https://github.com/psf/black
    rev: 21.7b0
    hooks:
    -   id: black
        language_version: python3.8
```

After:
```
default_language_version:
  python: python3.8
repos:
-   repo: https://github.com/psf/black
    rev: 21.7b0
    hooks:
    -   id: black
```

* Add changelog entry | 1 |
| Add jupyter deps to Pipfile.lock (#2419) | 1 |
| Update language server links (#2425)

python-language-server is no longer maintained. | 1 |
| fix: remove unneccessary escape character (#2423) | 1 |
| Jupyter notebook support (#2357)

To summarise, based on what was discussed in that issue:

due to not being able to parse automagics (e.g. pip install black)
without a running IPython kernel, cells with syntax which is parseable
by neither ast.parse nor IPython will be skipped cells with multiline
magics will be skipped trailing semicolons will be preserved, as they
are often put there intentionally in Jupyter Notebooks to suppress
unnecessary output

Commit history before merge (excluding merge commits):

* wip
* fixup tests
* skip tests if no IPython
* install test requirements in ipynb tests
* if --ipynb format all as ipynb
* wip
* add some whole-notebook tests
* docstrings
* skip multiline magics
* add test for nested cell magic
* remove ipynb_test.yml, put ipynb tests in tox.ini
* add changelog entry
* typo
* make token same length as magic it replaces
* only include .ipynb by default if jupyter dependencies are found
* remove logic from const
* fixup
* fixup
* re.compile
* noop
* clear up
* new_src -> dst
* early exit for non-python notebooks
* add non-python test notebook
* add repo with many notebooks to black-primer
* install extra dependencies for black-primer
* fix planetary computer examples url
* dont run on ipynb files by default
* add scikit-lego (Expected to change) to black-primer
* add ipynb-specific diff
* fixup
* run on all (including ipynb) by default
* remove --include .ipynb from scikit-lego black-primer
* use tokenize so as to mirror the exact logic in IPython.core.displayhooks quiet
* fixup
* :art:
* clarify docstring
* add test for when comment is after trailing semicolon
* enumerate(reversed) instead of [::-1]
* clarify docstrings
* wip
* use jupyter and no_jupyter marks
* use THIS_DIR
* windows fixup
* perform safe check cell-by-cell for ipynb
* only perform safe check in ipynb if not fast
* remove redundant Optional
* :art:
* use typeguard
* dont process cell containing transformed magic
* require typing extensions before 3.10 so as to have TypeGuard
* use dataclasses
* mention black[jupyter] in docs as well as in README
* add faq
* add message to assertion error
* add test for indented quieted cell
* use tokenize_rt else we cant roundtrip
* fmake fronzet set for tokens to ignore when looking for trailing semicolon
* remove planetary code examples as recent commits result in changes
* use dataclasses which inherit from ast.NodeVisitor
* bump typing-extensions so that TypeGuard is available
* bump typing-extensions in Pipfile
* add test with notebook with empty metadata
* pipenv lock
* deprivative validate_cell
* Update README.md
* Update docs/getting_started.md
* dont cache notebooks if jupyter dependencies arent found
* dont write to cache if jupyter deps are not installed
* add notebook which cant be parsed
* use clirunner
* remove other subprocess calls
* add docstring
* make verbose and quiet keyword only
* :art:
* run second many test on directory, not on file
* test for warning message when running on directory
* early return from non-python cell magics
* move NothingChanged to report to avoid circular import
* remove circular import
* reinstate --ipynb flag

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix type dependencies of mypy invocation (#2411)

Commit history before merge:

* Fix type dependencies of mypy invocation
* Consistent version upper bound | 1 |
| Test on Python 3.10-dev (#2406) | 1 |
| Fix issue templates + add docs template (#2399)

The template weren't applying the default labels ever since I renamed
the labels.

There has been enough issues about documentation opened recently so it's
probably worth a template for it. | 1 |
| Add ESP to sqlalchemy for black-primer (#2400)

The crash has been fixed for a little while now. Tentatively assuming
that this will lead to changes. | 1 |
| Clarify contributing docs (#2398)

"as configurable as gofmt" means little to people who haven't used gofmt. | 1 |
| isort docs have changed urls (#2390) | 1 |
| add context manager to temporarily change the cwd (#2377)

Commit history before merge:

* add context manager to temporarily change the cwd
* Iterator, not Iterable | 1 |
| Use platformdirs over appdirs (#2375)

Signed-off-by: Bernát Gábor <bgabor8@bloomberg.net>
Signed-off-by: Bernát Gábor <gaborjbernat@gmail.com> | 1 |
| Update CHANGES.md for 21.7b0 release (#2376)

* Update CHANGES.md for 21.7b0 release

* move some changes to the right section

* another one

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Create Docker tag 'latest_release' (#2374)

Docker images created during release process will have extra tag 'latest_release'.

This closes #2373. | 1 |
| Don't include profiling/ to cut down sdist by ~2x (#2362)

They seem to be used as test cases for a specific region of formatting
that was slow. Now performance testing is probably something end users
won't be needing to do, so this is an easy way of reducing the sdist
size sigificantly. | 1 |
| Improve AST safety parsing error message (#2304)

Co-authored-by: Hasan Ramezani <hasan.r67@gmail.com> | 1 |
| Switch `toml` TOML library for `tomli` (#2301)

toml unfortunately has a lack of maintainership issue right now. It's
evident by the fact toml only supports TOML v0.5.0. TOML v1.0.0 has
been recently released and right now Black crashes hard on its usage.

tomli is a brand new parse only TOML library. It supports TOML
v1.0.0. Although TBH we're switching to this one mostly because
pip is doing the same.

*The upper bound was included at the library maintainer's request.

Co-authored-by: Łukasz Langa <lukasz@langa.pl>
Co-authored-by: Taneli Hukkinen <3275109+hukkin@users.noreply.github.com> | 1 |
| Add LocalStack and Twisted to projects using Black | 1 |
| Second run of tox -e py results in a test error for test marked with no_python2 (#2369)

Fixes #2367 | 1 |
| Use setuptools.find_packages in setup (#2363)

* Use setuptools.find_packages in setup

* Address mypy errors | 1 |
| Avoid src being marked as optional in help (#2356) | 1 |
| fix typo (#2358) | 1 |
| Accept empty stdin (close #2337) (#2346)

Commit history before merge:

* Accept empty stdin (close #2337)
* Update tests/test_black.py
* Add changelog
* Assert Black reformats an empty string to an empty string (#2337) (#2346)
* fix | 1 |
| Get `click` types from main repo (#2344)

Click types have been moved to click repo itself. See pallets/click#1856

I've had some issues with typeshed types being outdated in another project
so might be good to avoid that here.

Commit history before merge:

* Get `click` types from main repo
* Fix mypy errors
* Require click v8 for type annotations
* Update Pipfile | 1 |
| Update pre-commit config (#2331)

via `pre-commit autoupdate`

```
Updating https://gitlab.com/pycqa/flake8
... updating 3.9.0 -> 3.9.2.
Updating https://github.com/pre-commit/mirrors-mypy
... updating v0.812 -> v0.902.
Updating https://github.com/pre-commit/mirrors-prettier
... updating v2.2.1 -> v2.3.1.
```

Signed-off-by: SADIK KUZU <sadikkuzu@hotmail.com>

* Add necessary typeshed packages to requirements

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Add Duolingo to list of users (#2341) | 1 |
| Chat on Discord instead of Freenode (#2336)

Now that we've moved, let's direct our users to Discord in the
documentation and readme. | 1 |
| Docs: no space is inserted to empty docstrings (#2249) (#2333) | 1 |
| Add EOF and trailing whitespace fixer to pre-commit config (#2330) | 1 |
| Fix internal error when FORCE_OPTIONAL_PARENTHESES feature is enabled (#2332)

Fixes #2313. | 1 |
| Vim plugin fix string normalization option (#1869)

This commit fixes parsing of the skip-string-normalization option in vim
plugin. Originally, the plugin read the string-normalization option,
which does not exist in help (--help) and it's not respected by black
on command line.

Commit history before merge:

* fix string normalization option in vim plugin
* fix string normalization option in vim plugin
* Finish and fix patch (thanks Matt Wozniski!)

FYI: this is totally the work and the comments below of Matt (AKA godlygeek)

This fixes two entirely different problems related to how pyproject.toml
files are handled by the vim plugin.

=== Problem #1 ===

The plugin fails to properly read boolean values from pyproject.toml.
For instance, if you create this pyproject.toml:

```
[tool.black]
quiet = true
```

the Black CLI is happy with it and runs without any messages, but the
:Black command provided by this plugin fails with:

```
Traceback (most recent call last):
  File "<string>", line 1, in <module>
  File "<string>", line 102, in Black
  File "<string>", line 150, in get_configs
  File "<string>", line 150, in <dictcomp>
  File "/usr/lib/python3.6/distutils/util.py", line 311, in strtobool
    val = val.lower()
AttributeError: 'bool' object has no attribute 'lower'
```

That's because the value returned by the toml.load() is already a
bool, but the vim plugin incorrectly tries to convert it from a str to a bool.

The value returned by toml_config.get() was always being passed to
flag.cast(), which is a function that either converts a string to an
int or a string to a bool, depending on the flag. vim.eval()
returns integers and strings all as str, which is why we need the cast,
but that's the wrong thing to do for values that came from toml.load().
We should be applying the cast only to the return from vim.eval()
(since we know it always gives us a string), rather than casting the
value that toml.load() found - which is already the right type.

=== Problem #2 ===

The vim plugin fails to take the value for skip_string_normalization
from pyproject.toml. That's because it looks for a string_normalization
key instead of a skip_string_normalization key, thanks to this line
saying the name of the flag is string_normalization:

black/autoload/black.vim (line 25 in 05b54b8)
```
 Flag(name="string_normalization", cast=strtobool),
```

and this dictcomp looking up each flag's name in the config dict:

black/autoload/black.vim (lines 148 to 151 in 05b54b8)
```
 return {
   flag.var_name: flag.cast(toml_config.get(flag.name, vim.eval(flag.vim_rc_name)))
   for flag in FLAGS
 }
```

For the second issue, I think I'd do a slightly different patch. I'd
keep the change to invert this flag's meaning and change its name that
this PR proposes, but I'd also change the handling of the
g:black_skip_string_normalization and g:black_string_normalization
variables to make it clear that g:black_skip_string_normalization is
the expected name, and g:black_string_normalization is only checked
when the expected name is unset, for backwards compatibility.

My proposed behavior is to check if g:black_skip_string_normalization
is defined and to define it if not, using the inverse of
g:black_string_normalization if that is set, and otherwise to the
default of 0. The Python code in autoload/black.vim runs later, and
will use the value of g:black_skip_string_normalization (and ignore
g:black_string_normalization; it will only be used to set
g:black_skip_string_normalization if it wasn't already set).

---

Co-authored-by: Matt Wozniski <mwozniski@bloomberg.net>

* Fix plugin/black.vim (need to up my vim game)

Co-authored-by: Matt Wozniski <godlygeek@gmail.com>

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Matt Wozniski <mwozniski@bloomberg.net>
Co-authored-by: Matt Wozniski <godlygeek@gmail.com> | 1 |
| Find pyproject from vim relative to current file (#1871)

Commit history before merge:

* Find pyproject from vim relative to current file
* Merge remote-tracking branch 'upstream/main' into find-pyproject-vim
* Finish and fix this patch (thanks Matt Wozniski!)

Both the existing code and the proposed code are broken.
The vim.eval() call (whether it's vim.eval("@%") or
vim.eval("fnamemodify(getcwd(), ':t')) returns a string, and it passes
that string to find_pyproject_toml, which expects a sequence of strings,
not a single string, and - since a string is a sequence of single
character strings - it gets turned into a list of ridiculous paths. I
tested with a file called foo.py, and added a print(path_srcs) into
find_project_root, which printed out:

[
  PosixPath('/home/matt/f'),
  PosixPath('/home/matt/o'),
  PosixPath('/home/matt/o'),
  PosixPath('/home/matt'),
  PosixPath('/home/matt/p'),
  PosixPath('/home/matt/y')
]

This does work for an unnamed buffer, too - we wind up calling
black.find_pyproject_toml(("",)), and that winds up prepending the
working directory to any relative paths, so "" just gets turned into
the current working directory.

Note that find_pyproject_toml needs to be passed a 1-tuple, not a
list, because it requires something hashable (thanks to
functools.lru_cache being used)

Co-authored-by: Matt Wozniski <mwozniski@bloomberg.net>

* I forgot the CHANGELOG entry ... again
* I'm really bad at dealing with merge conflicts sometimes
* Be more correct describing search behaviour

Co-authored-by: Austin Glaser <austin.glaser@spacex.com>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Matt Wozniski <mwozniski@bloomberg.net> | 1 |
| Add STDIN test to primer (#2315)

* Add STDIN test to primer

- Check that out STDIN black support stays working
- Add asyncio.subprocess STDIN pip via communicate
- We just check we format python code from primer's `lib.py`

Fixes #2310 | 1 |
| Fix incorrect document referance (#2326) | 1 |
| Update CHANGES.md for 21.6b0 release (#2325) | 1 |
| Add coverage files to gitignore (#2323) | 1 |
| Don't run Docker workflow on push to forks (#2324) | 1 |
| Support named escapes (`\N{...}`) in string processing (#2319)

Co-authored-by: Felix Hildén <felix.hilden@gmail.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix flake8 configuration by switching from extend-ignore to ignore (#2320) | 1 |
| Regression fix: leave R prefixes capitalization alone (#2285)

`black.strings.get_string_prefix` used to lowercase the extracted
prefix before returning it. This is wrong because 1) it ignores the
fact we should leave R prefixes alone because of MagicPython, and 2)
there is dedicated prefix casing handling code that fixes issue 1.
`.lower` is too naive.

This was originally fixed in 20.8b0, but was reintroduced since 21.4b0.

I also added proper prefix normalization for docstrings by using the
`black.strings.normalize_string_prefix` helper.

Some more test strings were added to make sure strings with capitalized
prefixes aren't treated differently (actually happened with my original
patch, Jelle had to point it out to me). | 1 |
| Mention comment non-processing in documentation (#2306)

This commit adds a short section discussing the non-processing of docstrings
besides spacing improvements, mentions comment moving and links to the
AST equivalence discussion. I also added a simple spacing test for good
measure.

Commit history before merge:

* Mention comment non-processing in documentation, add spacing test
* Mention special cases for comment spacing
* Add all special cases, improve wording | 1 |
| Possible fix for issue with indentation and fmt: skip (#2281)

Not sure the fix is right.  Here is what I found: issue is connected
with line

    first.prefix = prefix[comment.consumed :]

in `comments.py`.  `first.prefix` is a prefix of the line, that ends
with `# fmt: skip`, but `comment.consumed` is the length of the
`"  # fmt: skip"` string.  If prefix length is greater than 14,
`first.prefix` will grow every time we apply formatting.

Fixes #2254 | 1 |
| [primer] Enable everything (#2288)

See if we pass all our repos with experimental string processing enabled.
Django probably needed:
- Ignores >= 3.8 only

We could support PEP440 version specifiers, but that would introduce the packaging module as a dependency that I'd like to avoid ... Or I could implement a poor persons version or vendor

Commit history before merge:
 * [primer] Enable everything
 * Add exclude extend to django CLI args for primer
 * Change default timeout to from 5 to 10 mins for a primer project
 * Skip string normalization for Django
 * Limit Django to >= 3.8 due to := operator | 1 |
| Fix incorrect custom breakpoint indices when string group contains fake f-strings (#2311)

Fixes #2293 | 1 |
| Account for += assignment when deciding whether to split string (#2312)

Fixes #2294 | 1 |
| Go back to single core for test suite on CI (#2305)

The random asyncio bug is just too frequent and annoying to be
worth the speed improvements. Our test suite is already quite fast.
Random test failures hurt for 3 reasons, 1) they are discouraging for
new contributors who won't understand it's out of their control, 2)
it's annoying and time consuming to rerun the workflow, and 3) it
makes single job failures feel less important (even they should be
treated as important!). | 1 |
| Add option to require a specific version to be running (#2300)

Closes #1246: This PR adds a new option (and automatically a toml entry, hooray for existing configuration management 🎉) to require a specific version of Black to be running.

For example: `black --required-version 20.8b -c "format = 'this'"`

Execution fails straight away if it doesn't match `__version__`. | 1 |
| don't uvloop.install on import (#2303) | 1 |
| remove unnecessary docs changelog | 1 |
| Move `--code` #2259 change log to correct unlreased section of CHANGES.md | 1 |
| Code Flag Options (#2259)

Properly handles the diff, color, and fast option when black is run with
 the `--code` option.

Closes #2104, closes #1801. | 1 |
| Bump urllib3 from 1.26.4 to 1.26.5 (#2298)

Bumps [urllib3](https://github.com/urllib3/urllib3) from 1.26.4 to 1.26.5.
- [Release notes](https://github.com/urllib3/urllib3/releases)
- [Changelog](https://github.com/urllib3/urllib3/blob/main/CHANGES.rst)
- [Commits](https://github.com/urllib3/urllib3/compare/1.26.4...1.26.5)

---
updated-dependencies:
- dependency-name: urllib3
  dependency-type: indirect
...

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Add `version` to github action (and rewrite the whole thing while at it) (#1940)

Commit history before merge:

* Add black_version to github action
* Merge upstream/main into this branch
* Add version support for the Black action pt.2

  Since we're moving to a composite based action, quite a few changes
  were made. 1) Support was added for all OSes (Windows was painful). 
  2) Isolation from the rest of the workflow had to be done manually
  with a virtual environment.

  Other noteworthy changes:

  - Rewrote basically all of the logic and put it in a Python script
    for easy testing (not doing it here tho cause I'm lazy and I can't
    think of a reasonable way of testing it).
  - Renamed `black_version` to `version` to better fit the existing
    input naming scheme.
  - Added support for log groups, this makes our action's output a
    bit more fancy (I may or may have not added some debug output too).

* Add more to and sorta rewrite the Action's docs

  Reflect compatability and gotchas.

* Add CHANGELOG entry
* Merge main into this branch
* Remove debug; address typos; clean up action.yml

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Correct max string length calculation when there are string operators (#2292)

PR #2286 did not fix the edge-cases (e.g. when the string is just long
enough to cause a line to be 89 characters long). This PR corrects that
mistake. | 1 |
| Update CHANGES.md for 21.5b2 release (#2290)

* Update CHANGES.md for 21.5b2 release | 1 |
| Fix regular expression that black uses to identify f-expressions (#2287)

Fixes #1469 | 1 |
| Make sure to split lines that start with a string operator (#2286)

Fixes #2284 | 1 |
| Fix --experiemental-string-processing crash when matching parens not found (#2283)

Fixes #2271 | 1 |
| add discussion of magic comments to FAQ (#2272)

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| ptr nolong requires changes (#2276)

- I worked on this project yesterday and must have fixed the formatting | 1 |
| Add @zzzeek testimonial to README and docs | 1 |
| Fix path_empty() (#2275)

Behavior other than output shouldn't depend on the verbose/quiet option. As far as I can tell this currently has no visible effect, since code after this function is called handles an empty list gracefully. | 1 |
| Add --experimental-string-processing to future changes (#2273)

* add esp to future style

* changelog

* fix label | 1 |
| Issue templates: use HTML comments (#2269)

This commit makes use of HTML comments inside GitHub issue templates
to make sure that even if they aren't removed by the issue author they won't be shown
in the rendered output.

The goal is to simply make the issues less noisy by removing template messages. | 1 |
| Use latest Python in uploading binaries (#2260)

* Use latest Python in uploading binaries

* Don't pin version at all

* Add changelog entry | 1 |
| Fix and test docs on Windows (#2262)

There's some weird interaction between Click and
sphinxcontrib-programoutput on Windows that leads to an encoding error
during the printing of black-primer's help text.

Also symlinks aren't well supported on Windows so let's just use
includes which actually work because we now use MyST :D | 1 |
| Add optional uvloop import (#2258)

* Add optional uvloop import

- If we find `uvloop` in the env for black, blackd or black-primer lets try and use it
- Add a uvloop extra install

Fixes #2257

Test:
- Add ci job to install black[uvloop] and run a primer run with uvloop
  - Only with latest python (3.9)
  - Will be handy to compare runtimes as a very unoffical benchmark

* Remove tox install

* Add to CHANGES/news | 1 |
| Removed adding a space into empty docstrings. (#2249)

Resolves #2168 by disabling the insertion of a " " when the docstring is entirely empty.

Note that this PR is focussed only on the case of empty docstrings. In particular this does not make any changes to the behaviour that a " " is inserted if a non-empty docstring begins with the quoting character. That is, black still prefers:

    """ "something" """

to:

    """"something" """

and that:

    """"Something""""

is not a legal docstring. | 1 |
| Create FAQ documentation (GH-2247)

This commit creates a Frequently Asked Questions document for our users
to read. Hopefully they actually read it too. Items included are:
Black's non-API, AST safety, style stability, file discovery, Flake8
disagreements and Python 2 support. Hopefully I've got the answers
down in general.

Commit history before merge:

* Create FAQ
* Address feedback
* Move to single markdown file
* Minor wording improvements
* Add changelog entry | 1 |
| Solved Problem with Non-ASCII .gitignore Files (#2229)

* Solved Problem with non-alphabetical .gitignore files

When .gitignore file in the user's project directory contained non-alphabetical
characters(Japanese, Korean, Chinese, etc), Nothing works and printed this
weird message in the console('cp949' is the encoding for Korean characters
in this case). It even blocks VSCode's formatting from working. This commit
solves the problem.

Traceback (most recent call last):
  File "c:\users\username\anaconda3\envs\project-name\lib\runpy.py", line 193, in _run_module_as_main
    "__main__", mod_spec)
  File "c:\users\username\anaconda3\envs\project-name\lib\runpy.py", line 85, in _run_code
    exec(code, run_globals)
  File "C:\Users\username\anaconda3\envs\project-name\Scripts\black.exe\__main__.py", line 7, in <module>
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\black\__init__.py", line 1056, in patched_main      
    main()
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\click\core.py", line 829, in __call__
    return self.main(*args, **kwargs)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\click\core.py", line 782, in main
    rv = self.invoke(ctx)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\click\core.py", line 1066, in invoke
    return ctx.invoke(self.callback, **ctx.params)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\click\core.py", line 610, in invoke
    return callback(*args, **kwargs)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\click\decorators.py", line 21, in new_func
    return f(get_current_context(), *args, **kwargs)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\black\__init__.py", line 394, in main
    stdin_filename=stdin_filename,
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\black\__init__.py", line 445, in get_sources        
    gitignore = get_gitignore(root)
  File "c:\users\username\anaconda3\envs\project-name\lib\site-packages\black\files.py", line 122, in get_gitignore
    lines = gf.readlines()
UnicodeDecodeError: 'cp949' codec can't decode byte 0xb0 in position 13: illegal multibyte sequence

* Made .gitignore File Reader Detect Its Encoding
* Revert "Made .gitignore File Reader Detect Its Encoding"

  This reverts commit 6c3a7ea42b5b1e441cc0026c8205d1cee68c1bba.

* Revert "Solved Problem with non-alphabetical .gitignore files"

  This reverts commit b0100b5d91c2f5db544a60f34aafab120f0aa458.

* Made .gitignore Reader Open the File with Auto Encoding Detecting

  https://docs.python.org/3.8/library/tokenize.html#tokenize.open

* Revert "Made .gitignore Reader Open the File with Auto Encoding Detecting"

  This reverts commit 50dd80422938649ccc8c7f43aac752f9f6481779.

* Made .gitignore Reader Use UTF-8
* Updated CHANGES.md for #2229
* Updated CHANGES.md for #2229
* Update CHANGES.md
* Update CHANGES.md

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Łukasz Langa <lukasz@langa.pl>
Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Build macOS releases (#2198)

* Add macOS release target
* Update ubuntu runner

Ubuntu 16.04 runner environment is deprecated
https://github.blog/changelog/2021-04-29-github-actions-ubuntu-16-04-lts-virtual-environment-will-be-removed-on-september-20-2021/ | 1 |
| Link isort profile to Black code style isort mention (#2246)

The isort configuration currently in the Black code style document is
duplicated in Using Black with other tools document. I think it would
be better to consolidate information and simply link to the tool guide,
mentioning the easy profile in the original document.

I changed the link from isort PyPI page to Black's docs on isort
because for users it could be better to see the Black docs on why that
configuration is necessary and what isort is from Black's perspective. | 1 |
| Fix test requirements file name (#2245) | 1 |
| Make Prettier preserve line ending type (#2244)

Why? The default in Prettier 2.0 was
[changed](https://prettier.io/docs/en/options.html#end-of-line) from
`auto` to `LF`. This makes development on Windows awkward, because
every file is marked with changes both by Prettier and then by Git
regardless of repository line ending settings, making committing harder
than it should be.

---

Aside from that: I noticed that runnin pre-commit manually seems to add
line endings to symlink files, but they disappear when actually committing.
Don't know if that's a known.. quirk..(?) or not.

---

Commit history before merge:

* Make Prettier preserve line ending type
* Move options to .prettierrc | 1 |
| Fix: black only respects the root gitignore. (#2225)

Commit history before merge:

Black now respects .gitignore files in all levels, not only root/.gitignore file
(apply .gitignore rules like git does).

* Fix: typo
* Fix: respect .gitignore files in all levels.
* Add: CHANGELOG note.
* Fix: TypeError: unsupported operand type(s) for +: 'NoneType' and 'PathSpec'
* Update docs.
* Fix: no parent .gitignore
* Add a comment since the if expression is a bit hard to understand
* Update tests - conver no parent .gitignore case.
* Use main's Pipfile.lock instead

  The original changes in Pipfile.lock are whitespace only. The changes
  turned the JSON's file indentation from 4 to 2. Effectively this
  happened: `json.dumps(json.loads(old_pipfile_lock), indent=2) + "\n"`.

  Just using main's Pipfile.lock instead of undoing the changes because
  1) I don't know how to do that easily and quickly, and 2) there's a
  merge conflict.

  Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

* Merge remote-tracking branch 'upstream/main' into i1730 …
  
  conflicts for days ay? | 1 |
| Include Jelle's review suggestions | 1 |
| Update vim plugin manual installation instructions. (#2235) | 1 |
| Add issue triage documentation (#2236)

* Add issue triage documentation

Co-authored-by: Łukasz Langa <lukasz@langa.pl>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Add lower bound for aiohttp-cors + fix primer (#2231)

It appears sqlalchemy has recently reformatted their project with
Black 21.5b1.

Most of our dependencies have a lower bound and creating a test
environment with the oldest acceptable dependencies runs the full
Black test suite just fine. The only exception to this is aiohttp-cors.
It's unbounded and the oldest version 0.1.0 until 0.4.0 breaks the
test suite in such an old environment.

Failure with 0.1.0:

```
tests/test_blackd.py:10: in <module>
    import blackd
testenv/lib/python3.8/site-packages/blackd/__init__.py:12: in <module>
    import aiohttp_cors
testenv/lib/python3.8/site-packages/aiohttp_cors/__init__.py:29: in <module>
    from .urldispatcher_router_adapter import UrlDistatcherRouterAdapter
testenv/lib/python3.8/site-packages/aiohttp_cors/urldispatcher_router_adapter.py:27: in <module>
    class UrlDistatcherRouterAdapter(RouterAdapter):
testenv/lib/python3.8/site-packages/aiohttp_cors/urldispatcher_router_adapter.py:32: in UrlDistatcherRouterAdapter
    def route_methods(self, route: web.Route):
E   AttributeError: module 'aiohttp.web' has no attribute 'Route'
```

For 0.2.0:

```
tests/test_blackd.py:10: in <module>
    import blackd
testenv/lib/python3.8/site-packages/blackd/__init__.py:12: in <module>
    import aiohttp_cors
testenv/lib/python3.8/site-packages/aiohttp_cors/__init__.py:27: in <module>
    from .cors_config import CorsConfig
testenv/lib/python3.8/site-packages/aiohttp_cors/cors_config.py:24: in <module>
    from .urldispatcher_router_adapter import UrlDistatcherRouterAdapter
testenv/lib/python3.8/site-packages/aiohttp_cors/urldispatcher_router_adapter.py:27: in <module>
    class UrlDistatcherRouterAdapter(AbstractRouterAdapter):
testenv/lib/python3.8/site-packages/aiohttp_cors/urldispatcher_router_adapter.py:32: in UrlDistatcherRouterAdapter
    def route_methods(self, route: web.Route):
E   AttributeError: module 'aiohttp.web' has no attribute 'Route'
```

For 0.3.0:

```
ERROR: Cannot install aiohttp-cors==0.3.0 and aiohttp==3.6.0 because these package versions have conflicting dependencies.

The conflict is caused by:
    The user requested aiohttp==3.6.0
    aiohttp-cors 0.3.0 depends on aiohttp<=0.20.2 and >=0.18.0

To fix this you could try to:
1. loosen the range of package versions you've specified
2. remove package versions to allow pip attempt to solve the dependency conflict

ERROR: ResolutionImpossible: for help visit https://pip.pypa.io/en/latest/user_guide/#fixing-conflicting-dependencies
``` | 1 |
| Use codespell to find typos (#2228) | 1 |
| Modify when Test, Primer, and Documentation Build run (#2226)

- Test and Primer don't run for documentation only changes since it's
  unnecessary, eating unnecessary cycles and slowing down CI since these
  workflows eat up the 20 max workers limit quite easily!

- Documentation Build runs all of the time now since quite a bit of the
  content depends on Black's code so even a simple 1-file change in
  src/black/__init__.py may break the docs build. It's not like this is
  a costly workflow anyway.

Fuzz is still running on all changes because with fuzzing, the more the
better in general. 6 or 7 jobs on a documentation only commit is much
better than 27/28 jobs anyway :p

I also found an error in our bug report issue template :) | 1 |
| Click 8.0 renamed its "die on LANG=C" function so we need to look for that one too (#2227) | 1 |
| Remove useless flake8 config + test support code (#2221)

We've depended on Click 7.x ever since we broke CI systems across the
world (oops lol) and flake8-mypy was purged a fair bit back: #1867

Also remove the primer tests import in tests/test_black.py because it's
annoying when just trying to actually target tests/test_black.py tests.
`pytest -k test_black.py` doesn't do what you expect due to that import. | 1 |
| Add stable tag process to release process documentation (#2224)

* Add stable tag process to release process documentation
- Add reasoning + step commands

* Bah - I ran the linter but forgot to commit

* Update docs/contributing/release_process.md

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| fix typo (#2217) | 1 |
| Update CHANGES.md for 21.5b1 release (#2215) | 1 |
| Release process docs (#2214)

* Setup groundwork for release process docs

I'm using MyST for the index page since I like it more and it's easier
to work with.

* Fill in Release Process for black

* Apply suggestions from code review

Apply Jelle's grammar + typo fixes. I am a terrible only English speaker.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>

* Update release_process.md

Make lint happy via web UI.

* Move to contribution section and fix prettier

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Cover more in the usage docs (#2208)

Commit history before merge:

* Cover more in the usage docs
* Minor fixes
* Even more corrections by Jelle
* Update docs/usage_and_configuration/the_basics.md

  Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Autogenerate black(d|-primer)? help in usage docs (#2212)

So these won't go out of date. This does mean the environment has be
setup a bit more carefully so the right version of the tool is used,
but thankfully the build environment is rebuilt on change on RTD anyway.

Also since the HTML docs are known to build fine, let's provide
downloadable HTMLzips of our docs.

This change needs RTD and GH to install Black with the [d] extra so
blackd's help can generated. While editing RTD's config file, let's
migrate the file to a non-deprecated filename.

Also I missed adding AUTHORS.md to the files key in the doc GHA config. | 1 |
| Replace references to master branch (#2210)

Commit history before merge:

* Replace references to master branch
* Update .flake8 to reference docs on RTD

  We're moving away from GitHub as a documentation host to only RTD because
  it's makes our lives easier creating good docs. I know this link is dead right now,
  but it won't be once we release a new version with the documentation reorganization
  changes (which should be soon!).

  Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Remove docker CI from look at 'master' branch (#2209) | 1 |
| Fix autodoc refs broken by refactor (#2207) | 1 |
| Reorganize docs v2 (GH-2174)

I know I know, this is the second reorganization of the docs. I'm not
saying the first one was bad or anything... but.. actually wait nah,
*it was bad*.

Anyway, welcome to probably my biggest commit. The main thing with this
reorganization was to introduce nesting to the documentation! Having
all of the docs be part of the main TOC was becoming too much. There
wasn't much room to expand either. Finally, the old setup required
a documentation generation step which was just annoying.

The goals of this reorganization was to:

1. Significantly restructure the docs to be discoverable and
   understandable

2. Add room for further docs (like guides or contributing docs)

3. Get rid of the doc generation step (it was slow and frustrating)

4. Unblock other improvements and also just make contributing to the
   docs easier

Another important change with this is that we are no longer using GitHub
as a documentation host. While GitHub does support Markdown based docs
actually pretty well, the lack of any features outside of GitHub Flavoured
Markdown is quite limiting. ReadTheDocs is just much better suited for
documentation. You can use reST, MyST, CommonMark, and all of their
great features like toctrees and admonitions.

Related to this change, we're adopting MyST as our flavour of Markdown.
MyST introduces neat syntax extensions to Markdown that pretty much
gives us the best of both worlds. The ease of use and simplicity of MD
and the flexibility and expressiveness of reST. Also recommonmark is
deprecated now. This switch was possible now we don't use GH as a docs
host. MyST docs have to be built to really be usable / pretty, so the MD
docs are going to look pretty bad on GH, but that's fine now!

Another thing that should be noted is that the README has been stripped
of most content since it was confusing. Users would read the README and
then think some feature or bug was fixed already and is available in a
release when in reality, they weren't. They were reading effectively
the latest docs without knowing.

See also: https://github.com/psf/black/issues/1759

FYI: CommonMark is a rationalized version of Markdown syntax

--

Commit history before merge:

* Switch to MyST-Parser + doc config cleanup

  recommonmark is being deprecated in favour of MyST-Parser. This change
  is welcomed, especially since MyST-Parser has syntax extensions for the
  Commonmark standard. Effectively we get to use a language that's powerful
  and expressive like ReST, but get the simplicity of Markdown.

  The rest of this effort will be using some MyST features.

  This reorganization efforts aims to remove as much duplication as possible.
  The regeneration step once needed is gone, significantly simplifing our
  Sphinx documentation configuration.

* Tell pipenv we replaced recommonmark for MyST-Parser

  Also update `docs/requirements.txt`

* Delete all auto generated content
* Switch prettier for mdformat (plus a few plugins)

  **FYI: THIS WAS EFFECTIVELY REVERTED, SEE THIRD TO LAST COMMIT**

  prettier doesn't support MyST's syntax extensions which are going to be
  used in this reorganization effort so we have to switch formatter.

  Unfortanately mdformat's style is different from prettier's so time to
  reformat the whole repo too.

  We're excluding .github/ISSUE_TEMPLATE because I have no idea whether
  its changes are safe, so let's play it safe.

* Fix the heading levels in CHANGES.md + a link

  MyST-Parser / sphinx's linkcheck complains otherwise.

* Move reference docs into a docs/contributing dir

  They're for contributors of Black anyway. Also added a note in the
  summary document warning about the lack of attention the reference has
  been dealing with.

* Rewrite and setup the new landing page + main TOC

  - add some more detail about Black's beta status
  - add licensing info
  - add external links in the main TOC for GitHub, PyPI, and IRC
  - prepare main TOC for new structure

* Break out AUTHORS into its own file

  Not only was the AUTHORS list quite long, this makes it easy to include
  it in the Sphinx docs with just a simple symlink.

* Add license to docs via a simple include

  Yes the document is orphaned but it is linked to in the landing page
  (docs/index.rst).

* Add "The Black Code Style" section

  This mostly was a restructuring commit, there has been a few updates but
  not many. The main goal was to split "current style" and "planned
  changes to the style that haven't happened yet" to avoid confusion.

* Add "Getting Started" page

  This is basically a quick start + even more. This commit is certainly
  one of most creatively involved in this effort.

* Add "Usage and Configuration" section

  This commit was as much restructuring as new content. Instead of being
  in one giant file, usage and configuration documentation can expand
  without bloating a single file.

* Add "Integrations" section

Just a restructuring commit ...

* Add "Guides" section

  This is a promising area of documentation that could easily grow in the
  future, let's prepare for that!

* Add "Contributing" section

  This is also another area that I expect to see significant growth in.
  Contributors to Black could definitely do with some more specific docs
  that clears up certain parts of our slightly confusing project (it's
  only confusing because we're getting big and old!).

* Rewrite CONTRIBUTING.md to just point to RTD
* Rewrite README.md to delegate most info to RTD
* Address feedback + a lot of corrections and edits

  I know I said I wanted to do these after landing this but given there's
  going to be no time between this being merged and a release getting
  pushed, I want these changes to make it in.

  - drop the number flag for mdformat - to reduce diffs, see also:
    https://mdformat.readthedocs.io/en/stable/users/style.html#ordered-lists
  - the GH issue templates should be safe by mdformat, so get rid of the
    exclude
  - clarify our configuration position - i.e. stop claiming we don't have
    many options, instead say we want as little formatting knobs as
    possible
  - lots and lots of punctuation, spelling, and grammar corrections (thanks
    Jelle!)
  - use RTD as the source for the CHANGELOG too
  - visual style cleanups
  - add docs about our .gitignore behaviour
  - expand GHA Action docs
  - clarify we want the PR number in the CHANGELOG entry
  - claify Black's behaviour for with statements post Python 3.9
  - italicize a bunch of "Black"s

  Thank you goes to Jelle, Taneli (hukkinj1 on GH), Felix
  (felix-hilden on GH), and Wouter (wbolster on GH) for the feedback!

* Merge remote-tracking branch 'upstream/master' into reorganize-docs-v2

  merge conflicts suck, although these ones weren't too bad.

* Add changelog entry + fix merge conflict resolution error

  I consider this important enough to be worthy of a changelog entry :)

* Merge branch 'master' into reorganize-docs-v2

  Co-authored-by: Łukasz Langa <lukasz@langa.pl>

* Actually let's continue using prettier

  Prettier works fine for all of the default MyST syntax so let's not
  rock the boat as much. Dropping the mdformat commit was merge-conflict
  filled so here's additional commit instead.

* Address Cooper's, Taneli's, and Jelle's feedback

  Lots of wording improvements by Cooper. Taneli suggested to disable the
  enabled by default MyST syntax not supported by Prettier and I agreed.
  And Jelle found one more spelling error!

* More minor fixes | 1 |
| Speed up tests even more (#2205)

There's three optimizations in this commit:

1. Don't check if Black's output is stable or equivalant if no changes
   were made in the first place. It's not like passing the same code
   (for both source and actual) through black.assert_equivalent or
   black.assert_stable is useful. It's not a big deal for the smaller
   tests, but it eats a lot of time in tests/test_format.py since
   its test cases are big. This is also closer to how Black works IRL.

2. Use a smaller file for `test_root_logger_not_used_directly` since
   the logging it's checking happens during blib2to3's startup so the
   file doesn't really matter.

3. If we're checking a file is formatting (i.e. test_source_is_formatted)
   don't run Black over it again with `black.format_file_in_place`.
   `tests/test_format.py::TestSimpleFormat.check_file` is good enough. | 1 |
| Refactor `src/black/__init__.py` into many files (#2206)

* Move string-related utility to functions to strings.py, const.py
* Move Leaf/Node-related functionality to nodes.py
* Move comment-related functions to comments.py
* Move caching to cache.py and Mode/TargetVersion/Feature to mode.py
* Move some leftover functions to nodes.py, comments.py, strings.py
* Add missing files to source list for test runs
* Move line-related functionality into lines.py, brackets into brackets.py
* Move transformers to trans.py
* Move file handling, output, parsing, concurrency, debug, and report
* Move two more functions to nodes.py
* Add CHANGES
* Add numeric.py
* Add linegen.py
* More docstrings
* Include new files in tests

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Speed up test suite via distributed testing (#2196)

* Speed up test suite via distributed testing

Since we now run the test suite twice, one with Python 2 and another
without, full test runs are getting pretty slow. Let's try to
fix that with parallization.

Also use verbose mode on CI since more logs is usually better since
getting more is quite literally impossible.

The main issue we'll face with this is we'll hit
https://github.com/pytest-dev/pytest-xdist/issues/620 sometimes
(although pretty rarely). I suppose we can test this and see if how bad
this bug is for us, and revert if necessary down the line.

Also let's have some colours :tada: | 1 |
| Mark blackd tests with the `blackd` optional marker (#2204)

This is a follow-up of #2203 that uses a pytest marker instead of a bunch of
`skipUnless`.  Similarly to the Python 2 tests, they are running by default and
will crash on an unsuspecting contributor with missing dependencies.  This is
by design, we WANT contributors to test everything.  Unless we actually don't
and then we can run:

  pytest --run-optional=no_blackd

Relatedly, bump required aiohttp to 3.6.0 at least to get rid of expected
failures on Python 3.8 (see 6b5eb7d4651c7333cc3f5df4bf7aa7a1f1ffb45b). | 1 |
| Use optional tests for "no_python2" to simplify local testing (#2203) | 1 |
| Do not use gitignore if explicitly passing excludes (#2170)

Closes #2164.

Changes behavior of how .gitignore is handled. With this change, the rules in .gitignore are only used as a fallback if no exclusion rule is explicitly passed on the command line or in pyproject.toml. Previously they were used regardless if explicit exclusion rules were specified, preventing any overriding of .gitignore rules.

Those that depend only on .gitignore for their exclusion rules will not be affected. Those that use both .gitignore and exclude will find that exclude will act more like actually specifying exclude and not just another extra-excludes. If the previous behavior was desired, they should move their rules from exclude to extra-excludes. | 1 |
| Fix broken Action entrypoint (#2202) | 1 |
| Simplify GitHub Action entrypoint (#2119)

This commit simplifies entrypoint.sh for GitHub Actions by removing
duplication of args and black_args (cf. #1909).

The reason why #1909 uses the input id black_args is to avoid an overlap
with args, but this naming seems redundant. So let me suggest option
and src, which are consistent with CLI. Backward compatibility is
guaranteed; Users can still use black_args as well.

Commit history pre-merge:
* Simplify GitHub Action entrypoint (#1909)
* Fix prettier
* Emit a warning message when `black_args` is used

  This deprecation should be visible in GitHub Action's UI now.

  Co-authored-by: Shota Ray Imaki <shota.imaki.0801@gmail.com>
  Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Enable ` --experimental-string-processing` on most primer projects (#2184)

* Enable ` --experimental-string-processing` on all primer projects
- We want to make this default so need to test it more
- Fixed splat/star bug in extending black args for each project

* Disable sqlalchemy due to crash | 1 |
| Disable pandas while we look into #2193 (#2195) | 1 |
| Update CHANGES.md for 21.5b0 release (#2192)

* Update CHANGES.md for 21.5b0 release

* Make prettier happy | 1 |
| Use pre-commit/action to simplify CI (#2191) | 1 |
| compatible isort config: mention profile first (#2180)

Change the order of possible ways to configure isort:
1. using the profile black
2. custom configuration

Formats section:
change the examples to use the profile black

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Drop Travis CI and migrate Coveralls (#2186)

Travis CI for Open Source is shutting down in a few weeks so the queue
for jobs is insane due to lower resources. I'm 99.99% sure we don't need
it as our Test, Lint, Docs, Upload / Package, Primer, and Fuzz workflows
are all on GitHub Actions. So even though we *can* migrate to the .com
version with its 1000 free Linux minutes(?), I don't think we need to.

more information here:
- https://blog.travis-ci.com/oss-announcement
- https://blog.travis-ci.com/2020-11-02-travis-ci-new-billing
- https://docs.travis-ci.com/user/migrate/open-source-repository-migration

This commit does the following:
- delete the Travis CI configuration
- add to the GHA test workflows so coverage continues to be recorded
  - tweaked coverage configuration so this wouldn't break
- remove any references to Travis CI in the docs (i.e. readme + sphinx
  docs)

Regarding the Travis CI to GitHub Actions Coveralls transition, the
official action doesn't support the coverage files produced by coverage.py
unfornately. Also no, I don't really know what I am doing so don't @ me
if this breaks :p (well you can, but don't expect me to be THAT useful).

The Coveralls setup has two downfalls AFAIK:
- Only Linux runs are used because AndreMiras/coveralls-python-action
  only supports Linux. Although this isn't a big issue since the Travis
  Coveralls configuration only used Linux data too.
- Pull requests from an internal branch (i.e. one on psf/black) will be
  marked as a push coverage build by Coveralls since our anti-duplicate-
  workflows system runs under the push even for such cases. | 1 |
| add test configurations that don't contain python2 optional install (#2190)

add test for negative scenario: formatting python2 code
tag python2 only tests

Co-authored-by: KotlinIsland <kotlinisland@users.noreply.github.com> | 1 |
| Detect `'@' dotted_name '(' ')' NEWLINE` as a simple decorator (#2182)

Previously the RELAXED_DECORATOR detection would be falsely True on that
example. The problem was that an argument-less parentheses pair didn't
pass the `is_simple_decorator_trailer` check even it should. OTOH a
parentheses pair containing an argument or more passed as expected. | 1 |
| primer: Add `--no-diff` option (#2187)

- Allow runs with no code diff output
- This is handy for reducing output to see which file is erroring

Test:
- Edit config for 'channels' to expect no changes and run with `--no-diff` and see no diff output | 1 |
| primer: Renable pandas (#2185)

- It no longer crashes black so we should test on it's code
- Update django reason to name the file causing error
  - Seems it has a syntax error on purpose | 1 |
| Set `is_pyi` if `stdin_filename` ends with `.pyi` (#2169)

Fixes #2167

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Tox has been formatted with Black 21.4b0 (#2175) | 1 |
| Add ability to pass posargs to pytest run in tox.ini (#2173) | 1 |
| Elaborate on what AST changes Black might perform | 1 |
| Update CHANGELOG for 21.4b2 release (#2162) | 1 |
| Remove useless shebangs in non-executable files (#2161)

Such shebangs are only ever used if the file is executed directly, i.e.:

    $ /usr/lib/python3.9/site-packages/black_primer/cli.py

But that doesn't work:

    $ /usr/lib/python3.9/site-packages/black_primer/cli.py
    bash: /usr/lib/python3.9/site-packages/black_primer/cli.py: Permission denied

The lib file even has: "lib is a library, funnily enough" | 1 |
| Add automatic version tagging to Docker CI Pushes (#2132)

* Add automatic version tagging to Docker Uploads
- If the git comment has a tag, set that on the docker images pushed
- If we don't have a tag, we just set `latest_non_release`

* Add trigger on release creation too

* Make prettier happy omn docker.yml | 1 |
| Ignore inaccessible user config (#2158)

Fixes #2157 | 1 |
| Update discussion of AST safety check in README (#2159) | 1 |
| Install primer.json (used by black-primer by default) with black (#2154)

Fixes https://github.com/psf/black/issues/2153 | 1 |
| Add pyanalyze and typeshed to black-primer (#2152)

pyanalyze is one of my projects and it uses `--experimental-string-processing`.

typeshed has a lot of stub files. | 1 |
| Update CHANGELOG for 21.4b1 release (#2151)

* Update CHANGELOG for 21.4b1 release

* Add pathspec minimum bump + update primer not to expect changes for virtualenv | 1 |
| Maintainers += Richard Si (aka ichard26) (#2149) | 1 |
| Stop stripping parens in even more illegal spots (#2148)

We're only fixing them so fuzzers don't yell at us when we break "valid"
code. I mean "valid" because some of the examples aren't even accepted by
Python. | 1 |
| Bump pathspec to >= 0.8.1 to solve invalid .gitignore exclusion handling (#2084)

Also made the Click requirement in Pipfile consistent with setup.py and bumped mypy. | 1 |
| Add more tests for fancy whitespace (#2147) | 1 |
| Fix crash on docstrings ending with "\ " (#2142)

Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| Symlink docs/change_log.md to CHANGES.md, don't copy (#2146)

Super duper janky stopgap fix until I get my documentation reorganization
work done and merged | 1 |
| Don't strip parens in assert / return with assign expr (#2143)

Black would previously strip the parenthesis away from statements like this these ones:

    assert (spam := 12 + 1)
    return (cheese := 1 - 12)

Which happens to be invalid code. Now before making the parenthesis invisible, Black
checks if the assignment expression's parent is an assert stamtment, aborting if True.

Raise, yield, and await are already handled fine.

I added a bunch of test cases from the PEP defining asssignment expressions (PEP 572). | 1 |
| fix magic comma and experimental string cache flags (#2131)

* fix magic comma and experimental string cache flags

* more changelog

* Update CHANGES.md

Co-authored-by: Cooper Lees <me@cooperlees.com>

* fix tests

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Remove Lowercase Hex (PR #1692) from CHANGES.md (#2133)

- It was reverted to not cause so much diff churn on millions of lines of code
- Fix primer config for projects that should now pass | 1 |
| Update CHANGES.md for release (#2129) | 1 |
| Issue 1629 has been closed, let's celebrate! (#2127) | 1 |
| Docker CI: Add missed Checkout step (#2128)

- Reading my error others hit it by forgetting this Checkoutstep too so trying the fix
  - e.g. https://github.com/docker/build-push-action/issues/179
- Makes sense it's needed | 1 |
| Add Docker Github Action (#2086)

* Add Docker Github Action
- Build and upload arm64 + amd64 black images on push to master

This will need a `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` secrets set by someone with access.

* Change tag to push to pyfound/black repository. Thanks @ewdurbin | 1 |
| Work around stability errors due to optional trailing commas (#2126)

Optional trailing commas put by Black become magic trailing commas on another
pass of the tool.  Since they are influencing formatting around optional
parentheses, on rare occasions the tool changes its mind in terms of putting
parentheses or not.

Ideally this would never be the case but sadly the decision to put optional
parentheses or not (which looks at pre-existing "magic" trailing commas) is
happening around the same time as the decision to put an optional trailing
comma.  Untangling the two proved to be impractically difficult.

This shameful workaround uses the fact that the formatting instability
introduced by magic trailing commas is deterministic: if the optional trailing
comma becoming a pre-existing "magic" trailing comma changes formatting, the
second pass becomes stable since there is no variable factor anymore on pass 3,
4, and so on.

For most files, this will introduce no performance penalty since `--safe` is
already re-formatting everything twice to ensure formatting stability.  We're
using this result and if all's good, the behavior is equivalent.  If there is
a difference, we treat the second result as the binding one, and check its
sanity again. | 1 |
| Fix primer config | 1 |
| Re-add .venv to .gitignore | 1 |
| Revert "Use lowercase hex numbers fixes #1692 (#1775)"

This reverts commit 7d032fa848c8910007a0a41c1ba61d70d2846f48. | 1 |
| Document experimental string processing and docstring indentation (#2106) | 1 |
| Handle Docstrings as bytes + strip all whitespace (#2037)

(fixes #1844, fixes #1923, fixes #1851, fixes #2002, fixes #2103) | 1 |
| Make black remove leading and trailing spaces from one-line docstrings (#1740)

Fixes #1738. Fixes #1812.

Previously, Black removed leading and trailing spaces in multiline docstrings but failed to remove them from one-line docstrings. | 1 |
| Instructions on documenting code style modifications (#2109) | 1 |
| Fix small comment typo (#2112)

We probably don't need to fall back on "polling" when setting up an asyncio loop | 1 |
| Add missing instructions to make test passed (#2100) | 1 |
| Remove NBSP at the beginning of comments (#2092)

Closes #2091 | 1 |
| Added not formatting files in gitignore (psf#1682) (#1734) | 1 |
| fix typing issue around lru_cache arguments (#2098)

This was found by python/mypy#10308 | 1 |
| Exclude venv directory by default (#1683)

Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| Fix error from upcoming typeshed change (#2096)

See python/typeshed#5190 | 1 |
| Add `black` Dockerfile (#1916)

* Add `black` Dockerfile
- Build a `black` container based on python3-slim
- Test: `docker build --network-host --tag black .`
```console
cooper-mbp1:black cooper$ docker image ls
REPOSITORY                                         TAG            IMAGE ID       CREATED              SIZE
black                                              latest         0c7636402ac2   4 seconds ago        332MB
```

Addresses part of #1914

* Build with colorama + d extra installs - Adds ~9mb

* Combine all build commands in 1 run
- Combine the commands all into 1 RUN to down the size
- Get to 65.98 MB image size now: https://hub.docker.com/repository/docker/cooperlees/black/tags?page=1&ordering=last_updated

* Add rm -r of /var/lib/apt/lists/* + save 10mb down to 55mb | 1 |
| Bump urllib3 from 1.26.3 to 1.26.4 (#2090)

Bumps [urllib3](https://github.com/urllib3/urllib3) from 1.26.3 to 1.26.4.
- [Release notes](https://github.com/urllib3/urllib3/releases)
- [Changelog](https://github.com/urllib3/urllib3/blob/main/CHANGES.rst)
- [Commits](https://github.com/urllib3/urllib3/compare/1.26.3...1.26.4)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Get rid of redundant spaces in docs (#2085)

Thanks! | 1 |
| Run lint in latest python + update precommit (#2081)

- Lets move to latest and greatest of lints | 1 |
| Push contributors to use Next PR Number (#2080)

This is a tool of my own making. Right now our requirement to have the
PR number in the changelog entry is pretty painful / annoying since
the contributor either has to guess or add the # retroactively after
the PR creation. This tool should make it way less painful by making
it simple to get your PR number beforehand. | 1 |
| Add CONTRBUTING info about CHANGES.md requirement (#2073)

Instruct contributors to add the change line to help save maintainer / releaser time | 1 |
| Add a GitHub Action to build + Upload black to PyPI (#1848)

* Add a GitHub Action to build + Upload black to PyPI
- Build a wheel + sdist
- Upload via twine using token stored in GitHub secrets | 1 |
| Support for top-level user configuration (#1899)

* Added support for top-level user configuration

At the user level, a TOML config can be specified in the following locations:
* Windows: ~\.black
* Unix-like: $XDG_CONFIG_HOME/black (~/.config/black fallback)

Instead of changing env vars for the entire black-primer process, they
are now changed only for the black subprocess, using a tmpdir. | 1 |
| don't require typed-ast | 1 |
| Bump pygments from 2.6.1 to 2.7.4 in /docs (#2076)

Bumps [pygments](https://github.com/pygments/pygments) from 2.6.1 to 2.7.4.
- [Release notes](https://github.com/pygments/pygments/releases)
- [Changelog](https://github.com/pygments/pygments/blob/master/CHANGES)
- [Commits](https://github.com/pygments/pygments/compare/2.6.1...2.7.4)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| GREP for PR reference accepts references that are split over a line (#2072)

Fixes #2070 | 1 |
| Add `--skip-magic-trailing-comma` to CHANGES.md (#2064) | 1 |
| Add entry for `--extend-exclude` in the right place (#2061)

The PR author added the changelog entry for their `extend-exclude` addition
in `docs/change_log.md` which is understandable but incorrect as it will be
overwritten since it's autogenerated from the readme. | 1 |
| Add a GitHub CHANGELOG/News Check (#2057)

- Run grep to see commit has a line mentioning it to CHANGES.md
- Also add a label to disable this being required for PRs that don't need a change entry | 1 |
| Fix indentation in docs/editor_integration.md (#2056)

Numbered list entries' bodies need to be indented or else the list won't
render correctly. | 1 |
| Recommend B950 + 88 char limit instead of 80 (#2050)

[The section about line length][1] was contradictory.

On one side, it said:

> Black will try to respect that [line length limit]. However, sometimes it won't be able to without breaking other rules. In those rare cases, auto-formatted code will exceed your allotted limit.

So black doesn't guarantee that your code is formatted at 88 chars, even when configured with `--line-length=88` (default). Black uses this limit as a "hint" more than a "rule".

OTOH, it also said:

> If you're using Flake8, you can bump max-line-length to 88 and forget about it. Alternatively, use Bugbear's B950 warning instead of E501 and keep the max line length at 80 which you are probably already using.

But that's not true. You can't "forget about it" because Black sometimes won't respect the limit. Both E501 at 88 and B950 at 80 behave the same: linter error at 89+ length. So, if Black happens to decide that a line of code is better at 90 characters that some other fancy style, you land on a unlucky situation where both tools will fight.

So, AFAICS, the best way to align flake8 and black is to:

1. Use flake8-bugbear
2. Enable B950
3. Disable E501
4. Set `max-line-length = 88`

This way, we also tell flake8 that 88 limit is a "hint" and not a "rule". The real rule will be 88 + 10%. If black decides that a line fits better in 97 characters than in 88 + some formatting, _that_ probably means your code has a real problem.

To avoid further confusion, I change the official recommendation here.

[1]: https://github.com/PyCQA/flake8-bugbear/tree/e82bb8d8b855adbf1f6f9757fb1527e93039e0d9#opinionated-warnings | 1 |
| Use vim autoload script (#1157) | 1 |
| Add missing changelog entry for fmt: skip (#2025) | 1 |
| Add ALE (#1753) | 1 |
| Remove unused import statements using Pycln. (#2021)

* remove unused imports using Pycln.

* reverse comma style. | 1 |
| Bump aiohttp from 3.7.3 to 3.7.4 (#2009)

Bumps [aiohttp](https://github.com/aio-libs/aiohttp) from 3.7.3 to 3.7.4.
- [Release notes](https://github.com/aio-libs/aiohttp/releases)
- [Changelog](https://github.com/aio-libs/aiohttp/blob/master/CHANGES.rst)
- [Commits](https://github.com/aio-libs/aiohttp/compare/v3.7.3...v3.7.4)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Black requires Python 3.6.2+ (#1668) | 1 |
| Add formatters-python for atom to editor_integration (#1834) | 1 |
| Turn test_regex into a click callback (#2016)

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Adds --stdin-filename back to changelog (#2017)

* Adds --stdin-filename back to changelog

Looks like this went missing https://github.com/psf/black/commit/dea81b7ad5cfa04c3572771c34af823449d0a8f3#diff-729efdd61772b108539268bdbfd7472521bdc05a7cae6113f62ed2649a3ad9c7

* Update CHANGES.md

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>

* Update CHANGES.md

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Add --extend-exclude parameter (#2005)

Look ma! I contribute to open source!

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Strip redundant parentheses from assignment exprs (#1906)

Fixes #1656 | 1 |
| Wrap arithmetic and binary arithmetic expressions in invisible parentheses (#2001) | 1 |
| Fuzzer testing: less strict special-case regex match passthrough for multi-line EOF exceptions (#1998) | 1 |
| Fixup: update function name in docs to match source (#1997) | 1 |
| Fix for enum changes in 3.10 (#1999) | 1 |
| Indicate that a final newline was added in --diff (#1897) (#1897)

Fixes: #1662

Work-around for https://bugs.python.org/issue2142

The test has to slightly mess with its input data, because the utility
functions default to ensuring the test data has a final newline, which
defeats the point of the test.

Signed-off-by: Paul "TBBle" Hampson <Paul.Hampson@Pobox.com> | 1 |
| Minimize changes: more closely resemble original conditional logic | 1 |
| Readability: reduce boolean nesting | 1 |
| Cleanup: remove unused / redundant variables from conditionals | 1 |
| Simplification: only yield empty omit list when magic trailing comma is present | 1 |
| Simplification: only use special-case token retrieval logic when magic trailing comma is present | 1 |
| Clarity: special case: avoid using variables that have the same names as methods | 1 |
| Consistency: use variable names that correspond to the methods they invoke | 1 |
| Brevity: only use the variables required to convey the intended expressions | 1 |
| Clarity: isolate and extract each responsibility from an overloaded variable | 1 |
| Brevity: rename method | 1 |
| fuzzer: add special-case for multi-line EOF TokenError (#1991)

* Add special-case for multi-line EOF TokenError under Python3.7

* Update conditional check to correspond to original issue description (and include issue hyperlink)

* Add warning and hint regarding replaying the fuzzer code generation

* Include code review suggestion (with modifications for this to follow)

Co-authored-by: Zac Hatfield-Dodds <zac.hatfield.dodds@gmail.com>

* Remove Python version check, since this issue does exist across more recent Python versions than 3.7

* Update explanatory comment

* Add clarification for potentially-ambiguous blib2to3 import

* Update explanatory comment

* Bring comment into consistent format with previous comment

* Revert "Add clarification for potentially-ambiguous blib2to3 import"

This reverts commit 357b7cc03bdb19dd924f1accd428352f4bb44e5c.

Co-authored-by: Zac Hatfield-Dodds <zac.hatfield.dodds@gmail.com> | 1 |
| Use 'args' to Avoid GH workflow warning (#1990)

Unexpected input(s) 'black_args', valid inputs are ['entryPoint', 'args'] | 1 |
| add gedit integration (#1988) | 1 |
| Add "# fmt: skip" directive to black (#1800)

Fixes #1162 | 1 |
| readme: Include Zulip in used-by section (#1987)

Zulip has recently formatted their code in Black and also set Black as a linter: https://github.com/zulip/zulip/pull/15662.
See also https://chat.zulip.org/#narrow/stream/2-general/topic/black. | 1 |
| Fix duplication of checks on internal PRs (#1986)

Internal PRs (that come from branches of psf/black) match both the push
and pull_request events. This leads to a duplication of test, lint, etc.
checks on such PRs. Not only is it a waste of resources, it makes the
Status Checks UI more frustrating to go through.

Patch taken from:
https://github.community/t/duplicate-checks-on-push-and-pull-request-simultaneous-event/18012 | 1 |
| Stability fixup: interaction between newlines and comments (#1975)

* Add test case to illustrate the issue

* Accept carriage returns as valid separators while enumerating comments

Without this acceptance, escaped multi-line statments that use carriage returns will not be counted into the 'ignored_lines' variable since the emitted line values will end with a CR and not an escape character.  That leads to comments associated with the line being incorrectly labeled with the STANDALONE_COMMENT type, affecting comment placement and line space management.

* Remove comment linking to ephemeral build log | 1 |
| Bump cryptography from 3.3.1 to 3.3.2 (#1981)

Bumps [cryptography](https://github.com/pyca/cryptography) from 3.3.1 to 3.3.2.
- [Release notes](https://github.com/pyca/cryptography/releases)
- [Changelog](https://github.com/pyca/cryptography/blob/master/CHANGELOG.rst)
- [Commits](https://github.com/pyca/cryptography/compare/3.3.1...3.3.2)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Regenerate documentation (#1980)

Resolves #1979 and ensures that the content from #1861 is included in the repository-published documentation. | 1 |
| Update prettier in pre-commit config (#1966)

With older versions of prettier, when the hook failed a bunch of
"[error] No parser could be inferred for file: {PATH}" error lines
showed up because of lack of support of a flag that pre-commit
passes for us by default. It made figuring out why the prettier hook
failed annoying. | 1 |
| speed up cache by approximately 42x by avoiding pathlib (#1953) | 1 |
| Bump bleach from 3.2.1 to 3.3.0 (#1957)

Bumps [bleach](https://github.com/mozilla/bleach) from 3.2.1 to 3.3.0.
- [Release notes](https://github.com/mozilla/bleach/releases)
- [Changelog](https://github.com/mozilla/bleach/blob/master/CHANGES)
- [Commits](https://github.com/mozilla/bleach/compare/v3.2.1...v3.3.0)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Remove placeholder exit code in unreachable 'black-primer' subprocess handler (#1952) | 1 |
| Update example exclude to match only files in root (#1861)

* Update example exclude to match only files in root

The `exclude` section of the example `pyproject.toml` file didn't work
as expected. It claimed to exclude matched files only in the project
root, but it actually excluded matched files at any directory level
within the project. We can address this by prepending `^/` to the regex
to ensure that it only matches files in the project root.

See https://github.com/psf/black/issues/1473#issuecomment-740008873 for
explanation.

* Mention excluding directories as well | 1 |
| Add --skip-magic-trailing-comma (#1824) | 1 |
| OSS-Fuzz integration (#1930) | 1 |
| Fix typo (#1931) | 1 |
| Update link pointing to how-black-wraps-lines (#1925)

The section about the Black code style has been moved to its own file.
Update link on the compatible configs page to point to the right place. | 1 |
| Update Contributing Docs (#1915)

* Update Contributing Docs
- Update docs with all new tox hotness
- Test running docs build:
  - `sphinx-build -a -b html -W docs/ docs/_build/`

Fixes #1907

* Fix docs/contributing_to_black.md lint

* Remove autogenerated copy pasta

* Fix review typos + regen automated docs via Running Sphinx v1.8.5 | 1 |
| Set gh action entrypoint interpreter to bash (#1919) | 1 |
| fix #1917 (#1918) | 1 |
| Changed max workers on windows to 60 (#1912) | 1 |
| Release gh action (#1909)

* Gets gh-action ready for marketplace release

* Updates documentation and removes redundant gh-action input argument

* Fixes gh-action bug

This commit fixes a bug which caused not all input arguments were forwarder to the black formatter.

* Update README.md

Co-authored-by: Cooper Lees <me@cooperlees.com>

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Only require typing-extensions if Python < 3.8 (#1873) | 1 |
| Use Prettier pre-commit mirror (#1896)

This fixes Prettier install failures similar to those seen in
https://github.com/prettier/prettier/issues/9459, and is the solution
recommended there.

Signed-off-by: Paul "TBBle" Hampson <Paul.Hampson@Pobox.com> | 1 |
| Bump minimum_pre_commit_version per recommendation (#1895)

Recommended by @asottile, the pre-commit author and maintainer, to avoid
some breakages in version 2.9.0. | 1 |
| Add pyi file support to .pre-commit-hooks.yaml (#1875)

Since pre-commit 2.9.0 (2020-11-21), the types_or key can be used to
match multiple disparate file types. For more upstream details, see:
https://github.com/pre-commit/pre-commit/issues/607

Add the minimum_pre_commit_version to require pre-commit 2.9.0+.

Fixes #402 | 1 |
| Bump typed-ast to fix for s390x (#1892)

* Bump typed-ast to fix for s390x

* pipenv install typed-ast==1.4.2 | 1 |
| As long as it's black (#1893)

Make background transparent for dark mode | 1 |
| Fix INTERNAL ERROR caused by removing parens from pointless string (#1888)

Fixes #1846. | 1 |
| Bump mypy to 0.780 in pre-commit config (#1887)

To avoid hitting a mypy bug causes pre-commit to always fail on CPython
3.9. Even though it's still an outdated version, the bug effectively
blocks development on CPython 3.9 so that's why this commit exists
instead of waiting for cooperlees to finish his bump to 0.790 PR.

Also this fixes primer to ensure it always raises CalledProcessError
with an int error code. I stole the patch from cooperlees's mypy bump
PR.

It's funny how mypy 0.790 is already asked for in our
Pipfile.lock file, but oh well mypy is probably more commonly run
through pre-commit than standalone I guess.

Oh and if you're curious why the bug doesn't up on CPython 3.8 or lower:
there was some subscription AST changes in CPython 3.9. | 1 |
| Fuzz on Python 3.9 too (#1882)

Fuzzing on Python 3.9 used to cause errors but now they have disappeared
on more modern Python 3.9 and Hypothesmith. | 1 |
| fix format_str() docstring to prevent users from running into NameError (#1885) | 1 |
| Remove all trace of flake8-mypy (#1867)

flake8-mypy is long dead and shouldn't be used, see
https://github.com/ambv/flake8-mypy. We appear to use pre-commit to run
mypy now anyway.

I ran `pipenv uninstall flake8-mypy`, which seems to have made several
changes to Pipfile.lock. Let me know if there's a better way to do this.

Co-authored-by: hauntsaninja <> | 1 |
| vim plugin: Add quiet flag so non-error actions go unreported (#1733) | 1 |
| Switch back to Python 3.8 for ReadTheDocs (#1839)

ReadTheDocs doesn't support Python 3.9 yet. | 1 |
| Allow same RHS expressions in annotated assignments as in regular assignments (#1835) | 1 |
| Provide a stdin-filename to allow stdin to respect force-exclude rules (#1780)

* Provide a stdin-filename to allow stdin to respect exclude/force-exclude rules

This will allow automatic tools to enforce the project's
exclude/force-exclude rules even if they pass the file through stdin to
update its buffer.

This is a similar solution to --stdin-display-name in flake8.

* Update src/black/__init__.py

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

* --stdin-filename should only respect --exclude-filename

* Update README with the new --stdin-filename option

* Write some tests for the new stdin-filename functionality

* Apply suggestions from code review

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com>

* Force stdin output when we asked for stdin even if the file exists

* Add an entry in the changelog regarding --stdin-filename

* Reduce disk reads if possible

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

* Check for is_stdin and p.is_file before checking for p.is_dir()

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>
Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Use lowercase hex numbers fixes #1692 (#1775)

* Made hex lower case

* Refactored numeric formatting section

* Redid some refactoring and removed bloat

* Removed additions from test_requirements.txt

* Primer now expects expected changes

* Undid some refactoring

* added to changelog

* Update src/black/__init__.py

Co-authored-by: Zsolt Dollenstein <zsol.zsol@gmail.com>

Co-authored-by: Zsolt Dollenstein <zsol.zsol@gmail.com>
Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Correctly handle inline tabs in docstrings (#1810)

The `fix_docstring` function expanded all tabs, which caused a
difference in the AST representation when those tabs were inline and not
leading. This changes the function to only expand leading tabs so inline
tabs are preserved.

Fixes #1601. | 1 |
| Fix bug which causes f-expressions to be split (#1809)

Closes #1807. | 1 |
| Automatically build and upload binaries on release (#1743)

This commit adds a new GitHub Actions workflow that builds self-contained
binaries / executables and uploads them as release assets to the triggering
release. Publishing a release, drafting one doesn't count, will trigger this
workflow.

I personally used GitHub Actions only because it's the CI/CD platform(?)
I am familiar with. Only Windows and Linux binaries are supported since
I don't have any systems running Mac OS.

For Linux, I had originally planned to use the manylinux2010 docker image
the PyPA provides for highly compatible wheel building, but unfortunately
it wasn't feasible due to GitHub Actions and PyInstaller incompatibilities.
As a stopgap the oldest versions of Linux and Windows are used although
Windows Server 2019 isn't that old nor is Ubuntu 16.04! I guess someone
(maybe me) could work out something else if compatibility is big problem.

A few things you should know about the workflow:
 - You don't need to set the `GITHUB_TOKEN` secret as it is automatically
   provided by GitHub.
 - matrix.pathsep is used because PyInstaller configuration's format is OS
   dependent for some reason ...

Also it's worth mentioning that Black once had Travis CI and AppVeyor
configuration that did the same thing as this commit. They were committed
in mid 2018 and worked (somewhat) well. Eventually we stopped using AppVeyor
and the refactor to packages broke the Travis CI config. This commit
replaces the still existing and broken Travis CI config wholesale.

Co-authored-by: Anders Fredrik Kiær <31612826+anders-kiaer@users.noreply.github.com>

 - Anders told me that I could get the release asset upload URL directly
   from the github.event.release payload. I originally planned to use
   bruceadams/get-release to get such URL. | 1 |
| Fix bug where black tries to split string on escaped space (#1799)

Closes #1505. | 1 |
| Start using Python 3.9 on Travis (#1790)

* Start using Python 3.9 on Travis

* Remove allow_failures | 1 |
| Bump cryptography from 3.1 to 3.2 (#1791)

Bumps [cryptography](https://github.com/pyca/cryptography) from 3.1 to 3.2.
- [Release notes](https://github.com/pyca/cryptography/releases)
- [Changelog](https://github.com/pyca/cryptography/blob/master/CHANGELOG.rst)
- [Commits](https://github.com/pyca/cryptography/compare/3.1...3.2)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Add compatible configuration files. (psf#1789) (#1792)

* Add compatible configuration files. (psf#1789)

* Simplify isort configuration files. (#1789) | 1 |
| Extract formatting tests (#1785)

* Update test versions

* Use parametrize to remove tests duplications

* Extract sources format tests

* Fix mypy errors

* Fix .travis.yml | 1 |
| Document some culprits with pre-commit (#1783)

* Document some culprits with pre-commit

* make pre-commit happy

* don't use monospace for black & pre-commit

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com>

* make pre-commit happy again

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Update readme.md with current version (#1788)

* Update readme.md with current version

* Update version_control_integration.md | 1 |
| Switch to pytest and tox (#1763)

* Add venv to .gitignore

* Use tox to run tests

* Make fuzz run in tox

* Split tests files

* Fix import error | 1 |
| Update PyCharm integrations instructions to avoid running on external changes (#1769) | 1 |
| Fix GitHub markdown links to work on RTD (#1752)

* Fix internal links to work on RTD

Note that these still lead to GitHub, instead of staying on RTD.

* Point links to better anchors | 1 |
| Allow black's Github action params overriding. (#1755)

* Allow default params overriding.

* Update: docs and action.yaml.

* The second contirbution, add my name to authors.md

* Correct docs `with.args` example.

* Just to rerun the Travis jobs.

* chmod 755 | 1 |
| Add blackd to nicely exit if missing aiohttp deps (#1761)

- If no aiohttp* deps exist nicely print a helpful message and exit
- There seems to be no nice way to optionally install the entry point, so lets make the entry point nicer

Test:
```
cooper-mbp1:black cooper$ /tmp/tb/bin/pip install .
cooper-mbp1:black cooper$ /tmp/tb/bin/blackd
aiohttp dependency is not installed: No module named 'aiohttp'. Please re-install black with the '[d]' extra install  to obtain aiohttp_cors: `pip install black[d]`
cooper-mbp1:black cooper$ /tmp/tb/bin/pip install .[d]
...
Successfully installed aiohttp-3.6.3 aiohttp-cors-0.7.0 black
cooper-mbp1:black cooper$ /tmp/tb/bin/blackd
blackd version 20.8b2.dev31+gdd2f86a.d20201013 listening on localhost port 45484
```

Fixes #1688 | 1 |
| Support stable Python3.9. (#1748)

* Support stable Python3.9.

* Get back to 3.9-dev

* Add py39 to black usage.

* remove 3.9 temporarily. | 1 |
| docs: update `used-by` link to proper url (#1745) | 1 |
| Primer: pyramid and sqlalchemy are now formatted with latest Black (#1736) | 1 |
| Fix unnecessary if checks (#1728) | 1 |
| Hypothesis is now formatted with Black 20.8b1 (#1729) | 1 |
| Repair colorama wrapping on non-Windows platforms (#1670)

* Repair colorama wrapping on non-Windows platforms

The wrap_stream_for_windows() function calls
colorama.initialise.wrap_stream() function to apply colorama's magic to
wrapper to the output stream. Except this wrapper is only applied on
Windows platforms that need it, otherwise the original stream is
returned as-is.

The colorama wrapped stream lacks a detach() method, so a no-op lambda
was being assigned to the wrapped stream.

The problem is that the no-op lambda was being assigned unconditionally
whether or not colorama actually returns a wrapped stream, thus
replacing the original TextIOWrapper's detach() method. Replacing the
detach() method with a no-op lambda is the root cause of the problem
observed in #1664.

The solution is to only assign the no-op detach() method if the stream
lacks its own detach() method.

Repairs #1664 | 1 |
| End 'force-exclude' help message with a period (#1727)

It would be nice, if like other options help message, force-exclude's
help message also ends with a period punctuation mark. | 1 |
| PEP 614 support (#1717) | 1 |
| Fix typo in docstring (#1700)

Added a missing preposition | 1 |
| Fix empty line handling when formatting typing stubs (#1646)

Black used to erroneously remove all empty lines between non-function
code and decorators when formatting typing stubs. Now a single empty
line is enforced.

I chose for putting empty lines around decorated classes that have empty
bodies since removing empty lines around such classes would cause a
formatting issue that seems to be impossible to fix.

For example:

```
class A: ...
@some_decorator
class B: ...
class C: ...
class D: ...

@some_other_decorator
def foo(): -> None: ...
```

It is easy to enforce no empty lines between class A, B, and C.
Just return 0, 0 for a line that is a decorator and precedes an stub
class. Fortunately before this commit, empty lines after that class
would be removed already.

Now let's look at the empty line between class D and function foo. In
this case, there should be an empty line there since it's class code next
to function code. The problem is that when deciding to add X empty lines
before a decorator, you can't tell whether it's before a class or a
function. If the decorator is before a function, then an empty line
is needed, while no empty lines are needed when the decorator is
before a class.

So even though I personally prefer no empty lines around decorated
classes, I had to go the other way surrounding decorated classes with
empty lines.

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Virtualenv is now formatted with newest Black https://github.com/pypa/virtualenv/pull/1939 (#1695) | 1 |
| Fix unstable subscript assignment string wrapping (#1678)

Fixes #1598 | 1 |
| Add link to conda-forge integration (#1687)

* Add link to conda-forge integration

resolves #1686

* README: keep PyPI tags together

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Fix crash on assert and parenthesized % format (fixes #1597, fixes #1605) (#1681) | 1 |
| Fix crash on concatenated string + comment (fixes #1596) (#1677)

Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| Fix unstable formatting on string split + % formatting (#1680)

Fixes #1595 | 1 |
| Handle .COLOR_DIFF in the same way as .DIFF (#1673) | 1 |
| Test primer on Pillow (#1679) | 1 |
| Update primer.json to reflect Black's adoption (#1674)

- tox recently adopted Black  
https://github.com/tox-dev/tox/commit/a7903508fa07068b327e15cfdbf8ea330ab78765

- attrs already adopted Black but they updated to 20.08b1 + did a format pass and removed some trailing commas
https://github.com/python-attrs/attrs/commit/f680c5b83e65413eeb684c68ece60198015058c3 | 1 |
| Clarify current trailing comma behavior in the docs | 1 |
| Mention optional invalid W503 warning in pycodestyle | 1 |
| Fix incorrect space before colon in if/while stmts (#1655)

* Fix incorrect space before colon in if/while stmts

Previously Black would format this code

```
if (foo := True):
	print(foo)
```

as

```
if (foo := True) :
	print(foo)
```

adding an incorrect space after the RPAR. Buggy code in the
normalize_invisible_parens function caused the colon to be wrapped in
invisible parentheses. The LPAR of that pair was then prefixed with a
single space at the request of the whitespace function.

This commit fixes the accidental skipping of a pre-condition check
which must return True before parenthesis normalization of a specific
child Leaf or Node can happen. The pre-condition check being skipped
was why the colon was wrapped in invisible parentheses.

* Add an entry in CHANGES.md | 1 |
| Remove flake8 W503 from docs as it is ignored by default (#1661)

Fixes #1660 | 1 |
| Fix typo in comment (#1650)

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| fix 1631 and add test (#1641) | 1 |
| Fix multiline docstring quote normalization

The quotes of multiline docstrings are now only normalized when string
normalization is off, instead of the string normalization setting being
ignored and the quotes being *always* normalized.

I had to make a new test case and data file since the current pair for
docstrings only worked when there is no formatting difference between the
formatting results with string normalization on and off. I needed to add
tests for when there *are* differences between the two. So I split
test_docstring's test code when string normalization is disabled into a
new test case along with a new data file. | 1 |
| Fix g:black_fast and g:black_(skip)_string_normalization opts | 1 |
| Revert contains_pragma_comment function changes | 1 |
| Revert contains_standalone_comments function changes | 1 |
| Simplify black code by using generator expressions | 1 |
| Stop running Primer on macOS as it's flaky on GitHub Actions | 1 |
| v20.8b1 | 1 |
| Make dependency on Click 7.0, regex 2020.1.8, and toml 0.10.1 explicit | 1 |
| Add expected failure tests with the unstable formattings | 1 |
| Include mode information for unstable formattings | 1 |
| Treat all trailing commas as pre-existing, as they effectively are

On a second pass of Black on the same file, inserted trailing commas are now
pre-existing.  Doesn't make sense to differentiate between the passes then. | 1 |
| v20.8b0 | 1 |
| Primer update config - enable pytest (#1626)

Reformatted projects I have acceess to:
- aioexabgp
- bandersnatch
- flake8-bugbear

```
-- primer results 📊 --

13 / 16 succeeded (81.25%) ✅
0 / 16 FAILED (0.0%) 💩
 - 3 projects disabled by config
 - 0 projects skipped due to Python version
 - 0 skipped due to long checkout
```

* Also re-enable pytest

```
-- primer results 📊 --

14 / 16 succeeded (87.5%) ✅
0 / 16 FAILED (0.0%) 💩
 - 2 projects disabled by config
 - 0 projects skipped due to Python version
 - 0 skipped due to long checkout

real	2m26.207s
user	17m55.404s
sys	0m43.061s
``` | 1 |
| Add links regarding Spotless integration for gradle/maven users (#1622)

Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| Improve docstring re-indentation handling

This addresses a few crashers, namely:

* producing non-equivalent code due to mangling escaped newlines,

* invalid hugging quote characters in the docstring body to the docstring outer
  triple quotes (causing a quadruple quote which is a syntax error),

* lack of handling for docstrings that start on the same line as the `def`, and

* invalid stripping of outer triple quotes when the docstring contained
  a string prefix.

As a bonus, tests now also run when string normalization is disabled. | 1 |
| Address pre-existing trailing commas when not in the rightmost bracket pair

This required some hackery.  Long story short, we need to reuse the ability to
omit rightmost bracket pairs (which glues them together and splits on something
else instead), for use with pre-existing trailing commas.

This form of user-controlled formatting is brittle so we have to be careful not
to cause a scenario where Black first formats code without trailing commas in
one way, and then looks at the same file with pre-existing trailing commas
(that it itself put on the previous run) and decides to format the code again.

One particular ugly edge case here is handling of optional parentheses.  In
particular, the long-standing `line_length=1` hack got in the way of
pre-existing trailing commas and had to be removed.  Instead, a more
intelligent but costly solution was put in place: a "second opinion" if the
formatting that omits optional parentheses ended up causing lines to be too
long.  Again, for efficiency purposes, Black reuses Leaf objects from blib2to3
and modifies them in place, which was invalid for having two separate
formattings.  Line cloning was used to mitigate this.

Fixes #1619 | 1 |
| Run trailing comma tests with TargetVersion.PY38 | 1 |
| Add more trailing comma test variants | 1 |
| Make doc generation a little smarter, update doc sections | 1 |
| Update the changelog | 1 |
| Property-based fuzz test | 1 |
| Fix dealing with generated files in docs | 1 |
| Use properly renamed function name in docs | 1 |
| Require Sphinx 3 | 1 |
| Mark Primer projects that will change formatting | 1 |
| Open file explicitly with UTF-8 so it works on Windows, too | 1 |
| Reformat docs/conf.py according to the new style | 1 |
| Re-implement magic trailing comma handling:

- when a trailing comma is specified in any bracket pair, that signals to Black
  that this bracket pair needs to be always exploded, e.g. presented as "one
  item per line";

- this causes some changes to previously formatted code that erroneously left
  trailing commas embedded into single-line expressions;

- internally, Black needs to be able to identify trailing commas that it put
  itself compared to pre-existing trailing commas. We do this by using/abusing
  lib2to3's `was_checked` attribute.  It's True for internally generated
  trailing commas and False for pre-existing ones (in fact, for all
  pre-existing leaves and nodes).

Fixes #1288 | 1 |
| Reset trailing comma handling | 1 |
| Upgrade docs to Sphinx 3+ and add doc build test (#1613)

* Upgrade docs to Sphinx 3+
* Fix all the warnings...

- Fixed bad docstrings
- Fixed bad fenced code blocks in documentation
- Blocklisted some sections from being generated from the README
- Added missing documentation to index.rst
- Fixed an invalid autofunction directive in reference/reference_functions.rst
- Pin another documentation dependency

* Add documentation build test | 1 |
| Disable string splitting/merging by default (#1609)

* put experimental string stuff behind a flag
* update tests
* don't need an output section if it's the same as the input
* Primer: Expect no formatting changes in attrs, hypothesis and poetry with --experimental-string-processing off

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Update all dependencies to latest versions | 1 |
| Fix inline code style in README (#1608)

Refer to `pyproject.toml` in HTML section of README with HTML code tags | 1 |
| fix unary op detection (#1600) | 1 |
| don't strip brackets before lsqb (#1575) (#1590)

if the string contains a PERCENT, it's not safe to remove brackets that
follow and operator with the same or higher precedence than PERCENT | 1 |
| fix some docstring crashes (#1593)

Allow removing some trailing whitespace | 1 |
| in verbose mode, print stack trace (#1594)

Make Black failures easier to debug | 1 |
| Make --exclude only apply to recursively found files (#1591)

Ever since --force-exclude was added, --exclude started to touch files
that were given to Black through the CLI too. This is not documented
behaviour and neither expected as --exclude and --force-exclude now
behave the same!

Before this commit, get_sources() when encountering a file that was passed
explicitly through the CLI would pass a single Path object list to
gen_python_files(). This causes bad behaviour since that function
doesn't treat the exclude and force_exclude regexes differently. Which
is fine for recursively found files, but *not* for files given through
the CLI.

Now when get_sources() iterates through srcs and encounters
a file, it checks if the force_exclude regex matches, if not, then the
file will be added to the computed sources set.

A new function had to be created since before you can do regex matching,
the path must be normalized. The full process of normalizing the path is
somewhat long as there is special error handling. I didn't want to
duplicate this logic in get_sources() and gen_python_files() so that's
why there is a new helper function. | 1 |
| Add the direnv base directory to the default excludes (#1564)

Co-authored-by: Chris Rose <offline@offby1.net> | 1 |
| Remove slow assertion (#1592)

Partial fix for #1581

This assertion produces behavior quadratic in the number of leaves in a line, which is making Black extremely slow on files with very long expressions. On my benchmark file this change makes Black 10x faster. | 1 |
| fix spelling (#1567)

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Update to accomodate isort 5 release changes. (#1559)

Isort 5 introduced profiles and ensure_newline_before_comments options. Either needs to be added to work correctly with black.

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| docs: Improve pre-commit use (#1551)

Stable tag wasn't available and crashed when attempting to set initial
pre-commit. Also the python version needs to be installed so it would
be better to use the generic "python3" command. | 1 |
| pre-commit: show diff on failure on CI (#1552)

* pre-commit: --show-diff-on-failure

* pre-commit: --show-diff-on-failure | 1 |
| Update curl command to use stable branch (#1543) | 1 |
| Ensure path for finding root is absolute (#1550)

As Path.resolve() is buggy on windows (see https://bugs.python.org/issue38671)
an absolute path is ensured by prepending the Path.cwd() | 1 |
| Spelling fix in CONTRIBUTING.md (#1547) | 1 |
| ISSUE 1533: Fix --config argument description (#1534)

Change --config argument description to "Read configuration from FILE."
The "--config FILE                   Read configuration from FILE path" | 1 |
| add Quora to orgs that use Black (#1532) | 1 |
| Mozilla uses black too (#1531) | 1 |
| Add pip install from GitHub command to README.md (#1529)

* Add pip install from GitHub command to README.md
* Make it prettier ... | 1 |
| Find project root correctly (#1518)

Ensure root dir is a common parent of all inputs
Fixes #1493 | 1 |
| Fix toml hashes and make it clear that only TOML is supported (#1510)

* Fix TOML hashes

* Make it clear that only TOML is supported | 1 |
| Add missing contributors to README.md (Thank you everyone!) (#1508)

Black wouldn't be where it is without the countless outside contributions.
So thank you y'all and we will share our appreciation by listing your names
(and emails) in the README.md :D | 1 |
| Fix toml parsing and bump toml from 0.10.0 to 0.10.1 (#1501)

* Bump toml from 0.10.0 to 0.10.1 to fix a bug

* Add tests for TOML parsing and reading

* Fix configuration bug affecting vim plugin

The vim plugin directly calls parse_pyproject and skips the Click processing
, but parse_pyproject assumed that it would only be used before Click processing
and therefore made the config values click friendly. This moves the "make the values
click friendly processing" into read_pyproject_toml which is only called by a Click
callback.

* Please mypy and flake8 | 1 |
| Fix grammatical typos in black_primer and blackd (#1504) | 1 |
| Issue template: Add Python code formatting (#1485)

Makes things a bit more readable | 1 |
| Fix find_pyproject_toml type hint (#1495) | 1 |
| Move vim flag cast to outside of get (#1486)

With this config:

```toml
[tool.black]
line-length = 79
```

In neovim, this is loaded as a string which later causes an exception to
be thrown. This makes sure the value is always cast to an int | 1 |
| expression tests: adjust starred expression for Python 3.9 (#1441) (#1477)

As discussed in #1441, Python 3.9's new parser will not parse
`(*starred)` even using `compile()` with the `PyCF_ONLY_AST`
flag (as `ast.parse()` does), it raises a `SyntaxError`. This
breaks the four tests that use this file with Python 3.9.
Upstream does not consider this to be a bug - see
https://bugs.python.org/issue40848#msg370643 - so we must
adjust the expression. As suggested by @JelleZijlstra, this just
adds a comma, which makes the new parser happy with it (the old
parser is fine with this form also).

Signed-off-by: Adam Williamson <awilliam@redhat.com> | 1 |
| GitHub action (#1421)

* Implement a re-usable GitHub Action

Implement a GitHub action that can be reused across projects that want
to run black as part of their CI workflows.

* Fix typo in README.md

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com>

* Use latest Python 3

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Convert (most of the) configuration values from pyproject.toml to strings (#1466)

* Convert config values to string

We need to convert all configuration values from the pyproject.toml
file because Click already does value processing and conversion and
it only expects the exact Python type or string. Click doesn't like
the integer 1 as a boolean for example. This also means other
unsupported types like datetime.time will be rejected by Click as
a unvalid value instead of throwing an exception.

We only skip converting objects that are an instance of
collections.abc.Iterable because it's almost impossible to get back
the original iterable of a stringified iterable.

* Move where the conversion happens

Instead of converting the values in the merged 'default_map', I should
convert the values that were read from the 'pyproject.toml' file.

* Change collections.abc.Iterable to (list, dict)

I also moved where the conversion happens... again. I am rather indecisive
if you haven't noticed. It should be better as it takes place in the
parse_pyproject_toml logic where configuration modification already takes
place.

Actually when this PR was first created I had the conversion happen in that
return statement, but the target_version check was complaining about it being
a string. So I moved the conversion after that check, but then Click didn't
like the stringifed list, which led me to check whether the value was an
instance of an Iterable before turning it into a string. And... I forgot that
type checking before conversion would allow it to work before the
target_version check anyway. | 1 |
| Make 'python -m black' work (#1460) | 1 |
| Update CONTRIBUTION.md with pre-commit + black-primer instructions (#1459)

* Update CONTRIBUTION with pre-commit + black-primer instructions
- Inform people how to run primer and alter it's config
- Link to main documentation

* Apply suggestions from code review

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Refactor docs / Maintenance of docs (#1456)

* Split code style and components documentation

Splits 'the_black_code_style', 'pragmatism', 'blackd',and 'black_primer'
into their own files. The exception being 'the_black_code_style' and
'pragmatism'. They have been merged into one 'the_black_code_style_and_pragmatism'
file.

These changes are being made because the README is becoming very long. And
a README isn't great if it dissuades its reader because of its length.

* Update the doc generation logic and configuration

With the moving of several sections in the README and the renaming of a
few files, 'conf.py' needs to be able to support custom sections.

This commit introduces DocSection which can be used to specify custom
sections of documentation. The information stored in DocSection will be
used by the process_sections function to read, process, and write the section
to CURRENT_DIR.

A large change has been made to the how the docs are prepared to be built.
Instead of just generating the files needed by reading the README, this
has a full chain of operations so custom sections are supported. First,
it reads the README and spits out a list of DocSection objects representing
the sections to be generated by process_sections. This is done since most
of the docs still live in README. Then along with the defined custom_sections
, the process_sections will be begin to process the DocSection objects.
It reads the information it needs to generate the section. Then fetches
the section's contents, calls processors required by the section to process
the section's contents, and finally writes the section to CURRENT_DIR.

This large change is so processing of the documentation can be done just
for the versions hosted on ReadTheDocs.org. An example processor using this
feature is a 'replace_links' processor. It will replace documentation
links that point to the docs hosted on GitHub with links that point to the
version hosted on ReadTheDocs.org. (I won't be coding that ATM)

This also means that files will be overwritten or created once the docs
have been built. It is annoying, since you have to 'git reset --hard'
and 'git clean -f -d' after each build, but there's nothing better. The old
system had the same side effects, so yeah :(

* Update filenames and delete unnecessary files

Update the filenames since 'the_black_code_style' and 'pragmatism' were
merged and 'contributing' was deleted in favor of 'contributing_to_black'.

All symlinks were deleted since their home (_build/generated) is no longer
used.

* Fix broken links and a few redirections

* Merge master into refactor_docs (manually done)

* Add my and most of @hugovk suggestions

Co-Authored-By: Hugo van Kemenade <hugovk@users.noreply.github.com>

* Add logging and improve configurability

Just some cleaning up up of the DocSection dataclass and added logging
support so you know what's going on.

* Rename a section and please the grammar gods of Black

Thanks @hugovk for the suggestion!

* Fix Markdown comments

* Add myself as an author :P

Seems like the right time.

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Scrollable sidebar (#1457)

* Make the sidebar navigation scrollable

This is necessary since we have so many documentation sections that even
on a desktop screen, the navigation can sometimes be clipped. The only
annoyance is that on Firefox, the scrollbar can't be hidden :(

* allow the docs to build | 1 |
| Capture CalledProcessError for any postitive returncode (#1450)

- Leave logic to still allow for formatting changes to be ignored
- Now just capture the output of any other error that has a > 1 returncode
- Raise on anything else

Test: Add unit test to exercise this new logic | 1 |
| Add Primer badge to README.md (#1454) | 1 |
| Move primer to own workflow (#1451) | 1 |
| Update README.md (#1453) | 1 |
| Enable primer on CI Runs + add all README listed black projects into primer.json (#1440)

* Add all listed by projects into primer.json + Enable on CI Runs
- Change workers default to 2 as black uses system CPU count
- Increase timeout to 5 mins for subprocess black runs
- Takes about 120s for 13 (3 disabled) projects on my 2018 Macbook Pro
  - I was not removing directories tho ...

Will open an issue to investigate the failing projects and make this run cleaner.
- Once we get more stable we can expect more repos to be black formatted

Run it:
- `black-primer -k -w /tmp/primer_large_test --debug --rebase`
```
[2020-05-20 21:44:01,273] DEBUG: Starting /Users/cooper/venvs/b/bin/black-primer (cli.py:125)
[2020-05-20 21:44:01,273] DEBUG: Using selector: KqueueSelector (selector_events.py:53)
[2020-05-20 21:44:01,274] INFO: 16 projects to run Black over (lib.py:276)
[2020-05-20 21:44:01,274] DEBUG: Using 2 parallel workers to run Black (lib.py:281)
[2020-05-20 21:44:01,274] DEBUG: worker 0 workng on aioexabgp (lib.py:215)
[2020-05-20 21:44:01,276] DEBUG: worker 1 workng on attrs (lib.py:215)
[2020-05-20 21:44:02,443] INFO: Finished aioexabgp (lib.py:249)
[2020-05-20 21:44:02,443] DEBUG: worker 0 workng on bandersnatch (lib.py:215)
[2020-05-20 21:44:04,409] INFO: Finished bandersnatch (lib.py:249)
[2020-05-20 21:44:04,409] DEBUG: worker 0 workng on channels (lib.py:215)
[2020-05-20 21:44:04,702] INFO: Finished attrs (lib.py:249)
[2020-05-20 21:44:04,702] DEBUG: worker 1 workng on django (lib.py:215)
[2020-05-20 21:44:04,702] INFO: Skipping django as it's disabled via config (lib.py:222)
[2020-05-20 21:44:04,702] DEBUG: worker 1 workng on flake8-bugbear (lib.py:215)
[2020-05-20 21:44:05,813] INFO: Finished channels (lib.py:249)
[2020-05-20 21:44:05,813] DEBUG: worker 0 workng on hypothesis (lib.py:215)
[2020-05-20 21:44:06,071] INFO: Finished flake8-bugbear (lib.py:249)
[2020-05-20 21:44:06,071] DEBUG: worker 1 workng on pandas (lib.py:215)
[2020-05-20 21:44:06,071] INFO: Skipping pandas as it's disabled via config (lib.py:222)
[2020-05-20 21:44:06,071] DEBUG: worker 1 workng on poetry (lib.py:215)
[2020-05-20 21:44:16,207] INFO: Finished hypothesis (lib.py:249)
[2020-05-20 21:44:16,207] DEBUG: worker 0 workng on ptr (lib.py:215)
[2020-05-20 21:44:17,077] INFO: Finished poetry (lib.py:249)
[2020-05-20 21:44:17,077] DEBUG: worker 1 workng on pyramid (lib.py:215)
[2020-05-20 21:44:17,460] INFO: Finished ptr (lib.py:249)
[2020-05-20 21:44:17,460] DEBUG: worker 0 workng on pytest (lib.py:215)
[2020-05-20 21:44:17,460] INFO: Skipping pytest as it's disabled via config (lib.py:222)
[2020-05-20 21:44:17,460] DEBUG: worker 0 workng on sqlalchemy (lib.py:215)
[2020-05-20 21:44:33,319] INFO: Finished pyramid (lib.py:249)
[2020-05-20 21:44:33,319] DEBUG: worker 1 workng on tox (lib.py:215)
[2020-05-20 21:44:42,274] INFO: Finished tox (lib.py:249)
[2020-05-20 21:44:42,275] DEBUG: worker 1 workng on virtualenv (lib.py:215)
[2020-05-20 21:44:47,928] INFO: Finished virtualenv (lib.py:249)
[2020-05-20 21:44:47,928] DEBUG: worker 1 workng on warehouse (lib.py:215)
[2020-05-20 21:45:16,784] INFO: Finished warehouse (lib.py:249)
[2020-05-20 21:45:16,784] DEBUG: project_runner 1 exiting (lib.py:213)
[2020-05-20 21:45:45,700] INFO: Finished sqlalchemy (lib.py:249)
[2020-05-20 21:45:45,700] DEBUG: project_runner 0 exiting (lib.py:213)
[2020-05-20 21:45:45,701] INFO: Analyzing results (lib.py:292)
-- primer results 📊 --

13 / 16 succeeded (81.25%) ✅
0 / 16 FAILED (0.0%) 💩
 - 3 projects disabled by config
 - 0 projects skipped due to Python version
 - 0 skipped due to long checkout
```

* Move to partial for rmtree + specify a onerror handler for PermissionError on Windows for git

* Set default coding to utf8 for very important emoji's on Windows

* Set Python encoding to utf-8 for Windows

* Appease the white space gods of Black!

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com>

Co-authored-by: Richard Si <63936253+ichard26@users.noreply.github.com> | 1 |
| Fix typos (#1442) | 1 |
| black-primer: handle singular and plural in output messages (#1432)

* Handle singular and plural in output messages | 1 |
| Update README.md (#1439) | 1 |
| Add `black-primer` docs (#1427)

* Add `black-primer` docs

- Document the idea, CLI args, config and a example run for `black-primer` in README.md
- Add to docs/index.rst

* Add @hugovk suggestions - Thanks. | 1 |
| Update docs about stable tag now being a branch (#1422)

* Update docs about stable tag now being a branch

Issue #760 had the `stable` tag changed to a branch since it was causing a few issues. This updates the docs to reflect that change.

* Make the prettier hook pass

* Improve wording | 1 |
| Add black-primer unittests (#1426)

* Add black-primer unittests

- Get this tool covered with some decent unittests for all unittests wins
- Have a CLI and lib test class
- Import it from `test_black.py` so we always run tests
- Revert typing asyncio.Queue as Queue[str] so we can work in 3.6
- **mypy**: Until black > 3.6 disallow_any_generics=False for primer code

Test:
- Run tests: `coverage run tests/test_primer.py` or `coverage run -m unittest`
```
(b) cooper-mbp1:black cooper$ coverage report
Name                      Stmts   Miss  Cover
---------------------------------------------
src/black_primer/cli.py      49      8    84%
src/black_primer/lib.py     148     28    81%
tests/test_primer.py        114      1    99%
---------------------------------------------
TOTAL                       311     37    88%
```

* Use ProactorEventLoop for Windows + fix false path for Linux

* Set Windows to use ProactorEventLoop in  to benefit all callers

* sys.platform seems to not having the loop applied - So type ignore and use platform.system() gate

* Have each test loop correctly set to ProactorEventLoop on Windows for < 3.8 too | 1 |
| Update and fix Flake8 (#1424)

* Update pre-commit

* Fix F541 f-string is missing placeholders

* Fix E741 ambiguous variable name 'l'

* Update actions to v2 | 1 |
| Fix typos (#1423) | 1 |
| Add primer CI tool 🏴 (#1402)

* Add primer CI tool 💩
- Run in PATH `black` binary on configured projects
- Can set wether we expect changes or not per project
- Can set what python versions are supported for a project
- if `long_checkout` True project will not be ran on CI

Will add to CI after I finish unit tests to avoid silly bugs I'm sure I have 🤪

Tests:
- Manual Run - Will add unit tests if people think it will be useful
- Output:

```shell
(b) cooper-mbp1:black cooper$ time /tmp/b/bin/black-primer -k -w /tmp/cooper_primer_1
[2020-05-10 08:48:25,696] INFO: 4 projects to run black over (lib.py:212)
[2020-05-10 08:48:25,697] INFO: Skipping aioexabgp as it's disabled via config (lib.py:166)
[2020-05-10 08:48:25,699] INFO: Skipping bandersnatch as it's disabled via config (lib.py:166)
[2020-05-10 08:48:28,676] INFO: Analyzing results (lib.py:225)
-- primer results 📊 --

2 / 4 succeeded (50.0%) ✅
0 / 4 FAILED (0.0%) 💩
 - 2 projects Disabled by config
 - 0 projects skipped due to Python Version
 - 0 skipped due to long checkout

real	0m3.304s
user	0m9.529s
sys	0m1.019s
```

- ls of /tmp/cooper_primer_1
```
(b) cooper-mbp1:black cooper$ ls -lh /tmp/cooper_primer_1
total 0
drwxr-xr-x  21 cooper  wheel   672B May 10 08:48 attrs
drwxr-xr-x  14 cooper  wheel   448B May 10 08:48 flake8-bugbear
```

* Address mypy 3.6 type errors
- Don't use asyncio.run() ... go back to the past :P
- Refactor results into a named tuple of two dicts to avoid typing nightmare
- Fix some variable names
- Fix bug with rebase logic in git_checkout_or_rebase

* Prettier the JSON config file for primer

* Delete projects when finished, move dir to be timestamped + shallow copy

* Re-enable disabled projects post @JelleZijlstra's docstring fix

* Workaround for future annotations until someone tells me the correct fix | 1 |
| Document git's ignore-revs-file feature (#1419)

* Document git's ignore-revs-file feature

A long-standing counterargument against moving to automated code formatters
like Black is that the migration will clutter up the output of git blame.
This used to be a valid argument, but not anymore. Since git version 2.23,
git natively supports ignoring revisions in blame with the --ignore-rev
and --ignore-revs-file options.

This isn't documented in the Black documentation. It should be as it would
put old projects wanting to migrate to Black at ease since blame won't
be ruined.

* Add links so people can +1 on --ignore-revs-file support

* Fix wording

'Counterargument' was the wrong word since it means an objection to an objection. 'Argument' is the proper word I was looking for.

Co-authored-by: Cooper Lees <me@cooperlees.com> | 1 |
| Expand docs about slice formatting (#1418)

Black will apply no spaces around ':' operators for 'simple' expresssions
, but will apply extra space around ':' operators for 'complex' expressions.
Black treats anything more than variable names as 'complex', but this isn't
noted in the Black documentation. Which leads to a few issues on the GitHub
issue tracker that all report inconsistent spacing around ':' operators. | 1 |
| fix crashes on docstring whitespace changes (#1417)

Fixes #1415 | 1 |
| Fix Boolean values in pyproject.toml config (#1410)

Boolean values use lowercase identifiers in TOML.
https://github.com/toml-lang/toml#user-content-boolean | 1 |
| Remove no longer used appveyor (#1401) | 1 |
| Handle ImportError from multiprocessing module (#1400)

Termux's Python environment doesn't provide sem_open, but fails with a
nested `ImportError` on import attempts:

    ImportError: cannot import name 'SemLock' from '_multiprocessing'

This updates the existing handling for AWS Lambda to catch both
`OSError` and `ImportError`. | 1 |
| Add preference of parantheses over backslashes in docs (#1399)

Closes https://github.com/psf/black/issues/392

Revived from https://github.com/psf/black/pull/558

Relevant to https://github.com/psf/black/issues/664 | 1 |
| README/CHANGES: Fix links (#1397)

* Replace relative README links with absolutes, so they work on PyPI

* CHANGES: Fix Spyder web link | 1 |
| Add py.typed file. (#1395)

* Add py.typed file | 1 |
| Refactor black into packages in src/ dir (#1376)

- Move black.py to src/black/__init__.py
- Have setuptools_scm make src/_black_version.py and exclude from git
- Move blackd.py to src/blackd/__init__.py
- Move blib2to3/ to src/
- Update `setup.py`
- Update unittests to pass
  - Mostly path fixing + resolving
- Update CI
  - pre-commit config
  - appveyor + travis

Tested on my mac with python 3.7.5 via:
```
python3 -m venv /tmp/tb3
/tmp/tb3/bin/pip install --upgrade setuptools pip coverage pre-commit
/tmp/tb2/bin/pip install ~/repos/black/
cd ~/repos/black/
/tmp/tb2/bin/coverage run tests/test_black.py
/tmp/tb3/bin/pre-commit run -a
/tmp/tb3/bin/black --help
/tmp/tb3/bin/black ~/repos/ptr/ptr.py
``` | 1 |
| Adding documentation to the README for import errors in vim (#1317)

* Adding documentation to the README for import errors in vim

I had the same issue as psf/black#1148 and have been searching for a
solution to this. I realized that you cannot fix it by change anything
in the code, but by re-compiling the C extensions of regex and
typed-ast. Installing this packages from the tarballs solves the
problem.

* Fixing a bad copy&paste

Co-Authored-By: Hugo van Kemenade <hugovk@users.noreply.github.com>

* chore: changed made by pre-commit

* chore: better way of dealing with non-binary installs

* chore: adding vim cache files to the ignore list

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Add purcell/reformatter.el as an Emacs integration option (#1330) | 1 |
| close event loop for all tests (#1394) | 1 |
| Test on Python 3.9-dev (#1393)

* Test on Python 3.9-dev

* Python 3.9 still in alpha, allow to fail | 1 |
| Fix mono test | 1 |
| Improve error messages from BlackRunner | 1 |
| Update changelog and README (#1392)

* add two CHANGELOG entries

* update README on command-line options | 1 |
| Remove deprecated  --py36 option (#1236) | 1 |
| add --force-exclude argument (#1032)

Co-authored-by: Peter Yu <2057325+yukw777@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com> | 1 |
| skip mono test while im working on it | 1 |
| Update CHANGELOG with some PRs merged (#1391)

* Update CHANGELOG with some PRs merged
- Add section for vim plugin
- Add vim plugin now prefering virtualenv
- Add py3 tagged wheels

* packges

* Update CHANGES.md _Black_

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com>

* Update CHANGES.md Vim

Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com>
Co-authored-by: Jelle Zijlstra <jelle.zijlstra@gmail.com>
Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Add Black compatible configurations in documentation (#1366 & #1205) (#1371)

* Add Black compatible configs in docs ( #1366 & #1205)

When users of Black are starting a new project or are adding Black to their
existing projects, they will usually have to config their existing tools like
flake8 to be compatible with Black. Usually, these configs are the same across
different projects. Yet, there isn't any consolidated infomation on what configs
are compatible. Leaving users of Black to dig out any infomation they can find
in README.md.

* Fix a bad max-line-length value

* Clean up pylint's configs

* Add explanations for each configurations

Copying and pasting code without understanding it is a bad idea. This goes the same
for users copying and pasting configurations.

* Capitalize Pylint

* Link from the README

* Fix the isort configuration

I referenced the wrong source. I used a pesonal configuration, not a pure Black-
compatible configuration.

* Improve the config explanations

The explanation for the isort configuration was pretty bad. After having fixed the
configuration (see commit 01c55d1), improving the its explanation was necessary to
make it more user-friendly and understandable. Also added fenced code blocks of the
raw configuration options so the explanations make sense.

* Improve README wording slightly

* Add @hugovk, @JelleZijlstra + my suggestions

Co-authored-by: Cooper Lees <cooper@fb.com> | 1 |
| Include an easier way to integrate black with Pycharm (#1268)

* Include an easier way to integrate black with Pycharm | 1 |
| Make the use of a ThreadPoolExecutor explicit | 1 |
| better test for mono executor | 1 |
| Add docstrings to fmt checking functions, add to docs

Follow up from #1325

Adds docstrings to the fmt checking functions.
Renames fmt_on to is_fmt_on.
Adds the functions to the autodocs. | 1 |
| permits black to run in AWS Lambda: (#1141)

AWS Lambda and some other virtualized environment may not permit access
to /dev/shm on Linux and as such, trying to use ProcessPoolExecutor will
fail.

As using parallelism is only a 'nice to have' feature of black, if it fails
we gracefully fallback to a monoprocess implementation, which permits black
to finish normally.

Co-authored-by: Allan Simon <asimon@yolaw.fr> | 1 |
| Add Change Log project URL (#1382)

* Add Change Log project URL

Co-authored-by: Cooper Lees <me@cooperlees.com>
Co-authored-by: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Prefer virtualenv packages over global packages (#1383) (#1384) | 1 |
| Add option for printing a colored diff (#1266) | 1 |
| Remove deprecated use of 'setup.py test' (#1275)

Since setuptools v41.5.0 (27 Oct 2019), the 'test' command is formally
deprecated and should not be used. Now use unittest as the test entry
point. | 1 |
| Add Dropbox as a user of Black (#1335) | 1 |
| add bracket check in split_line (#1315) | 1 |
| Make sure sys._base_executable is sane in Vim plugin (#1380)

The `venv` module relies on `sys._base_executable` to determine the
Python executable to run, but with recent versions of Vim, this is set
to the `vim` executable. A possible workaround is to just override it,
since the `black` plugin already overrides `sys.executable` (possibly
for similar reasons?) anyway. | 1 |
| add change log entry for #1053 | 1 |
| Re-indent the contents of docstrings (#1053)

* Re-indent the contents of docstrings when indentation changes

Keeping the contents of docstrings completely unchanged when
re-indenting (from 2-space intents to 4, for example) can cause
incorrect docstring indentation:

```
class MyClass:
  """Multiline
  class docstring
  """

  def method(self):
    """Multiline
    method docstring
    """
    pass
```
...becomes:
```
class MyClass:
    """Multiline
  class docstring
  """

    def method(self):
        """Multiline
    method docstring
    """
        pass
```

This uses the PEP 257 algorithm for determining docstring indentation,
and adjusts the contents of docstrings to match their new indentation
after `black` is applied.

A small normalization is necessary to `assert_equivalent` because the
trees are technically no longer precisely equivalent -- some constant
strings have changed.  When comparing two ASTs, whitespace after
newlines within constant strings is thus folded into a single space.

Co-authored-by: Luka Zakrajšek <luka@bancek.net>

* Extract the inner `_v` method to decrease complexity

This reduces the cyclomatic complexity to a level that makes flake8 happy.

* Blacken blib2to3's docstring which had an over-indent

Co-authored-by: Luka Zakrajšek <luka@bancek.net>
Co-authored-by: Zsolt Dollenstein <zsol.zsol@gmail.com> | 1 |
| Add error on non-list target-version in config file (#1284) | 1 |
| Improve String Handling (#1132)

This pull request's main intention is to wraps long strings (as requested by #182); however, it also provides better string handling in general and, in doing so, closes the following issues:

Closes #26
Closes #182
Closes #933
Closes #1183
Closes #1243 | 1 |
| Move to 'py3' tagged wheels (#1388)

- This makes black wheels to be tagged as py3 default
- `python_requires=">=3.6"` ensures we are >= 3.6

Test:
```
python3 -m venv /tmp/build_black
/tmp/build_black/bin/pip install --upgrade pip setuptools wheel
/tmp/build_black/bin/python setup.py bdist_wheel
...
black-19.10b1.dev53+g81bf9bb.d20200508-py3-none-any.whl
```

Addresses #1385 | 1 |
| Fix for "# fmt: on" with decorators (#1325) | 1 |
| Issue 1144: Fix type annotations for --config option (#1166) | 1 |
| Bump bleach from 3.1.2 to 3.1.4 (#1326)

Bumps [bleach](https://github.com/mozilla/bleach) from 3.1.2 to 3.1.4.
- [Release notes](https://github.com/mozilla/bleach/releases)
- [Changelog](https://github.com/mozilla/bleach/blob/master/CHANGES)
- [Commits](https://github.com/mozilla/bleach/compare/v3.1.2...v3.1.4)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Change exit code to 2 when config file doesn't exist (#1361)

Fixes #1360, where an invalid config file causes a return/exit code of 1. This
change means this case is caught earlier, treated like any other bad
parameters, and results in an exit code of 2.

Co-authored-by: Toby Fleming <tobywf@users.noreply.github.com> | 1 |
| Fixing the documentation of how to install the vim plugin (#1318)

* fix: Fixing the documentation of how to install the vim plugin

Solves the installation problem that currently exist because
the current master branch is not stable. See psf/black#1304 for
more information.

* fix: fixing incorrect path | 1 |
| Update heredoc marker case to conform with vim patch 8.1.1723 (#1348) | 1 |
| Small nitpicks (#1340)

Co-authored-by: MomIsBestFriend <> | 1 |
| Fix --diff output when encountering EOF (#1328)

`split("\n")` includes a final empty element `""` if the final line
ends with `\n` (as it should for POSIX-compliant text files), which
then became an extra `"\n"`.

`splitlines()` solves that, but there's a caveat, as it will split
on other types of line breaks too (like `\r`), which may not be
desired.

Fixes #526. | 1 |
| Omit commit hash and date stamp from doc version (#1322)


This also removes the dependency on setuptools-scm while building the
docs.

Fixes #1104. | 1 |
| Add missing separator in README (#1320) | 1 |
| Fix readthedocs build (#1321)

* migrate to new rtd config format and pip

* no type field anymore

* use builtin re for docs | 1 |
| Bump bleach from 3.1.1 to 3.1.2 (#1313)

Bumps [bleach](https://github.com/mozilla/bleach) from 3.1.1 to 3.1.2.
- [Release notes](https://github.com/mozilla/bleach/releases)
- [Changelog](https://github.com/mozilla/bleach/blob/master/CHANGES)
- [Commits](https://github.com/mozilla/bleach/compare/v3.1.1...v3.1.2)

Signed-off-by: dependabot[bot] <support@github.com>

Co-authored-by: dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com> | 1 |
| Don't suggest using `sudo` with Docker

Thanks for the tip, @imomaliev. | 1 |
| Compress RUN statements into one to avoid layer proliferation

Thanks for the suggestion, @imomaliev. | 1 |
| Update dependencies | 1 |
| Fix typo in README (#1306) | 1 |
| Update wording and formatting (#1302) | 1 |
| Implement Black Version Gallery (#1294)

Closes #1290. | 1 |
| [README.md] Updated Mail Address - Abdur-Rahmaan Janhangeer (#1301) | 1 |
| Update the name of Mode in the reference docs, too | 1 |
| Rename FileMode into just Mode

The mode was never just about files to begin with.  There are no other modes in
Black, this can be the default one. | 1 |
| Document how to use format_str()

Closes #1064 | 1 |
| Introduce a section of docs about exceptions | 1 |
| Run prettier and fix whitespace on CHANGES.md (#1296) | 1 |
| Add @cooperlees to maintainers

Fixes #1295 | 1 |
| Tell people where Change Log went | 1 |
| Split out Change Log (#1117)

* Split out Change Log
- Move to CHANGES.md to allow bots to see changes
- MANIFEST.in already includes *.md so CHANGES.md will be included
- THis maintains format but the change log will now be after acknowledgements
- This also ensure this gets added to pypi.org via setup.py function | 1 |
| string prefixes: don't normalise capital R-strings (#1271)

Resolves #1244

Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| Notify users of missing Python lazily (#1210)

Currently this message shows up with no context prior to the start of
Vim. By changing this to a lazy message, the user will only be notified
of a problem with the Black plugin when they are attempting to use the
Black plugin.

Co-authored-by: Łukasz Langa <lukasz@langa.pl> | 1 |
| Teach the Vim plugin to respect pyproject.toml (issue 414) (#1273)

Creates two separate functions:

1) abspath_pyproject_toml: find the absolute path to pyproject.toml
2) parse_pyproject_toml: finds black-specific toml config

Co-authored-by: Samuel Roeca <samuel.roeca@gmail.com> | 1 |
| Simplify Line.contains_multiline_strings method (#1267) | 1 |
| Update README.md to include appropriate command to install Vim with Python 3 on macOS (#1247)

* Update README.md to include appropriate command to install Vim with Python 3 on macOS

* Run pre-commit hooks | 1 |
| Change error message to specify its origin. (#1240) | 1 |
| Support py38-style starred expressions in return statement (#1121) | 1 |
| Remove unused variables tokenprog, Token and PlainToken (#1137) | 1 |
| change pyproject.toml relative path to absolute path in README.md (#1152)

because in the readthedocs hosted version this pyproject.toml will route to readthedocs does not exist page | 1 |
| Add comment to flake8 configuration explaining line-length mismatch (#1206) | 1 |
| Use conditional case for diff reports (#1226)

When --diff flag is used, black will now use the
conditional case in the Report output: eg "would
be reformatted" | 1 |
| bump regex version, providing wheels (#1232)

Close #1112 | 1 |
| find_project_root: allow .git to be a file (#1217)

Fixes #1213 | 1 |
| Skip the broken version of regex (#1209) | 1 |
| This MANIFEST.in not needed with setuptools_scm (#1200) | 1 |
| Add Thonny-black-code-format plugin (#1195) | 1 |
| Add GitHub Actions badge to README.md (#1134) | 1 |
| Fix list literal example in README

The literal as written is going to be exploded because of the trailing comma. | 1 |
| Add Home Assistant to 'Used by' section (#1182)

See the following post: https://developers.home-assistant.io/blog/2019/07/31/black.html | 1 |
| Fix unstable formatting with some `# type: ignore`s (#1113)

`type: ignore` shouldn't block collapsing a line, since it will still
apply fine to the merged line. This prevents an issue where a reformat
causes it to shift lines and then be merged on a subsequent pass.

There is a downside to this, which is that it can cause a `type:
ignore` to apply to more code than was originally intended. There
might be a way to apply this in a more limited situation, but I'm not
sure what it is.

Fixes #1061. | 1 |
| Simple docs cleanup (#1168)

* Remove reference to format_int_string in docs

The function got dropped in 250ba7f04b300df284ba80cd4bb4122b45b41efb.

* Remove reference to is_python36 in docs

The function got removed in 36d3c516d3c09fc5f05c420900dd6b854e3c8bbd. | 1 |
| CI: Add Python 3.8 and lint to GitHub Actions (#1173)

* CI: Test Python 3.8 on GitHub Actions

* pre-commit autoupdate

* CI: Lint on GitHub Actions | 1 |
| Suggest `extend-ignore` over `ignore` for flake8 (#1165)

this option was introduced in flake8 3.7.x and is generally preferred over `ignore` (which unsets all default ignores) | 1 |
| Fix simple typo: intput -> input (#1146)

Fixes #1147 | 1 |
| Remove duplicate paragraph about blackd headers (#1124) | 1 |
| Support compilation with mypyc (#1009)

* Make most of blib2to3 directly typed and mypyc-compatible

This used a combination of retype and pytype's merge-pyi to do the
initial merges of the stubs, which then required manual tweaking to
make actually typecheck and work with mypyc.

Co-authored-by: Sanjit Kalapatapu <sanjitkal@gmail.com>
Co-authored-by: Michael J. Sullivan <sully@msully.net>

* Make black able to compile and run with mypyc

The changes made fall into a couple categories:
 * Fixing actual type mistakes that slip through the cracks
 * Working around a couple mypy bugs (the most annoying of which being
   that we need to add type annotations in a number of places where
   variables are initialized to None)

Co-authored-by: Sanjit Kalapatapu <sanjitkal@gmail.com>
Co-authored-by: Michael J. Sullivan <sully@msully.net> | 1 |
| replace broken rtfd pypi badge (#1120) | 1 |
| Switch from attrs to dataclasses (#1116)

The main motivation here is that mypyc is going to have custom support
for dataclasses but probably not attrs. | 1 |
| Remove Poetry metadata | 1 |
| Test Windows, macOS and Linux on GitHub Actions (#1085) | 1 |
| acks += llchan | 1 |
| Fix fmt on/off when multiple exist in leaf prefix (#1086)

The old behavior would detect the existence of a `# fmt: on` in a leaf
node's comment prefix and immediately mark the node as formatting-on,
even if a subsequent `# fmt: off` in the same comment prefix would turn
it back off. This change modifies that logic to track the state through
the entire prefix and take the final state.

Note that this does not fully solve on/off behavior, since any _comment_
lines between the off/on are still formatted. We may need to add
virtual leaf nodes to truly solve that. I will leave that for a separate
commit/PR.

Fixes #1005 | 1 |
| Always move the prefix out when wrapping with parentheses (#1103)

Fixes #1097 | 1 |
| Restore simple form of if statement | 1 |
| Simplify some code flow

Put empty lines after control flow changes. | 1 |
| Explicitly close .gitignore during processing | 1 |
| Remove unnecessary casts after pinning Mypy to >= 0.740 | 1 |
| Use early returns in `get_grammars()` to save an indentation level | 1 |
| Reword comment | 1 |
| line_length=1 to reduce churn (#1092) | 1 |
| Fix regression: unexpected parentheses around non-mathematical powers

This was caused by an overly liberal application of parentheses in #909 that
fixed #646.

Fixes #1041 | 1 |
| Add diff support to blackd (#969) | 1 |
| Upgrade typed-ast to 1.4.0 | 1 |
| Put missing contributors in the list (THANKS!) | 1 |
| Let readers know E203 and W503 are explained later | 1 |
| Put missing features and fixes in the change log | 1 |
| Docstring nit | 1 |
| fix crash with long type annotations (#1093) | 1 |
| coverage: omit tests/data (#1095)

Noticed that when it complains about falling coverage, it's sometimes because code in tests/data isn't executed. | 1 |
| Do not crash when failing to read an entry (#1090) | 1 |
| setup.py: rename _version.py to _black_version.py (#1089)

In #1082, _version.py was renamed to _black_version.py, but the
py_modules line in setup.py wasn't changed, which meant that when
installed from source, running it failed with something like:

```
Traceback (most recent call last):
  File "~/.pyenv/versions/3.6.5/bin/black", line 5, in <module>
    from black import patched_main
  File "~/.pyenv/versions/3.6.5/lib/python3.6/site-packages/black.py", line 55, in <module>
    from _black_version import version as __version__
ModuleNotFoundError: No module named '_black_version'
``` | 1 |
| s/_version.py/_black_version.py/ (#1082)

Some users are installing Black as a dependency in their project. Having
a _version.py in site-packages is asking for a conflict sooner or later.

Ideally we shouldn't require a separate version file at all, that's an
additional import we need to make. But I'll leave that bikeshedding for
a different time. | 1 |
| add gitignore support using pathspec (#878) | 1 |
| Automatic markdown and YAML formatting with Prettier (#874) | 1 |
| Restore all cursors, instead of only the current window (#978)

If we have the same buffer open in multiple windows/tabs, we'll only
restore the current window's cursor.

Iterate through all tabs and windows, and save/restore all cursor
positions of windows that contain our buffer.

Addendum to #433. | 1 |
| Do not load incompatible cache (#875) (#1034)

A black cache created in Python 3.8 throws an unhandled
ValueError in earlier versions. This is because 3.6 does
not recognize the pickle protocol used as default in 3.8.
Accordingly, this commit:

  - Fixes read_cache to return an empty cache instead.

  - Changes the pickle protocol to 4 as the highest protocol
    fully supported by black's supported Python versions. | 1 |
| Run pre-commit on Travis CI (#1081) | 1 |
| Revert "restore cursor to same line of code, not same line of buffer (#989)"

This reverts commit 65c5a0d9f180c4b36ea98917cb3b569f8e4f892f.

Edge cases were discovered on the pull request post merge. | 1 |
| Back out #850 (#1079)

Fixes #1042 (and probably #1044 which looks like the same thing).

The issue with the "obviously unnecessary" parentheses that #850 removed is that sometimes they're necessary to help Black fit something in one line. I didn't see an obvious solution that still removes the parens #850 was intended to remove, so let's back out this change for now in the interest of unblocking a release.

This PR also adds a test adapted from the failing example in #1042, so that if we try to reapply the #850 change we don't break the same case again. | 1 |
| fix CI (#1078) | 1 |
| Set correct return statement for `is_type_comment` function (#929) | 1 |
| Use better default venv dir when using neovim (#937) | 1 |
| Create new issue templates (#934)

* Create new issue templates

* style -> design

* Apply suggestions from code review

Co-Authored-By: Hugo van Kemenade <hugovk@users.noreply.github.com> | 1 |
| Update README.md (#906)

Add Kakoune integration instructions | 1 |
| Change how venv path is modified in vim plugin (#804)

- Check if black venv path is not already in `sys.path`
- Append (not insert) path so that black doesn't incorrectly import backports (e.g. `typing`)

Avoids this error if `typing` is present in venv:
```
Traceback (most recent call last):
  File "<string>", line 56, in <module>
  File "/home/josh/.virtualenvs/default/lib/python3.7/site-packages/black.py", line 19, in <module>
    from typing import (
  File "/home/josh/.virtualenvs/default/lib/python3.7/site-packages/typing.py", line 1356, in <module>
    class Callable(extra=collections_abc.Callable, metaclass=CallableMeta):
  File "/home/josh/.virtualenvs/default/lib/python3.7/site-packages/typing.py", line 1004, in __new__
    self._abc_registry = extra._abc_registry
AttributeError: type object 'Callable' has no attribute '_abc_registry'
``` | 1 |
| Add .svn to default exclusion list (#965) | 1 |
| Tweak collection literals to explode with trailing comma (#826) | 1 |
| add instructions to Readme for installing vim plugin using vim native package loading, and how to map a key to run it manually (#944) | 1 |
| restore cursor to same line of code, not same line of buffer (#989) | 1 |
| Blacken .py files in blib2to3 (#1011)

* Blacken .py files in blib2to3

This is in preparation for adding type annotations to blib2to3 in
order to compiling it with mypyc (#1009, which I can rebase on top of
this).

To enforce that it stays blackened, I just cargo-culted the existing
test code used for validating formatting. It feels pretty clunky now,
though, so I can abstract the common logic out into a helper if that
seems better. (But error messages might be less clear then?)

* Tidy up the tests | 1 |
| Test on Python 3.8 final (#1070) | 1 |
| Declare support for Python 3.8 (#1069) | 1 |
| Fix type: ignore line breaking when there is a destructuring assignment (#1065)

It turns out we also need to handle invisible *left* parens added at
the *start* of a line. Refactor `contains_unsplittable_type_ignore` to
handle this more cleanly. | 1 |
| Update Pipfile.lock (#1062) | 1 |
| Add .eggs to .gitignore (#1063)

I don't know what .eggs is but it keeps showing up when I work on black
so... | 1 |
| Add black version header to blackd responses (#1046) | 1 |
| Require regex version 2019.8 | 1 |
| #455 Fix bug with tricky unicode symbols (#1047)

* add test for special unicode symbol which usual re can not process correctly
add regex lib which supports unicode 12.1.0 standard
replace re usage in project in favor to regex

* #455 fix dependency | 1 |
| Used by: add pandas and Pillow (#1057) | 1 |
| Fix missed cases in the `# type: ignore` logic (#1059)

In #1040 I had convinced myself that the type ignore logic didn't
need anything like the ignored_ids from the type comment logic, but I
was wrong, and we do.

We hit these cases in practice a bunch. | 1 |
| Fix issue with type comments on lines with trailing commas (#1058)

The code introduced in #1027 to detect whether a type comment appeared
after a regular comment in a Line would spuriously misfire when a leaf
was in the comments dict but had an empty list of comments. This can
occur as an artifact of how comments on trailing commas are handled,
it seems.

(This was discovered trying to test black out on mypy.) | 1 |
| Don't break long lines when `type: ignore` is present (#1040)

Fixes #997. | 1 |
| Fix typechecking under mypy 0.730 (#1039)

mypy 0.730 fixed a bug involving nonexistent attributes accessed on
modules, which caused an error since COLONEQUAL never got added to
token.pyi. Add it. | 1 |
| fix environment for readthedocs | 1 |
| Bump dependencies | 1 |
| Switch from versioneer to setuptools-scm (#1008) | 1 |
| fix tests | 1 |
| Support PEP 572 in while statements (#1028)

Commit d8fa8df0526de9c0968e0a3568008f58eae45364 added support for
parsing and formatting PEP572, but it missed the posibility to add
PEP572 syntax in while statements. | 1 |
| Don't allow type comments to be merged behind regular comments (#1027)

Type comments only apply if they are the first comment on the line,
which means that allowing them to be pushed behind a regular comment
when joining lines is a semantic change (and, indeed, one that black
catches and fails on). | 1 |
| Print a separate message when there are no inputs given (#999)


Fixes #886. | 1 |
| Change variable in README according to the PEP8 (#1002)

* Change variable in README according to the PEP8
* Change variable in tests according to the PEP8 | 1 |
| Fix unstable formatting involving unwrapping multiple parentheses (#836) (#961)

This change also unwraps all unnecessary parentheses. | 1 |
| use versioneer to manage __version__ (#981) | 1 |
| [blackd] Support `py36`-style values in X-Python-Variant header (#979) | 1 |
| Reraise exception in `skip_if_exception` decorator | 1 |
| Fix async blackd tests which won't fail currently (#966) | 1 |
| Fix unstable format involving backslash + whitespace at beginning of file (#948) | 1 |
| Remove unnecessary if-statement in maybe_make_parens_invisible_in_atom (#964) | 1 |
| appease flake8... | 1 |
| skip tests touching aiohttp when known exception occurs | 1 |
| Support PEP-570 (positional only arguments) (#946)

Code using positional only arguments is considered >= 3.8 | 1 |
| Add support for walrus operator (#935)

* Parse `:=` properly
* never unwrap parenthesis around `:=`
* When checking for AST-equivalence, use `ast` instead of `typed-ast` when running on python >=3.8
* Assume code that uses `:=` is at least 3.8 | 1 |
| CONTRIBUTING.md - update Python version (#942) | 1 |
| Fix Travis CI badge (#939)

It should point to travis-ci.com instead of .org | 1 |
| Change repo name to psf/black in README (#938) | 1 |
| update Pipfile.lock to work with Py3.[78]

Note: had to pin `docutils==0.15` because of https://github.com/pypa/pipenv/issues/3865 | 1 |
| python/black -> psf/black (#936) | 1 |
| Hello github.com/psf! | 1 |
| Use nullcontext in case when lock is None. Shutdown pool after code formatting. (#928) | 1 |
| Fix typo (#916) | 1 |
| Force parentheses between unary op and binary power. (#909) | 1 |
| Fix docstring of schedule_formatting

Fixes #914. | 1 |
| Fix mypy errors. (#911) | 1 |
| Ignore broken E203 (#910)

See https://github.com/python/black/issues/565 | 1 |
| Add W503 to default flake8 ignore list (#894)

W503 and W504 are mutually exclusive, to do with splitting binary operators across lines. Black reformats code according to W504, putting the operator on the start of the newline, therefore W503 needs to be ignored in the suggested Flake8 config to use with Black. | 1 |
| Pin comment to single leaf in invisible parens (#872) | 1 |
| Fix trailing comma for function with one arg (#880) (#891)

Modified maybe_remove_trailing_comma to remove trailing commas for
typedarglists (in addition to arglists), and updated line split logic
to ensure that all lines in a function definition that contain only one
arg have a trailing comma. | 1 |
| Add Datadog to list of users (#876) | 1 |
| Add link to the pyproject.toml for setting up pre-commit hook (#885) | 1 |
| [blib2to3] Fixed a typo and removed an unused import. (#848) | 1 |
| fix some out-of-date docstrings; other cleanup (#865) | 1 |
| Document cache location configuration (#866) | 1 |
| Document the need to enter the virtual environment shell (#868) | 1 |
| Don't introduce quotes to f-string sub-expressions on string boundaries (#871) | 1 |
| bump Pipfile.lock | 1 |
| minor performance improvement (~2% speedup in unit tests) (#858) | 1 |
| Add doc clarifying that there is no blackd client (#859)

Resolves #854

The first sentence of this is pretty uncontroversial. (Though I wasn't
sure exactly where in the text to put it.)
I thought it would also be nice to show the `curl` test with a tiny
statement that actually reformats. | 1 |
| Remove happiness of error message (#852) | 1 |
| remove obviously unnecessary parentheses (#850)

Fixes #548 | 1 |
| Mention support for async generators | 1 |
| Change log wording and ordering | 1 |
| acks += bgw | 1 |
| Move tokenizer config onto grammar, rename flag

Based on the feedback in
https://github.com/python/black/pull/845#issuecomment-490622711

- Remove TokenizerConfig, and add a field to Grammar instead.
- Pass the Grammar to the tokenizer.
- Rename `ASYNC_IS_RESERVED_KEYWORD` to `ASYNC_KEYWORDS` and
  `ASYNC_IS_VALID_IDENTIFIER` to `ASYNC_IDENTIFIERS`. | 1 |
| Add support for always tokenizing async/await as keywords

Fixes #593

I looked into this bug with @ambv and @carljm, and we reached the
conclusion was that it's not possible for the tokenizer to determine if
async/await is a keyword inside all possible generators without breaking
the grammar for older versions of Python.

Instead, we introduce a new tokenizer mode for Python 3.7+ that will
cause all async/await instances to get parsed as a reserved keyword,
which should fix async/await inside generators. | 1 |
| acks += revfried | 1 |
| Mention fix for backslashes before standalone comments | 1 |
| Remove spurious prints | 1 |
| Use  to handle legacy async/await handling in assert_equivalent | 1 |
| Add PyCon talk link to README (#844) | 1 |
| Make --safe work for Python2.7 syntax, by using typed_ast for safe validation (#840) | 1 |
| Avoid unstable formatting when comment follows escaped newline. (#839). Fixes #767. | 1 |
| Minor README updates (#842)

* Header in sentence case, for consistency

* Black in italics | 1 |
| Mention Elpy

Fixes #689 | 1 |
| humility -= 1 | 1 |
| Use g:pymode_python-defined interpreter if defined and exists, otherwise use existing defaults (#666)

This is helpful when using custom-compiled interpreters, or alternative
Python interpreters in non-standard locations | 1 |
| don't run more than 61 workers on Windows (#838) | 1 |
| Describe how to add black to Wing IDE (#758) | 1 |
| Add `black -c "code"` (#761) | 1 |
| Remove deprecated license_file from setup.cfg (#825)

Starting with wheel 0.32.0 (2018-09-29), the "license_file" option is
deprecated.

https://wheel.readthedocs.io/en/stable/news.html

The wheel will continue to include LICENSE, it is now included
automatically:

https://wheel.readthedocs.io/en/stable/user_guide.html#including-license-files-in-the-generated-wheel-file | 1 |
| add to changelog | 1 |
| Add parentheses around tuple unpack assignment (#832)

Fixes #656 | 1 |
| Remove unnecessary parens around yield (#834) | 1 |
| Update calver version number (#835)

If released this month, it will be 19.5b0. | 1 |
| add to CHANGELOG | 1 |
| fix handling of comments in from imports (#829)

Fixes #671 | 1 |
| Wrap `loop.run_in_executor` up in `asyncio.ensure_future` for reliable cross-platform berhavior. (#679)

Closes #494

Task completion should also remove the task from `pending`.

Only replicates on some platforms. (eg. Can replicate on Python 3.7+, with either Windows or whatever default Linux distro Travis uses.) | 1 |
| ambv/black -> python/black (#819) | 1 |
| Fix B011 (#820)

Do not call assert False since python -O removes these calls. Instead callers should raise AssertionError(). | 1 |
| minor: remove wrong comment in .flake8 (#788)

This is there since the initial commit, which did not have a setup.cfg. | 1 |
| Split the TRAILING_COMMA feature (#763) | 1 |
| Terget version option kebab-style (#770) | 1 |
| fix vim plugin for 19.3b0 (#755) (#766) | 1 |
| redo grammar selection, add test (#765) | 1 |
| fix appveyor deploy section | 1 |
| Use new github token for appveyor release | 1 |
| add change log entry (#764) | 1 |
| fix incorrect call (#762) | 1 |
| Fix print() function on Python 2 (#754)

Fixes #752 | 1 |
| v19.3b0 | 1 |
| Add back --py36 as a deprecated option (#750)

This partially reverts commit 21ab37a5d92c866a289320cba7c4689df70b3342. | 1 |
| Mention tab comment fixes, extend tests | 1 |
| Mention atomic cache creation in the change log | 1 |
| Indicate that PyCharm instructions also work with IntelliJ (#681)

* Indicate that PyCharm instructions also work with IntelliJ

* Update README.md | 1 |
| Update README.md - Pycharm instructions not working for files path containing white spaces (#659) | 1 |
| Mention fix for #632 in the change log | 1 |
| Enhance the type comment patch | 1 |
| Fix PyCharm instructions in README (#701)

Without this change, PyCharm won't refresh the file in the editor after Black runs. | 1 |
| Fix PendingDeprecationWarning: Task.all_tasks() is deprecated, use asyncio.all_tasks() instead (#741) | 1 |
| Updates to the change log | 1 |
| Simplify the #606 patch

Thanks for the original patch to solve #509, @hauntsaninja. | 1 |
| Changes default logger used by blib2to3 Driver (#732)

... to stop it from spamming the log when black is used as a library in another
    python application.

When used indirectly by black the logger initiated in `driver.py` will emit
thousands of debug messages making the debug level of the root logger virtually
useless. By getting a named logger instead the verbosity of logging from this
module can easily be controlled by setting its log level.

Fixes #715 | 1 |
| Update Pipfile environment | 1 |
| Add pip-wheel-metadata/ to ignores | 1 |
| remove Python implementation-specific versions (#736) | 1 |
| Put cursor in last line if old position is invalid (#641) | 1 |
| remove --py36 (#724)

Fixes #703. | 1 |
| split long del statements into multiple lines (#698)

Fixes #693 | 1 |
| Fix example with well formated code (add missing comma) (#720) | 1 |
| Improve examples to use 88 chars line length (#677) (#714)

The examples were wrapping at less than 88 characters, which is not the
default for black. | 1 |
| add missing aiohttp dep (#699)

add missing aiohttp dep

Also mark 3.8 as allowed to fail for now; it will fail due to an aiohttp bug.

Fixes #690 | 1 |
| Add PyCharm setup step (#680) | 1 |
| Remove numeric underscore normalization (#696) | 1 |
| Add `--target-version` option to allow users to choose targeted Python versions (#618) | 1 |
| 'sudo: required' no longer required https://blog.travis-ci.com/2018-11-19-required-linux-infrastructure-migration (#694) | 1 |
| Properly close the code block in README (#695) | 1 |
| show how to exclude individual files in the exclude example (#663)

* show how to exclude individual files in the exclude example

* include comments in the regex | 1 |
| Update readthedocs.yml (#611)

I'm pretty sure the name shouldn't be 'jupyterhub' | 1 |
| Format pyi files correctly (#599) | 1 |
| Fix indent calculation with tabs when computing prefixes (#595)

Closes #262 | 1 |
| Fix location of expression.diff in the change notification message (#670) | 1 |
| chore: Fix noqa comment (#684)

Omitting the colon makes Flake8 ignore all errors, rather than the specific code. | 1 |
| Atomically write cache files (#674) | 1 |
| Turn off pre-commit's automatic parallelization for black (#675)

black internally uses multiprocessing for speed.  In pre-commit 1.13.0 this is automated by the framework itself however if both pre-commit and black are forking processes this is slower and hits race-conditions in `black`. | 1 |
| delete some dead code (#669)

dead code detected via [dead](https://github.com/asottile/dead)

- **`KEYWORDS`**: introduced (unreferenced) in e74117f172e29e8a980e2c9de929ad50d3769150
- **`FLOW_CONTROL`**: last referenced in e9a940d69e789ce8caf1f3c1ded786dc102df2fd

"clean" command:

```
dead --exclude '^(tests/data/|docs/conf.py|blib2to3/)' | grep -Ev '^(visit_.*|show|_stop_signal|lib2to3_unparse) '
``` | 1 |
| Add support for special comments in multiline functions (#642) | 1 |
| README.md: fix mailto link (#660) | 1 |
| Improve an error message when failed to load pyproject.toml (#653) | 1 |
| Fix multiprocessing support for Windows binary (#632)

* Fix multiprocessing support for Windows binary

The black and blackd binaries generated for Windows builds would fail on
reformatting multiple files due to a Windows-specific
multiprocessing issue. Fix by calling freeze_support() as
described in Python docs. | 1 |
| Add CORS support to blackd (#627)

See issue #622. Use aiohttp-cors to allow cross-origin requests to blackd,
and add a dependency on it to the pipfile. | 1 |
| Add .eggs to default exclusions (#629) | 1 |
| Silence expected stderr (#621)

* Silence expected stderr output during test

* Change based on PR comment | 1 |
| Reflect renaming of IPython notebook to Jupyter (#616) | 1 |
| Add url to pep 257 in readme (#615) | 1 |
| Refactor Travis (#614)

Fixes #305 

- Run separate jobs for mypy, self-formatting, flake8, and test runs.
- Don't run flake8 in 3.8 because it is broken (and we can't really expect flake8 to always keep up with 3.8 development).
- Fix unused variable in test | 1 |
| Improves performance on large commented logical lines (#606)

Fixes #509 | 1 |
| Fix two types to be Optional (#607) | 1 |
| remove unused variable (#604) | 1 |
| Update isort config to use_parentheses instead of combine_as_imports (#547)

The `combine_as_imports=True` modifies isort style as a side-effect and was not the intended purpose of the suggested change in #250. The problem was that isort was actually replacing the parens with backslash and using `combine_as_imports=True` happened to also produce the same result.

The actual setting should be `use_parentheses` as this tells isort to use parenthesis for line continuation instead of \ for lines over the allotted line length limit and matches precisely what black is outputting. | 1 |
| set entry to black (#553) | 1 |
| patch main to ensure click_patch() gets called (#572) | 1 |
| delete unused code (#588) | 1 |
| Typo (#561) | 1 |
| use blackrelease github user for uploading release artifacts | 1 |
| Link to Bugbear's documentation (#577) | 1 |
| Explicit # fmt: on/off indentation level (#554) | 1 |
| add --skip-numeric-underscore-normalization in readme (#537) | 1 |
| Require attrs >= 18.1.0 to work around ctypes failure in Vim

Fixes #116, #539 | 1 |
| v18.9b0 | 1 |
| Deploy windows binary (#422) | 1 |
| Remove whitespace at the beginning of the file

Fixes #399 | 1 |
| Deploy linux binary (#362) (#410) | 1 |
| Fix mangling pweave and Spyder IDE special comments

Fixes #532. | 1 |
| Make CHANGELOG more accurate | 1 |
| Move should_explode handling to bracket_split_build_line | 1 |
| Add trailing comma for single `as` imports, too | 1 |
| Refactor left_hand_split and right_hand_split to deduplicate line building logic | 1 |
| add blackd ignore pyproject (#536) | 1 |
| Add trailing comma when a single import doesn't fit on a line. (#504)

Fixes #250. | 1 |
| Add underscores to numeric literals with more than six digits (#529) | 1 |
| Add .nox directories to default exclude (#525)

[Nox](https://nox.readthedocs.io/) is similar to Tox. It creates a .nox directory that contains virtualenv for testing with different Python versions. | 1 |
| Uppercase digits in hex literals (#530) | 1 |
| Improve Poetry support | 1 |
| Update Poetry section in pyproject.toml (#490) | 1 |
| Fix documentation build | 1 |
| Move things around in change log for the latest version to sort in rough notability order | 1 |
| blackd: a HTTP server for blackening (#460) | 1 |
| fix unstable formatting when unpacking big tuples (#514)

* fix unstable formatting when unpacking big tuples

* add changelog entry | 1 |
| Update atom plugin link to point to the python-black plugin (#505) | 1 |
| Make sure `async for` is not broken up to separate lines (#503)

Fixes #372. | 1 |
| Add trove classifier for Python 3.7 support (#486)

Testing added in 3bdd42389128bbbe8b64a8e050563f09bff99979. | 1 |
| Prefer https:// links where available (#485) | 1 |
| Add build & dist directories to .gitignore (#487)

Generated when running the command "python3 setup.py bdist_wheel". | 1 |
| Include license file in the generated wheel package (#484)

The wheel package format supports including the license file. This is
done using the [metadata] section in the setup.cfg file. For additional
information on this feature, see:

https://wheel.readthedocs.io/en/stable/index.html#including-the-license-in-the-generated-wheel-file

Helps project comply with its own license:

> The above copyright notice and this permission notice shall be
> included in all copies or substantial portions of the Software. | 1 |
| Update pypi.python.org URL to pypi.org (#488)

For details on the new PyPI, see the blog post:

https://pythoninsider.blogspot.ca/2018/04/new-pypi-launched-legacy-pypi-shutting.html | 1 |
| Change my email in the README (#483)

Would prefer my personal email here. I realise it's still in the git log but c'est la vie. | 1 |
| ISSUE_TEMPLATE.md: Add mention of online formatter (#481)

People can try out https://black.now.sh/?version=master to test against master. That should make issue reporting easier.

See https://github.com/jpadilla/black-playground/issues/6#issuecomment-416088863. Thanks @jpadilla! | 1 |
| fix lint errors | 1 |
| add test case for preserving newlines from stdin | 1 |
| change some numeric behavior (#469) | 1 |
| add changelog entry for #468 | 1 |
| fix bracket match bug (#470)

* fix bracket match bug

* add missing test file | 1 |
| wrap atoms in invisible parens to split adjacent strings (#463) | 1 |
| fix misformatting of floats with leading zeros (#464) | 1 |
| Support parsing of async generators in non-async functions (#165)

This is a new syntax added in python3.7, so black can't verify that reformatting will not change the ast unless black itself is run with 3.7. We'll need to change the error message black gives in this case. @ambv any ideas?

Fixes #125. | 1 |
| autodetect Python 3.6 on the basis of underscores (#461) | 1 |
| Fix minor typos (#443) | 1 |
| committers += jelle | 1 |
| PyPI downloads badge | 1 |
| Nits around numeral normalization. | 1 |
| Put missing blank lines after return statements. | 1 |
| Make schedule_formatting logic less nested. | 1 |
| Simplify caching logic. | 1 |
| Update README with missing change log, etc. | 1 |
| not enforcing python3.6 for precommit hook (#430)

this should allow precommit hooks to be used with py37 | 1 |
| Remove mappings from Vim plugin. (#417)

They clashed with standard mappings.  Simplest just to let users define
their own.

Fixes #415 | 1 |
| added instructions for PyCharm File Watcher setup (#418)

* added instructions for PyCharm File Watcher setup

With these steps, PyCharm will run black on every file save.

* Update README.md | 1 |
| vim: Restore cursor/window position after format (#433)

Without this the cursor jumps to the top of the window after formatting
occurs. | 1 |
| Add playground link (#437) | 1 |
| Use atom-black plugin for Atom integration (#456) | 1 |
| write cache in --check mode (#453)

Fixes #448.

This diff makes us always write to the cache in normal mode, except
if the file is already in the cache, and it makes us write to the
cache in --check mode if the file is already well formatted.

I also fixed some related docstrings.

WriteBack.NO is now used only in tests. | 1 |
| normalize numeric literals (#454)

Fixes #452

I ended up making a couple of other normalizations to numeric literals
too (lowercase everything, don't allow leading or trailing . in floats,
remove redundant + sign in exponent). I don't care too much about those,
so I'm happy to change the behavior there.

For reference, here is Python's grammar for numeric literals:
https://docs.python.org/3/reference/lexical_analysis.html#numeric-literals | 1 |
| Look at actual parenthesis when generating ignored leafs.

Fixes #385 | 1 |
| update to mypy 0.620 and make tests pass again

Fixes #408 | 1 |
| pre-commit: use exclusion instead of ever-growing regex (#382) | 1 |
| Improve get_future_imports implementation.

Closes #389. | 1 |
| TravisCI: Test on production Python 3.7 and 3.8-dev (#393) | 1 |
| Suggest BufWritePre instead of BufWritePost for vi (#376)

closes #321 | 1 |
| 18.6b4 | 1 |
| Don't freeze when multiple comments directly precede # fmt: off

Fixes #371 | 1 |
| 18.6b3 | 1 |
| More tests for `# fmt: off`

Two more known limitations that I don't feel like solving now.  Probably very
low priority. | 1 |
| Trivial nits | 1 |
| Stop Click from crashing Black on invalid environments

Fixes #277 | 1 |
| Move INDENT value to the postponed prefix

This makes blib2to3's tree output valid again (which was broken by the previous
fiddling with INDENT and DEDENT nodes).

Fixes #334 | 1 |
| Use the separate pass for `# fmt: off` on all code

This removes the hacky exception-based handling that didn't work across
statement boundaries.

Fixes #335 | 1 |
| Support `# fmt: off/on` pairs within brackets

Fixes #329 | 1 |
| Update README with missing fixes in Change Log | 1 |
| Cache generated comments | 1 |
| Add travis badge and GitHub Fork banner to docs (#365) | 1 |
| Add pyls-black to README (#361) | 1 |
| Add blank line after constants in stub file (#360)

Fixes #340 | 1 |
| Add code snippet for using black badge in .rst (#356) | 1 |
| Harmonise with other instances (#347) | 1 |
| Ignore symbolic links pointing outside of the root directory (#339)

Fixes #338 | 1 |
| Remove reference to deprecated Visual Studio Code extension (#343) | 1 |
| Exclude profiling data when doing black . in this repo | 1 |
| Fix string normalization eating all backslashes above 3 | 1 |
| Add failing test data | 1 |
| Don't mark subtrees as changed that were already marked. | 1 |
| Cache child sibling lookups

Removes catastrophically quadratic behavior on nodes with very many siblings. | 1 |
| Make test_black.py work in profilers | 1 |
| Make `is_complex_subscript()` ignore list literals

This fixes catastrophically quadratic behavior on long lists. | 1 |
| Move profiling data out of tests/data | 1 |
| Fix string normalization sometimes producing invalid fstrings (#327) | 1 |
| Add .toml from tests to MANIFEST.in (#325)

Needed for `test_piping_diff()`. | 1 |
| 18.6b2 | 1 |
| Update README with missing Change Log entries | 1 |
| Return early from comment placement calculation on lines without comments | 1 |
| Add `-h` as a shortcut for `--help` (#316) | 1 |
| fix handling of empty triple quoted strings (#314) | 1 |
| Don't crash the Vim plugin

Fixes #312 | 1 |
| 2018 is not the year of Unicode on your desktop | 1 |
| Preliminary work on Poetry integration | 1 |
| Fix link | 1 |
| It works better when dependencies are installed. Who knew? | 1 |
| Trim TOC to fit in two lines again | 1 |
| Use `black .` now that we can | 1 |
| Support pyproject.toml

Fixes #65 | 1 |
| Move test data to data | 1 |
| Fix improper unmodified file caching when `-S` was used

This will also future-proof the cache to changes to FileMode. | 1 |
| Update beta link in docs | 1 |
| vim: add "--skip-string-normalization" support (#310)

Since 18.6b0 was released, there has been a new option to skip string
normalization when Black is called, but it wasn't able to be specified
from within the vim plugin. This commit adds that functionality.

Tested with g:black_skip_string_normalization set to 0 (off) and 1 (on). | 1 |
| Don't put a space after `*` in `g = 1, *"x"` (#309)

Fixes #305. | 1 |
| Change tests with stdin/out to exercise black.main (#307) | 1 |
| List the Python extension for VS Code as an editor integration (#308) | 1 |
| Link to GitHub + HTTPS + typos (#303)

* Link to GitHub, update 3.6 minor version

* http -> https

* Fix typos

* The Black style for Black, the project, is italics | 1 |
| correct email for Peter Bengtsson (#302) | 1 |
| acks += beterbe | 1 |
| 18.6b1 | 1 |
| ✨ 🍰 ✨ isn't appropriate when it fails, fixes #300 (#301) | 1 |
| Print report on stderr.\n\nFixes #299. | 1 |
| 18.6b0

Fixes #289 | 1 |
| Fix unnecessary parentheses when a line contains multiline strings

Fixes #232 | 1 |
| Fix long trivial assignments being wrapped in unnecessary parentheses

Fixes #273 | 1 |
| Fix handling of empty files | 1 |
| Consider stars in testlist_star_expr unpacking (because they are)

Fixes #297 | 1 |
| Properly format unified diff

Previously we weren't using timestamps. | 1 |
| Nits | 1 |
| Always show summary of reformatting | 1 |
| Make source handling use sets instead of lists

Also, sort cached file output to be (more) deterministic. | 1 |
| Make sure --verbose trumps --quiet

This is so that users can have a --quiet alias in their environment and only
occasionally add --verbose if they are surprised by the result. | 1 |
| Preserve line endings when formatting a file in place (#288) | 1 |
| Reformat docs/conf.py, too. | 1 |
| Fix missing leading slash due to `relative_to()` resolution | 1 |
| Add `--verbose` and report excluded paths in it, too

Fixes #283 | 1 |
| [trivial] Simplify `mode` and `write_back` calculation in main() | 1 |
| [trivial] Simplify stdin handling | 1 |
| Revert "don't run tests from /build"

This reverts commit 1687892d63fdff7525bb50a0166db3c5214ce2de.

This is no longer necessary with the fix in the previous commit. | 1 |
| Introduce "project root" as a concept

This is required for regular expressions in `--include=` and `--exclude=` not
to catch false positives from directories outside of the project. | 1 |
| Add .pie from tests to MANIFEST.in | 1 |
| `python_version` => `language_version` (#296)

Noticed this in `pytest`'s config -- `python_version` isn't a thing :D | 1 |
| don't run tests from /build | 1 |
| Skip symlink test if can't create one (#287) | 1 |
| Don't over-eagerly make a path absolute if only one passed

If a directory or more than one file is passed, Black nicely shows the relative
paths in output.  Before this change, it showed an absolute path if only
a single file was passed as an argument.  This fixes the inconsistency. | 1 |
| Make empty --include mean "anything goes", simplify `gen_python_files_in_dir` | 1 |
| Reorder command-line options | 1 |
| Sort default excludes, include the leading slash | 1 |
| Added --include and --exclude cli options (#281)

These 2 options allow you to pass in regular expressions that determine
whether files/directories are included or excluded in the recursive file
search.

Fixes #270 | 1 |
| acks += Stavros; document fix, add to Pipfile | 1 |
| Specify the minimum click version (#284) | 1 |
| Add --skip-string-normalization

Fixes #118 | 1 |
| Improve doc regarding PyCharm keyboard shortcut (#271) | 1 |
| Move setuptools and wheel to dev deps, upgrade them, too | 1 |
| 18.5b1 | 1 |
| Change minor whitespace in "Usage" | 1 |
| Refactor --pyi and --py36 into FileMode | 1 |
| Mention fix for #196 in the README | 1 |
| Clean up PEP 257 support

I documented the new behavior, added it to the change log, greatly expanded
tests, added support for inner defs and classes, and added Luka to ACKS.

Fixes #196 | 1 |
| Class new line between docstrings / vars / methods (#219)

Partially addresses #144 | 1 |
| Fix dangling file in documentation | 1 |
| Reword isort configuration, add --combine-as | 1 |
| Add isort args to README (#268) | 1 |
| Add instructions for running Black on save in Vim (#255) | 1 |
| Remove remains of extra empty lines for flow control statements | 1 |
| Reword --pyi and --py36 documentation | 1 |
| Update changelog for PR 249. | 1 |
| Add --pyi and --py36 flags (#249)

Fixes #244. | 1 |
| tweak grammar in docs about fluent interfaces (#247)

...to make the sentence a bit easier to understand. | 1 |
| Fix unstable formatting on trailers omitted from line splitting with comments

Fixes #238 | 1 |
| Fix invalid code on stars in long from-imports being wrapped in parentheses

Fixes #234 | 1 |
| Fix optional parentheses being removed within `# fmt: off` sections

Fixes #224 | 1 |
| Sentence case (#242) | 1 |
| Fix invalid code in an omitted trailer on large expressions

Fixes #237 | 1 |
| Mention fix for pickle files | 1 |
| Add navigation (#229) | 1 |
| README updates (#235)

* Consistent titles in 'Sentence case'
* Add console Markdown formatting
* Fix macOS typos
* Fix Homebrew typo | 1 |
| Store grammar pickle caches in CACHE_DIR

Fixes #192

Fixes #203 | 1 |
| Include blib2to3 LICENSE file (#230)

See: https://github.com/ambv/black/issues/226
Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| Remove grammar pickles from git (#225)

There is no need to keep the pickled grammar files in git. PR #203 will
move them into a user-specific cache directory any way.

See: https://github.com/ambv/black/issues/192
Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| Include stub files (*.pyi) (#222)

Fixes: https://github.com/ambv/black/issues/221
Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| 18.5b0 | 1 |
| Don't explode a one-element collection ending with a comma. | 1 |
| Fix overly optimistic removal of optional parentheses

The current behavior is explained with much detail in
`can_omit_invisible_parens`. | 1 |
| Don't allow indexing to trigger omitting optional parentheses | 1 |
| Fix consecutive string literals not forcing optional parentheses | 1 |
| Avoid treating ellipsis as a dot delimiter | 1 |
| Always explode data structure literals

Fixes #152 | 1 |
| Consistent empty lines in the README | 1 |
| Fix double colon (#216) | 1 |
| Fix multiline strings unnecessarily wrapped in optional parentheses

Fixes #215 | 1 |
| Explain automatic parentheses management better | 1 |
| Implement fluent interfaces

Fixes #67 | 1 |
| Consider `in`, `not in`, `is`, `is not` operators | 1 |
| For omitting optional parentheses, ignore delimiters of lower priorities | 1 |
| Report progress on multiple files incrementally | 1 |
| Link fix to issue | 1 |
| Don't use optional parentheses in unnecessary situations

If an expression starts or ends with a bracket and only contains a single
delimiter, don't wrap it in additional optional parentheses.  We can use the
brackets for the split.

Fixes #177

Fixes #193 | 1 |
| Simplify `is_trivial_*` methods | 1 |
| Document .pyi formatting | 1 |
| Warn that `right_hand_split()` modifies `bracket_depth` in leaves | 1 |
| Add support for pyi files (#210)

Fixes #207 | 1 |
| acks += miggaiowski | 1 |
| Don't explode trailers that fit in a single line | 1 |
| Re-use indexes of current iteration in `comments_after()` | 1 |
| acks += JelleZijlstra | 1 |
| Travis workaround script no longer necessary | 1 |
| Check for broken symlinks before checking file data (#202) | 1 |
| fix a spelling typo (#206) | 1 |
| Update Travis to use the default 3.7-dev binary | 1 |
| Document string prefix standardization | 1 |
| Adding Jupyter Notebook magic command (#200) | 1 |
| Remove u prefix if unicode_literals is present (#199) | 1 |
| Show badge for stable docs, not latest | 1 |
| Don't make parentheses invisible around yield expressions | 1 |
| Fix docstring of is_vararg | 1 |
| Clarify language in README | 1 |
| Automatic management of parentheses in `elif`, too | 1 |
| Support nested lambdas in BracketTracker | 1 |
| Automatic management of parentheses in assignments

Fixes #140

Note: this is an evolution but the end result needs to be different.  See
cantfit.py for some good examples on bad formatting caused by this change. | 1 |
| Fix docstrings of visit_stmt and normalize_invisible_parens | 1 |
| Delimit multiline expressions according to math operator priority

Fixes #148 | 1 |
| Discover whether a file is Python 3.6+ also by stars in calls

Fixes a pathological situation where if a function signature used a trailing
comma but was later reformatted to a single line (with the trailing comma
removed), Black would change its mind whether a file is Python
3.6-compatible between runs. | 1 |
| Addresses #174 Neovim Error (#197)

Neovim uses stdout for `msgpack` communication and the `subprocess` call for `virtualenv` was leaking that stream. Fix is to attach to a `subprocess.PIPE`. | 1 |
| Upgrade dependencies | 1 |
| Don't fail the entire right_hand_split if an optional split failed

Fixes splitting long import lines with only a single name. | 1 |
| Diff version | 1 |
| Make parentheses invisible recursively in atoms

This fixes non-deterministic formatting when multiple pairs of removable
parentheses are used.

Fixes #183 | 1 |
| Don't leave invalid trailing comma on imports

Fixes #185 | 1 |
| Update README (change log; acks += skapil; acks += tiran) | 1 |
| Formatting nits | 1 |
| Removing empty parentheses after class name (#180) | 1 |
| Output something when no files are reformatted (#190)

Just executing ``black`` without any argument does not print any message
to stdout or stderr. It's rather confusing, because the user doesn't
know what happened.

In ``len(sources) == 0`` case, black now prints ``No paths given. Nothing to
do``.

Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| Add more files/directories to .gitignore (#191)

Ignore .tox, black.egg-info and __pycache__ directories.

Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| More detailed isort configuration explanation | 1 |
| Remove unnecessary shebang lines (#189)

Since black.py is not marked as executable, the shebang in black.py serves
no purpose. black should be invoked through its entry point any way.

token.py is an internal module without a __name__ == '__main__' block or
other executable code. It contains just list of constants and small
helper functions.

Signed-off-by: Christian Heimes <christian@python.org> | 1 |
| Should this be  "_cede_ control" (#187) | 1 |
| Format subscriptions in a PEP-8 compliant way (#178)

Fixes #157 | 1 |
| fix type errors in setup.py (#179) | 1 |
| .gititnore += .vscode | 1 |
| 18.4a4 hotfix: don't populate the cache on --check

Fixes #175 | 1 |
| Reword inspiration

Fixes #167 | 1 |
| Add `explode_split` to documentation | 1 |
| 18.4a3 | 1 |
| Split imports like isort

Fixes #127

Partially addresses #152 | 1 |
| Do not enforce empty lines after control flow statements

Fixes #90 | 1 |
| Split ternary expressions

Fixes #141 | 1 |
| Make cache work with non-default line lenghts (#163) | 1 |
| Support sticky standalone comments (comments preceding defs, classes, and decorators)

Fixes #56
Fixes #154 | 1 |
| [#154] Handle comments between decorators properly (#166) | 1 |
| Add install instructions for Vim plugin (#131) | 1 |
| Improve change log message | 1 |
| Allow standalone comments to close code blocks

Fixes #16
Fixes #32 | 1 |
| Accelerate Unicode identifier support (backport from Lib/tokenize.py) | 1 |
| Remove nonsensical grammar from blib2to3 | 1 |
| Put the PSF license in blib2to3/ to mark that code. (#162)

The blib2to3/ code is PSF licensed as that is where the code originated.
This change just drops a proper copy of that license file into the
directory tree to make that clear. | 1 |
| Show full path on diffs

Fixes #130 | 1 |
| acks += csurfer | 1 |
| Refactor `reformat_one` and `schedule_formatting` to decrease state | 1 |
| Fix tests on windows (#159) | 1 |
| [#149] Make check and diff not mutually exclusive (#161)

Fixes #149. | 1 |
| Add AppVeyor for Windows builds | 1 |
| Move delimiter token skipping to BracketTracker

Also, added lambda argument delimiter skipping.

Fixes #133 | 1 |
| Skip handling signals on event loops that don't support it (#156) | 1 |
| fixed cache file location in readme (#150) | 1 |
| Remove dead code | 1 |
| Print exact Python version with build date | 1 |
| acks += ojii | 1 |
| Store pickles for 3.8.0a0 | 1 |
| Solve the Travis failure with 3.7 from deadsnakes | 1 |
| Update documentation

* Add "Ignore non-modified files" from the README
* Add missing functions to the reference | 1 |
| Docstring for `max_delimiter_priority_in_atom()` | 1 |
| Simplify single-file vs. multi-file modes | 1 |
| Revert `format_file_in_place()` and `format_stdin_to_stdout()` to return bools

`Changed.CACHED` is meaningless for those two functions. | 1 |
| Update 3.6.4 grammar pickle | 1 |
| Added caching (#136)

Black will cache already formatted files using their file size and
modification timestamp. The cache is per-user and will always be used
unless Black is used with --diff or with code provided via standard
input. | 1 |
| add sublack plugin for sublimetext (#137) | 1 |
| Merge pull request #138 from ambv/star-expr

Parse complex expressions in parameters after * and ** | 1 |
| Add changelog entry | 1 |
| use STARS instead of STAR | DOUBLESTAR | 1 |
| Parse complex expressions in parameters after * and ** | 1 |
| Generalize star expression handling

Fixes #132 | 1 |
| 18.4a2 | 1 |
| Handle unnecessarily escaped strings (#128) | 1 |
| Open temporary files with utf-8 encoding (#126)

This is not the default on Windows. | 1 |
| Consistent empty lines in Change Log | 1 |
| Advertise Windows support in Vim plugin | 1 |
| Make Vim plugin work on macOS/Linux again | 1 |
| Fix placement of dictionary unpacking inside dict literals

Fixes #111 | 1 |
| Remove debug print | 1 |
| Fix parsing of unaligned standalone comments

Fixes #99
Fixes #112 | 1 |
| Add windows support for black vim plugin (#123)

This is mostly a best effort support, and I only tested it on my
machine. | 1 |
| 18.4a1 | 1 |
| Don't omit escaping the second consecutive quote

This would produce invalid code for strings like `"x = ''; y = \"\""`. | 1 |
| Fix an embarrassing UnboundLocalError | 1 |
| Automatic parentheses management

Fixes #4 | 1 |
| team += zsol | 1 |
| Console formatting nits | 1 |
| Add support for all valid string literals (#115) | 1 |
| README: Add instructions for PyCharm (#81)

Instructions to add `black` to "External Tools" in PyCharm.

Adapted from https://kirankoduru.github.io/python/pylint-with-pycharm.html | 1 |
| Document that W503 is not compliant with PEP 8 (#114) | 1 |
| team += autophagy | 1 |
| Link VCS integration in documentation | 1 |
| [blib2to3] Support non-ASCII identifiers

This support isn't *exactly* right per PEP 3131 as the regex engine is a bit
too limited for that and I didn't want to spend time on Other_ID_Start and
Other_ID_Continue unless they're actually needed.

Hopefully this doesn't slow it down too much. | 1 |
| Handle arbitrary number of backslashes during string normalization (#110) | 1 |
| Simplify delimiter logic | 1 |
| acks += asottile | 1 |
| Update change log | 1 |
| Update `language-version` => `language_version` (#106) | 1 |
| Add a description for the pre-commit hook (#107)

This string appears on the hooks page on pre-commit.com. | 1 |
| Handle backslashes in raw strings while normalizing (#105)

In raw strings, a single backslash means a literal backslash. It is also used to escape quotes if it precedes them. This means it is impossible to change the quote type for strings that contain an unescaped version of the other quote type.
Fixes #100 | 1 |
| Add integration for pre-commit.com (#104)

Fixes #103 | 1 |
| acks += ikatanic | 1 |
| 3.6.5 grammar pickles | 1 |
| Fix --check for multiple files (#101) | 1 |
| Add --quiet

Fixes #78 | 1 |
| [blib2to3] Make the grammar pickles faster | 1 |
| 18.4a0 | 1 |
| acks += zsol | 1 |
| Ignore `# fmt: off` as inline comment

Black cannot currently support this form due to its generator-based nature.
This is mostly a problem for existing `# yapf: disable` usage as trailing
comment.

Fixes #95 | 1 |
| Don't insert trailing commas after standalone comments | 1 |
| Clarify why Black prefers double quotes | 1 |
| Improve test coverage a bit | 1 |
| Support --diff for both files and stdin

Fixes #87 | 1 |
| Describe how string literals are handled (#96) | 1 |
| Lines now break before all delimiters (#94)

The default behaviour is that now all lines break *before* delimiters,
instead of afterwards. The special cases for this are commas and
behaviour around args.

Resolves #73 | 1 |
| Normalize string quotes (#75)

* Normalize string quotes

Convert single-quoted strings to double-quoted. Convert triple single-quoted strings to triple double-quoted. Do not touch any strings where conversion would increase the number of backslashes.

Fixes #51.

* reformat Black itself | 1 |
| Document asyncio fixes | 1 |
| Graceful shutdown in case of cancellation | 1 |
| Merge pull request #89 from willingc/doc-conda

use conda for readthedocs | 1 |
| float python in doc build | 1 |
| Mention fix for #22 in changelog | 1 |
| More comments tests | 1 |
| Remove standalone comment hacks

Now Black properly splits standalone comments within bracketed expressions.
They are treated as another type of split instead of being bolted on with
whitespace prefixes.

A related fix: now multiple comments might appear after a given leaf.

Fixes #22 | 1 |
| use conda for rtd | 1 |
| Remove the test-specific .flake8 file | 1 |
| Fix --check with multiple files (#88)

Passing multiple files to --check would previously result in the report
being printed as if the files had been written to. | 1 |
| Use imperative language in all docstrings | 1 |
| Show __str__ in UnformattedLines | 1 |
| Add DebugVisitor.show() to documentation under utility functions | 1 |
| More minor documentation-related changes | 1 |
| Auto-generated documentation-related fixes | 1 |
| document classes, functions, exceptions (#82) | 1 |
| First stab at the Vim plugin! | 1 |
| Allow up to two empty lines on module level and single empty lines otherwise

Fixes #74 | 1 |
| It's obviously not just me, yo. Thanks y'all 🖤 | 1 |
| Don't crash and burn on empty lines with trailing whitespace

Fixes #80 | 1 |
| Big documentation deduplication

Most is not generated from README.md so we no longer have to remember to update
two Change Logs, and so on!

If we decide to diverge from the README in Sphinx, that's fine, too. We will
just create dedicated documents. | 1 |
| Add Emacs text editor integration to the README. (#79) | 1 |
| Improve pypi badge template | 1 |
| Self-host PyPI-related badges | 1 |
| Custom MIT license badge | 1 |
| Include .out file(s) in the distribution (#77)

> FileNotFoundError: [Errno 2] No such file or directory: '/home/user/pkg/build/black/src/black-18.3a4/tests/debug_visitor.out' | 1 |
| Any logo you like | 1 |
| Consistently style the name (#76) | 1 |
| 18.3a4 | 1 |
| Coverage reporting | 1 |
| The site is cleaner without the 'Related' cruft. | 1 |
| ReadTheDocs badge | 1 |
| Not actually using the Model T logo after all | 1 |
| Link to ReadTheDocs | 1 |
| Compress the logos better | 1 |
| More documentation fixes for ReadTheDocs | 1 |
| Documentation fixes for ReadTheDocs | 1 |
| Implement `# fmt: off` and `# fmt: on`

Fixes #5 | 1 |
| blib2to3: Never put prefixes on INDENT leaves either | 1 |
| Introduce DebugVisitor.show() + tests | 1 |
| add sphinx docs skeleton (#71) | 1 |
| Omit extra space in Sphinx auto-attribute comments

This feature of Sphinx is described in:
http://www.sphinx-doc.org/en/stable/ext/autodoc.html#directive-autoattribute

Fixes #68 | 1 |
| Properle space complex expressions in default values of typed arguments

Fixes #60 | 1 |
| Ignore typing error around Node/Leaf | 1 |
| Automatic detection of deprecated Python 2 forms of print and exec

Note: if those are handled, you can't use --safe because this check is using
Python 3.6+ builtin AST.

Fixes #49 | 1 |
| Only return exit code 1 when --check is used

Also, output less confusing messages in --check.

Fixes #50 | 1 |
| Mention delimiter_split() in CannotSplit docstring | 1 |
| Mention fix for #59 | 1 |
| Don't remove the single trailing comma from square bracket indexing

Fixes #59 | 1 |
| Badges. BADGES. BAAADDDGGGEEESSS!!! | 1 |
| Omit extra space in kwarg unpacking if it's an argument

Fixes #46 | 1 |
| Don't omit whitespace when the factor is not a math operator

Fixes #55 | 1 |
| Pin attrs to >=17.4.0 for @dataclass use

Fixes #54 | 1 |
| Mention how stdio handling works

Fixes #57 | 1 |
| Extra newlines in code examples | 1 |
| Twine 1.11.0 | 1 |
| 18.3a3 | 1 |
| Fix tests on 3.7 | 1 |
| Mention joslarson.black-vscode

Fixes #45 | 1 |
| Treat comments less magically | 1 |
| Support skipping AST printing on test failure | 1 |
| Don't write back stdin to stdout when --check is passed | 1 |
| Fix numpy-style array indexing for real

Fixes #33 | 1 |
| Don't remove single empty lines outside of bracketed expressions

Fixes #19 | 1 |
| Restore ability to format code with legacy usage of `async` as a name

Fixes #20
Fixes #42 | 1 |
| Update README with stdin information | 1 |
| Add piping from stdin to stdout with a - (#25)

Being able to format code by piping it through the formatter makes it much easier to integrate with tools like google/vim-codefmt or Chiel92/vim-autoformat. | 1 |
| More support for numpy tuple indexing | 1 |
| Update formatting example | 1 |
| 18.3a2 | 1 |
| Native README.md support on PyPI \o/

See: https://dustingram.com/articles/2018/03/16/markdown-descriptions-on-pypi | 1 |
| Set a 3.6+ python-tag for the wheel

Fixes #37 | 1 |
| Don't fold postscriptum standalone comment into last statement

This happened when the last statement was a simple statement.

Fixes #18
Fixes #28 | 1 |
| Consolidate empty line handling in EmptyLineTracker

Previously, extra newlines left on imports were handled sort of by accident.
Now it's all handled uniformly in one place. | 1 |
| Don't put four empty lines between top-level functions split by a comment | 1 |
| Describe fix for #21 in README | 1 |
| blib2to3: Never put prefixes on DEDENT leaves | 1 |
| Line breaks before logical operators (#36)

Fixes #21 | 1 |
| Use implicit defaults for auto_attribs

It reads much nicer. | 1 |
| Remove the trailing comma if there is only one argument to a call

This makes it consistent with removing the trailing comma when multiple
arguments to a call fit in a single line. It also makes it a tiny bit more
likely that an expression will fit a line that didn't use to. | 1 |
| Ignore empty bracket pairs while splitting

Fixes #35 | 1 |
| Add words | 1 |
| Add flake8 to CI, too | 1 |
| Bump version, update README with current fixes | 1 |
| Fix spurious space after star-based unary expression

This happened when the operand was a complex expression.

Fixes #31 | 1 |
| Also run mypy on test_black.py | 1 |
| Fix numpy-style array indexing

Fixes #33 | 1 |
| Clean up typing ignores, fix build | 1 |
| Add mypy to CI | 1 |
| Only use trailing commas in function signatures when it's safe

Trailing commas after * or ** in a function signature are only safe for Python 3.6
code.  So now Black checks whether the file was already Python 3.6 to begin
with.  If so, trailing commas are used in such cases.  Otherwise, they're not.

When * and ** don't appear in a function signature, the trailing comma is
always safe.

Fixes #8 | 1 |
| Don't split on for-loop variable unpacks

Fixes #23 | 1 |
| Fix tests after introducing --check | 1 |
| Add --check

Fixes #9 | 1 |
| Fixed malformed link to pathlib | 1 |
| Fix spurious space after unary expression

This happened when the operand was a complex expression.

Fixes #15 | 1 |
| Fix spurious extra spaces after opening parentheses and in default arguments

Fixes #14
Fixes #17 | 1 |
| Fix spurious space in parenthesized set expressions

Fixes #7 | 1 |
| Fix invalid spacing of dots in relative imports

Fixes #6
Fixes #13 | 1 |
| Add Python 3-only classifier

https://pypi.python.org/pypi?%3Aaction=list_classifiers | 1 |
| Use HTTPS | 1 |
| Mention *args and **kwargs backport | 1 |
| Update code formatting

!XXX doesn't render as code formatting, XXX does | 1 |
| testimonials += 1  # kennethreitz | 1 |
| Bummer, Python 3.8 builds don't work yet | 1 |
| Actually use the bundled Grammar.txt | 1 |
| Include Grammar.txt in the distribution | 1 |
| Initial commit | 1 |
## Committer Stats

-    372	Łukasz Langa <lukasz@langa.pl>
-    167	Jelle Zijlstra <jelle.zijlstra@gmail.com>
-    124	Richard Si <63936253+ichard26@users.noreply.github.com>
-     97	dependabot[bot] <49699333+dependabot[bot]@users.noreply.github.com>
-     79	Zsolt Dollenstein <zsol.zsol@gmail.com>
-     77	Shantanu <12621235+hauntsaninja@users.noreply.github.com>
-     54	Cooper Lees <me@cooperlees.com>
-     34	Hugo van Kemenade <hugovk@users.noreply.github.com>
-     32	Yilei "Dolee" Yang <yileiyang@google.com>
-     25	Felix Hildén <felix.hilden@gmail.com>
-     24	Batuhan Taskaya <isidentical@gmail.com>
-     20	Marco Edward Gorelli <marcogorelli@protonmail.com>
-     16	James Addison <jay@jp-hosting.net>
-     14	Bryan Bugyi <bryanbugyi34@gmail.com>
-     14	Hugo <hugovk@users.noreply.github.com>
-     14	cobalt <61329810+RedGuy12@users.noreply.github.com>
-     13	Cooper Lees <cooper@fb.com>
-     12	Michael J. Sullivan <sully@msully.net>
-     11	Jon Dufresne <jon.dufresne@gmail.com>
-     10	Henri Holopainen <henri.i.holopainen@gmail.com>
-      9	Anthony Sottile <asottile@umich.edu>
-      9	Joe Young <80432516+jpy-git@users.noreply.github.com>
-      8	Nipunn Koorapati <nipunn1313@gmail.com>
-      8	rdrll <13176405+rdrll@users.noreply.github.com>
-      7	Alex Waygood <Alex.Waygood@Gmail.com>
-      7	Carol Willing <carolcode@willingconsulting.com>
-      7	Sagi Shadur <saroad2@gmail.com>
-      7	pre-commit-ci[bot] <66853113+pre-commit-ci[bot]@users.noreply.github.com>
-      6	Antonio Ossa-Guerra <aaossa@uc.cl>
-      6	Christian Heimes <christian@python.org>
-      6	Joe Antonakakis <jma353@cornell.edu>
-      6	John Litborn <11260241+jakkdl@users.noreply.github.com>
-      6	Richard Si <sichard26@gmail.com>
-      6	Shivansh-007 <shivansh-007@outlook.com>
-      6	Yurii Karabas <1998uriyyo@gmail.com>
-      6	jgirardet <ijkl@netc.fr>
-      5	David Szotten <davidszotten@gmail.com>
-      5	Yilei Yang <yileiyang@google.com>
-      5	Zac Hatfield-Dodds <zac.hatfield.dodds@gmail.com>
-      5	jack1142 <6032823+jack1142@users.noreply.github.com>
-      4	Hadi Alqattan <alqattanhadizaki@gmail.com>
-      4	Mika⠙ <mail@autophagy.io>
-      4	Thomas Grainger <tagrain@gmail.com>
-      4	Yilei "Dolee" Yang <hi@mangoumbrella.com>
-      4	hauntsaninja <hauntsaninja@users.noreply.github.com>
-      3	Daniel Krzeminski <dankrzeminski32@gmail.com>
-      3	Henry Schreiner <HenrySchreinerIII@gmail.com>
-      3	Jonas Obrist <ojiidotch@gmail.com>
-      3	KotlinIsland <65446343+KotlinIsland@users.noreply.github.com>
-      3	Mariatta <Mariatta@users.noreply.github.com>
-      3	Mark Bell <MarkCBell@users.noreply.github.com>
-      3	Miro Hrončok <miro@hroncok.cz>
-      3	Ray Bell <rayjohnbell0@gmail.com>
-      3	Taneli Hukkinen <3275109+hukkin@users.noreply.github.com>
-      3	Tom Fryers <61272761+TomFryers@users.noreply.github.com>
-      3	Vishwas B Sharma <sharma.vishwas88@gmail.com>
-      3	Yngve Høiseth <yngve@hoiseth.net>
-      3	aru <genericusername414@gmail.com>
-      3	pszlazak <pszlazak@users.noreply.github.com>
-      3	shaoran <shaoran@sakuranohana.org>
-      2	Abdur-Rahmaan Janhangeer <cryptolabour@gmail.com>
-      2	Aneesh Agrawal <aneeshusa@gmail.com>
-      2	Asger Hautop Drewsen <asgerdrewsen@gmail.com>
-      2	Benjamin Woodruff <github@benjam.info>
-      2	Brandt Bucher <brandtbucher@gmail.com>
-      2	Bryan Forbes <bryan@reigndropsfall.net>
-      2	Carl Meyer <carl@oddbird.net>
-      2	Clément Robert <cr52@protonmail.com>
-      2	Cong <congusbongus@gmail.com>
-      2	Daniel M. Capella <polyzen@users.noreply.github.com>
-      2	Deepyaman Datta <deepyaman.datta@utexas.edu>
-      2	Greg Gandenberger <ggandenberger@shoprunner.com>
-      2	Gustavo Camargo <gcamargo1@users.noreply.github.com>
-      2	Holger Brunn <mail@hunki-enterprises.com>
-      2	Jakub Kuczys <me@jacken.men>
-      2	Jameel Al-Aziz <247849+jalaziz@users.noreply.github.com>
-      2	Jim Brännlund <BeyondEvil@users.noreply.github.com>
-      2	Josh Holland <anowlcalledjosh@gmail.com>
-      2	Kaleb Barrett <dev.ktbarrett@gmail.com>
-      2	Marc Mueller <30130371+cdce8p@users.noreply.github.com>
-      2	Matthew Armand <marmand68@gmail.com>
-      2	Matthew Clapp <itsayellow+dev@gmail.com>
-      2	Maximilian Cosmo Sitter <48606431+mcsitter@users.noreply.github.com>
-      2	Ofek Lev <ofekmeister@gmail.com>
-      2	Paul "TBBle" Hampson <Paul.Hampson@Pobox.com>
-      2	Peter Bengtsson <peterbe@mozilla.com>
-      2	Ran Benita <ran@unusedvar.com>
-      2	Shota Ray Imaki <shota.imaki@icloud.com>
-      2	Stijn de Gooijer <stijn@degooijer.io>
-      2	Ville Skyttä <ville.skytta@iki.fi>
-      2	Vlad Emelianov <volshebnyi@gmail.com>
-      2	johnthagen <johnthagen@users.noreply.github.com>
-      2	mainj12 <118842653+mainj12@users.noreply.github.com>
-      2	vezeli <37907135+vezeli@users.noreply.github.com>
-      1	Abdenour Madani <61651582+Ab2nour@users.noreply.github.com>
-      1	Abdullah Selek <abdullahselek@users.noreply.github.com>
-      1	Adam Johnson <me@adamj.eu>
-      1	Adam Williamson <adamw@happyassassin.net>
-      1	Aditya Garg <110886184+aditya7302@users.noreply.github.com>
-      1	Alex Vandiver <github@chmrr.net>
-      1	Alexander Huynh <git-46f1a0bd5592a2f9244ca321b129902a06b53e03@e.sc>
-      1	Alexander Huynh <github@grande.coffee>
-      1	Alexandr Artemyev <mogost@gmail.com>
-      1	Allan Simon <allan.simon@supinfo.com>
-      1	Alwyn Kik <alwyn@kik.pw>
-      1	Amethyst Reese <amy@n7.gg>
-      1	Anders-Petter Ljungquist <apljungquist@users.noreply.github.com>
-      1	Andrew Thorp <andrew.thorp.dev@gmail.com>
-      1	Andrew Zhou <andrewfzhou@gmail.com>
-      1	Andrey <dyuuus@yandex.ru>
-      1	Andy Freeland <andy@andyfreeland.net>
-      1	Aneesh Agrawal <aagrawal@lyft.com>
-      1	Aniket Patil <128228805+AniketP04@users.noreply.github.com>
-      1	Antek S <3324881+bluefish6@users.noreply.github.com>
-      1	Anupya Pamidimukkala <anupya@hotmail.ca>
-      1	Archit Gopal <73956153+Architrixs@users.noreply.github.com>
-      1	Arjaan Buijk <arjaan.buijk@gmail.com>
-      1	Arnav Borborah <arnavborborah11@gmail.com>
-      1	Art Chaidarun <art@duolingo.com>
-      1	Artem Malyshev <proofit404@gmail.com>
-      1	Ash <ashisbitt@icloud.com>
-      1	Augie Fackler <raf@durin42.com>
-      1	Austin Glaser <austin.glaser@gmail.com>
-      1	Austin Pray <71290498+austinpray-mixpanel@users.noreply.github.com>
-      1	Autophagy <mika@crate.io>
-      1	Aviskar KC <aviskarkc10@gmail.com>
-      1	Bartosz Sokorski <b.sokorski@gmail.com>
-      1	Bartosz Telenczuk <bartosz@telenczuk.pl>
-      1	Batuhan Taşkaya <47358913+isidentical@users.noreply.github.com>
-      1	Benjamin Wohlwend <bw@piquadrat.ch>
-      1	Bernát Gábor <gaborjbernat@gmail.com>
-      1	Bharat Raghunathan <bharatraghunthan9767@gmail.com>
-      1	Bibo-Joshi <hinrich.mahler@freenet.de>
-      1	Blandes22 <96037855+Blandes22@users.noreply.github.com>
-      1	Brandon J <153339574+veryslowcode@users.noreply.github.com>
-      1	Brett Cannon <brettcannon@users.noreply.github.com>
-      1	Bruno Oliveira <nicoddemus@gmail.com>
-      1	Bryan Bugyi <bryan.bugyi@rutgers.edu>
-      1	Bryce Willey <Bryce.Steven.Willey@gmail.com>
-      1	Calum Lind <calumlind@gmail.com>
-      1	Carl Meyer <carljm@instagram.com>
-      1	Casey Korver <84342833+Casey-Kiewit@users.noreply.github.com>
-      1	Casper Weiss Bang <c@cwb.dk>
-      1	Cha Gyuseok <cgs.zx6@gmail.com>
-      1	Charles <peacech@gmail.com>
-      1	Charles Patel <17268094+acharles7@users.noreply.github.com>
-      1	Charles Reid <53452777+chmreid@users.noreply.github.com>
-      1	Charlie Marsh <crmarsh416@gmail.com>
-      1	Charpy <nico_github@charpenel.org>
-      1	Chris Rose <offby1@offby1.net>
-      1	Christian Clauss <cclauss@me.com>
-      1	Christian Proud <christian.jay.proud.cic@gmail.com>
-      1	Chuck Wooters <ccwooters@gmail.com>
-      1	CiderMan <github.hills@spamgourmet.com>
-      1	Codey Oxley <coxley@users.noreply.github.com>
-      1	Cooper Ry Lees <me@cooperlees.com>
-      1	Corey Hickey <bugfood-c@fatooh.org>
-      1	Cristiano Salerno <119511125+csalerno-asml@users.noreply.github.com>
-      1	D. Ben Knoble <ben.knoble+github@gmail.com>
-      1	Daanyaal Syed <daanyaalasyed@gmail.com>
-      1	Dan Davison <dandavison7@gmail.com>
-      1	Daniel <61800298+ffe4@users.noreply.github.com>
-      1	Daniel Hahler <github@thequod.de>
-      1	Daniel Sparing <dsparing@google.com>
-      1	Daniele Esposti <expobrain@users.noreply.github.com>
-      1	Daniël van Noord <13665637+DanielNoord@users.noreply.github.com>
-      1	Dario Curreri <48800335+dariocurr@users.noreply.github.com>
-      1	David Culley <6276049+davidculley@users.noreply.github.com>
-      1	David Hotham <david.hotham@metaswitch.com>
-      1	David Lev <42866208+david-lev@users.noreply.github.com>
-      1	David Lukes <dafydd.lukes@gmail.com>
-      1	David W.H. Swenson <dwhs@hyperblazer.net>
-      1	Denis Laxalde <denis@laxalde.org>
-      1	Dimitri Merejkowsky <dmerejkowsky@users.noreply.github.com>
-      1	Douglas Thor <dougthor42@users.noreply.github.com>
-      1	Dragorn421 <Dragorn421@users.noreply.github.com>
-      1	Eddie Darling <darling@berkeley.edu>
-      1	Edouard Choinière <27212526+echoix@users.noreply.github.com>
-      1	Eero Vaher <eero.vaher@gmail.com>
-      1	Eli Treuherz <1574403+treuherz@users.noreply.github.com>
-      1	Emilv2 <emil.vanherp@hotmail.com>
-      1	Evan Chen <evan@evanchen.cc>
-      1	Felix Kohlgrüber <felix.kohlgrueber@gmail.com>
-      1	Fergus Mitchell <fergus.htm@gmail.com>
-      1	Florent Thiery <fthiery@gmail.com>
-      1	Francisco <35090042+Franccisco@users.noreply.github.com>
-      1	Frédérik Paradis <frederik.paradis.1@ulaval.ca>
-      1	Frédérik Paradis <fredy_14@live.fr>
-      1	Gabriel Perren <Gabriel-p@users.noreply.github.com>
-      1	Gerhard van Andel <10352022+GerhardOfRivia@users.noreply.github.com>
-      1	Giacomo Tagliabue <giacomo.tag@gmail.com>
-      1	Gregory P. Smith <greg@krypto.org>
-      1	Gunung P. Wibisono <55311527+gunungpw@users.noreply.github.com>
-      1	Gunung Pambudi Wibisono <55311527+gunungpw@users.noreply.github.com>
-      1	Hakan Çelik <hakancelik96@outlook.com>
-      1	Harish Rajagopal <harish.rajagopals@gmail.com>
-      1	Harutaka Kawamura <hkawamura0130@gmail.com>
-      1	Hassan Abouelela <abouelelahassan@gmail.com>
-      1	Heaford <dan@heaford.com>
-      1	Hong Minhee (洪 民憙) <hong@minhee.org>
-      1	Hongbo Miao <Hongbo.Miao@outlook.com>
-      1	Hugo Barrera <hugo@barrera.io>
-      1	Hynek Schlawack <hs@ox.cx>
-      1	Iain Dorrington <idorrington92@users.noreply.github.com>
-      1	Ikko Eltociear Ashimine <eltociear@gmail.com>
-      1	Ilia Lazarev <kephircheek@gmail.com>
-      1	Ionite <dev@ionite.io>
-      1	Isac Byeonghoon Yoo <bhyoo@bhyoo.com>
-      1	Ivan Katanić <ivan.katanic@gmail.com>
-      1	Jairo Llopis <Yajo@users.noreply.github.com>
-      1	Jake Anto <64896514+jake-anto@users.noreply.github.com>
-      1	Jakub Kadlubiec <jakub.kadlubiec@skyscanner.net>
-      1	Jakub Kuczys <6032823+jack1142@users.noreply.github.com>
-      1	Jakub Warczarek <jakub.warczarek@gmail.com>
-      1	James <50501825+Gobot1234@users.noreply.github.com>
-      1	James Braza <jamesbraza@gmail.com>
-      1	James Salvatore <james.c.salvatore.services@gmail.com>
-      1	Jan Hnátek <jan.hnatek@gmail.com>
-      1	Jan-Hendrik Müller <44469195+kolibril13@users.noreply.github.com>
-      1	Jason Fried <me@jasonfried.info>
-      1	Jason Friedland <jason@friedland.id.au>
-      1	Jason R. Coombs <jaraco@jaraco.com>
-      1	Jeffrey Lazar <jeff.p.lazar@gmail.com>
-      1	Jimmy Jia <tesrin@gmail.com>
-      1	JiriKr <33967184+JiriKr@users.noreply.github.com>
-      1	Joachim Jablon <ewjoachim@gmail.com>
-      1	John Meow <j0hn.meow@ya.ru>
-      1	Johnny.H <jnhyperion@gmail.com>
-      1	Jonas Haag <jonas@lophus.org>
-      1	Jonathan Berthias <jvberthias@gmail.com>
-      1	Jonty Wareing <jonty@jonty.co.uk>
-      1	Jordan Ephron <j@jephron.com>
-      1	Joseph Larson <larson.joseph@gmail.com>
-      1	Joseph Young <80432516+jpy-git@users.noreply.github.com>
-      1	Josh Bode <joshbode@fastmail.com>
-      1	Josh Owen <josh.owen@flourish.com>
-      1	Joshua Cannon <joshdcannon@gmail.com>
-      1	Joshua Cannon <joshua.cannon@ni.com>
-      1	José Padilla <jpadilla@webapplicate.com>
-      1	Juan Luis Cano Rodríguez <hello@juanlu.space>
-      1	Justin Prieto <density@users.noreply.github.com>
-      1	Kai Sforza <kai@kaictl.me>
-      1	Kaligule <Code@schauderbasis.de>
-      1	Katie McLaughlin <katie@glasnt.com>
-      1	Katrin Leinweber <9948149+katrinleinweber@users.noreply.github.com>
-      1	Keith Smiley <keithbsmiley@gmail.com>
-      1	Kenneth Schackart <schackartk1@gmail.com>
-      1	Kenyon Ralph <kenyon@kenyonralph.com>
-      1	Kevin Kirsche <Kev.Kirsche+GitHub@gmail.com>
-      1	Kevin Paulson <kevin.paulson@mindbridge.ai>
-      1	Kian Meng Ang <kianmeng.ang@gmail.com>
-      1	Kiyoon Kim <yoonkr33@gmail.com>
-      1	Kjell-Magnus <kmgrime@gmail.com>
-      1	Konstantin Alekseev <mail@kalekseev.com>
-      1	KotlinIsland <kotlinisland@users.noreply.github.com>
-      1	Kyle Sunden <sunden@wisc.edu>
-      1	Laurent Lyaudet <Laurent.Lyaudet@gmail.com>
-      1	Laurent Tréguier <laurent@treguier.org>
-      1	Lawrence Chan <llchan@users.noreply.github.com>
-      1	Lihu Ben-Ezri-Ravin <lbenezriravin@starry.com>
-      1	Linus Groh <mail@linusgroh.de>
-      1	Logan Hunt <39638017+dosisod@users.noreply.github.com>
-      1	LordOfPolls <22540825+LordOfPolls@users.noreply.github.com>
-      1	Loren Carvalho <comradeloren@gmail.com>
-      1	Luka Sterbic <luka.sterbic@gmail.com>
-      1	LukasDrude <mail@lukas-drude.de>
-      1	Maciej Olko <maciej.olko@affirm.com>
-      1	Mahmoud Hossam <mahmoudhossam@users.noreply.github.com>
-      1	Marco Edward Gorelli <33491632+MarcoGorelli@users.noreply.github.com>
-      1	Martin de La Gorce <martin.delagorce@gmail.com>
-      1	Mathieu Kniewallner <mathieu.kniewallner@gmail.com>
-      1	Matt VanEseltine <vaneseltine@gmail.com>
-      1	Matteo Bertucci <matteobertucci2004@gmail.com>
-      1	Matthew D. Scholefield <matthew331199@gmail.com>
-      1	Matthew Walster <matthew@walster.org>
-      1	Matthieu Simon <matthieu@bluegreen.sh>
-      1	Max Smolens <msmolens@users.noreply.github.com>
-      1	Michael Aquilina <michaelaquilina@gmail.com>
-      1	Michael Eliachevitch <m.eliachevitch@posteo.de>
-      1	Michael Flaxman <michael.flaxman@gmail.com>
-      1	Michael Marino <mmarino@gmail.com>
-      1	Michael McClimon <michael@mcclimon.org>
-      1	Michael Wilkinson <goi42@users.noreply.github.com>
-      1	Michal Siska <94260368+515k4@users.noreply.github.com>
-      1	Miguel Gaiowski <miggaiowskI@gmail.com>
-      1	Mike <roshi@fedoraproject.org>
-      1	Mike Taves <mwtoews@gmail.com>
-      1	Min ho Kim <minho42@gmail.com>
-      1	Miroslav Shubernetskiy <miroslav@miki725.com>
-      1	Mitch Negus <21086604+mitchnegus@users.noreply.github.com>
-      1	MomIsBestFriend <50263213+MomIsBestFriend@users.noreply.github.com>
-      1	Mr. Outis <mroutis@protonmail.com>
-      1	Nate Prewitt <nate.prewitt@gmail.com>
-      1	Nathan Goldbaum <ngoldbau@illinois.edu>
-      1	Nathan Hunt <neighthan.hunt@gmail.com>
-      1	Naveen <172697+naveensrinivasan@users.noreply.github.com>
-      1	Ned Twigg <ned.twigg@diffplug.com>
-      1	Ned Western <ned.western@gmail.com>
-      1	Neraste <neraste.herr10@gmail.com>
-      1	Nicola Soranzo <nicola.soranzo@gmail.com>
-      1	Nicolò Intrieri <81313286+n-borges@users.noreply.github.com>
-      1	Nikita Sobolev <mail@sobolevn.me>
-      1	Nikolaus Waxweiler <madigens@gmail.com>
-      1	Nimrod <87605179+Panther-12@users.noreply.github.com>
-      1	Noel Evans <noelevans@gmail.com>
-      1	Olexiy <alosha969@gmail.com>
-      1	Oliver Margetts <oliver.margetts@gmail.com>
-      1	Oliver Newman <15459200+orn688@users.noreply.github.com>
-      1	Osaetin Daniel <osaetindaniel@gmail.com>
-      1	Pablo Galindo <Pablogsal@gmail.com>
-      1	Panagiotis Vasilopoulos <hello@alwayslivid.com>
-      1	Paolo Melchiorre <paolo@melchiorre.org>
-      1	Paul Ganssle <p.ganssle@gmail.com>
-      1	Paul Meinhardt <mnhrdt@gmail.com>
-      1	Perry Vargas <perrybvargas@gmail.com>
-      1	Pete Grayson <github@jpgrayson.net>
-      1	Peter Mescalchin <peter@magnetikonline.com>
-      1	Peter Stensmyr <peter.stensmyr@gmail.com>
-      1	Peter Stensmyr <peter.stensmyr@iress.com>
-      1	PeterGrossmann <peter.grossmann@synoptics.de>
-      1	Pierre Sassoulas <pierre.sassoulas@gmail.com>
-      1	Pierre Verkest <pierreverkest84@gmail.com>
-      1	Pradeep Kumar <gohanpra@gmail.com>
-      1	Quentin Pradet <quentin@pradet.me>
-      1	QuentinSoubeyran <45202794+QuentinSoubeyran@users.noreply.github.com>
-      1	Ralf Schmitt <ralf@systemexit.de>
-      1	Renan Santos <renan.engmec@gmail.com>
-      1	Richard Fearn <richardfearn@gmail.com>
-      1	Rick Staa <rick.staa@outlook.com>
-      1	Rishav Kundu <rk@rishav.io>
-      1	Rishikesh Jha <rishijha424@gmail.com>
-      1	Riyazuddin Khan <riyaz489.rk@gmail.com>
-      1	Rob Hammond <13874373+RHammond2@users.noreply.github.com>
-      1	Roma <81729714+rtdev-com@users.noreply.github.com>
-      1	Romain Rigaux <romain.rigaux@gmail.com>
-      1	Rowan Rodrik van der Molen <rowan@ytec.nl>
-      1	Rowan Seymour <rowanseymour@gmail.com>
-      1	Rupert Bedford <rupert@rupertb.com>
-      1	Ruslan <7631314+ruslaniv@users.noreply.github.com>
-      1	Russell Davis <russelldavis@users.noreply.github.com>
-      1	Ryan McPartlan <ryanmcp45@gmail.com>
-      1	Ryan Siu <ryansiu@umich.edu>
-      1	Rémi Verschelde <rverschelde@gmail.com>
-      1	S. Co1 <sco1.git@gmail.com>
-      1	SADIK KUZU <sadikkuzu@hotmail.com>
-      1	Salomon Popp <hi@salomonpopp.me>
-      1	Sam Ezeh <sam.z.ezeh@gmail.com>
-      1	Sami Salonen <sakki@iki.fi>
-      1	Samson Umezulike <samson.ume@gmail.com>
-      1	Samuel Cormier-Iijima <samuel@cormier-iijima.com>
-      1	Sanket Dasgupta <sanketdasgupta@gmail.com>
-      1	Satyam Namdev <111422209+Spyrosigma@users.noreply.github.com>
-      1	Scott Stevenson <scott@stevenson.io>
-      1	Semen Zhydenko <simeon.zhidenko@gmail.com>
-      1	Sergey Vartanov <me@enzet.ru>
-      1	Seung Wan Yoo <74849806+wannieman98@users.noreply.github.com>
-      1	Shantanu <hauntsaninja@users.noreply.github.com>
-      1	Shinya Fujino <shf0811@gmail.com>
-      1	Shivam Singh <103785990+Shivam250702@users.noreply.github.com>
-      1	Shota Ray Imaki <shota.imaki.0801@gmail.com>
-      1	Shreya Agarwal <a.shreya202@gmail.com>
-      1	Simon <32608483+J-Exodus@users.noreply.github.com>
-      1	Simon Alinder <92031780+AlinderS@users.noreply.github.com>
-      1	Sorin Sbarnea <sorin.sbarnea@gmail.com>
-      1	Stavros Korokithakis <hi@stavros.io>
-      1	Stefaan Lippens <soxofaan@users.noreply.github.com>
-      1	Stefan Foulis <stefan@foulis.ch>
-      1	Stephen Rosen <sirosen@globus.org>
-      1	Steven M. Vascellaro <S.Vascellaro@gmail.com>
-      1	Steven Maude <StevenMaude@users.noreply.github.com>
-      1	Stian Jensen <me@stianj.com>
-      1	Surav Shrestha <148626286+shresthasurav@users.noreply.github.com>
-      1	Syed Mohammad Ibrahim <8592115+iamibi@users.noreply.github.com>
-      1	Sylvestre Ledru <sledru@mozilla.com>
-      1	Sébastien Eustace <sebastien.eustace@gmail.com>
-      1	Tal Amuyal <TalAmuyal@gmail.com>
-      1	Taneli Hukkinen <hukkinj1@users.noreply.github.com>
-      1	Tanvi Moharir <74228962+tanvimoharir@users.noreply.github.com>
-      1	Terrance <4025899+Terrance@users.noreply.github.com>
-      1	Thiago Bellini Ribeiro <hackedbellini@gmail.com>
-      1	Thom Lu <thomas.c.lu@gmail.com>
-      1	Thomas Hagebols <ThomasHagebols@users.noreply.github.com>
-      1	Théophile Bastian <contact@tobast.fr>
-      1	Tim Gates <tim.gates@iress.com>
-      1	Tim Swast <swast@google.com>
-      1	Timo <timo_tk@hotmail.com>
-      1	Toby Fleming <2903454+tobywf@users.noreply.github.com>
-      1	Tom Christie <tom@tomchristie.com>
-      1	Tom Saunders <tom-saunders@users.noreply.github.com>
-      1	Tomáš Jelínek <tojeline@redhat.com>
-      1	Tony Narlock <tony@git-pull.com>
-      1	Tristan Seligmann <mithrandi@mithrandi.net>
-      1	Troy Murray <troysmurray@gmail.com>
-      1	Tsuyoshi Hombashi <tsuyoshi.hombashi@gmail.com>
-      1	Tushar Chandra <tusharchandra2018@u.northwestern.edu>
-      1	Tushar Sadhwani <tushar.sadhwani000@gmail.com>
-      1	Tzu-ping Chung <uranusjr@gmail.com>
-      1	Utkarsh Gupta <utkarshgupta137@gmail.com>
-      1	Utsav Shah <ukshah2@illinois.edu>
-      1	Vadim Nikolaev <defntvdm@gmail.com>
-      1	VanSHOE <75690289+VanSHOE@users.noreply.github.com>
-      1	Victorien <65306057+Viicos@users.noreply.github.com>
-      1	Vincent Barbaresi <vincent.barbaresi@datadoghq.com>
-      1	Vinicius Gubiani Ferreira <vini.g.fer@gmail.com>
-      1	Vipul <finn02@disroot.org>
-      1	Vivek Vashist <vivekvashist@gmail.com>
-      1	WMOkiishi <w.muneo.o@gmail.com>
-      1	Wael Nasreddine <wael.nasreddine@gmail.com>
-      1	William Moreno <williamjmorenor@gmail.com>
-      1	Xuan (Sean) Hu <i+github@huxuan.org>
-      1	Yazdan <yzdann@users.noreply.github.com>
-      1	Yury V. Zaytsev <yury@shurup.com>
-      1	Yusuke Nishioka <yusuke.nishioka.0713@gmail.com>
-      1	Zac-HD <zac.hatfield.dodds@gmail.com>
-      1	brucearctor <5032356+brucearctor@users.noreply.github.com>
-      1	cbows <32486983+cbows@users.noreply.github.com>
-      1	cclauss <cclauss@bluewin.ch>
-      1	ceh <emil@hessman.se>
-      1	danieleades <33452915+danieleades@users.noreply.github.com>
-      1	dawn <78233879+dawnofmidnight@users.noreply.github.com>
-      1	dhaug-op <56020126+dhaug-op@users.noreply.github.com>
-      1	dylanjblack <38996120+dylanjblack@users.noreply.github.com>
-      1	emfdavid <84335963+emfdavid@users.noreply.github.com>
-      1	erykoff <erykoff@stanford.edu>
-      1	exag <hukuti145@gmail.com>
-      1	freddiewanah <freddie.wanah@gmail.com>
-      1	jlplenio <ubtown@gmail.com>
-      1	jmcb <joelsgp@protonmail.com>
-      1	jose nazario <jose.monkey.org@gmail.com>
-      1	jtpavlock <jtpavlock@gmail.com>
-      1	kaiix <kvn.hou@gmail.com>
-      1	konsti <konstin@mailbox.org>
-      1	kyle hausmann <kyle.hausmann@gmail.com>
-      1	laundmo <laurinschmidt2001@gmail.com>
-      1	mbarkhau <mbarkhau@gmail.com>
-      1	mihazagar <miha.zagar1@gmail.com>
-      1	mikehoyio <mikehoy@gmail.com>
-      1	nikkie <takuyafjp+develop@gmail.com>
-      1	nn <45516943+NNRepos@users.noreply.github.com>
-      1	oncomouse <oncomouse@gmail.com>
-      1	onescriptkid <onescriptkid@gmail.com>
-      1	otstrel <otstrel@gmail.com>
-      1	pmacosta <pmacosta@users.noreply.github.com>
-      1	programmer04 <jakub.warczarek@gmail.com>
-      1	rax <133822160+kotnen@users.noreply.github.com>
-      1	reka <382113+reka@users.noreply.github.com>
-      1	rht <rhtbot@protonmail.com>
-      1	sckarlin <github@karlin-online.com>
-      1	simaki <shota.imaki.0801@gmail.com>
-      1	skykasko <88055150+skykasko@users.noreply.github.com>
-      1	snlkapil <snlkapil@gmail.com>
-      1	sponsfreixes <sponsfreixes@users.noreply.github.com>
-      1	springstan <46536646+springstan@users.noreply.github.com>
-      1	sth <sth.dev@tejp.de>
-      1	temeddix <66480156+temeddix@users.noreply.github.com>
-      1	tpilewicz <31728184+tpilewicz@users.noreply.github.com>
-      1	treuherz <1574403+treuherz@users.noreply.github.com>
-      1	tungol <github@tungol.org>
-      1	utsav-dbx <49925333+utsav-dbx@users.noreply.github.com>
-      1	williamfzc <178894043@qq.com>
-      1	wouter bolsterlee <wouter@bolsterl.ee>
-      1	yoerg <73831825+yoerg@users.noreply.github.com>
## Branches

| Branch | Number of Commits |
| --- | --- |
| main | 1840 |
## Top 10 Modified Files:

| File | Modifications |
| --- | --- |
| 📃 CHANGES.md | 454 |
| 📃 README.md | 395 |
| 🐍 black.py | 330 |
| 🐍 tests/test_black.py | 259 |
| 🐍 src/black/__init__.py | 204 |
| 🐍 src/black/linegen.py | 86 |
| 🐍 setup.py | 79 |
| 🐍 src/black/mode.py | 71 |
| 📁 pyproject.toml | 71 |
| 📁 Pipfile.lock | 62 |
## Commit Frequency

| Date | Number of Commits |
| --- | --- |
| 08-05-2020 | 35 |
| 28-10-2019 | 19 |
| 15-03-2018 | 18 |
| 04-06-2018 | 17 |
| 14-03-2019 | 16 |
| 26-03-2018 | 14 |
| 17-08-2018 | 14 |
| 20-10-2019 | 12 |
| 04-04-2018 | 11 |
| 07-05-2019 | 11 |
| 20-03-2018 | 10 |
| 18-04-2018 | 10 |
| 23-04-2018 | 10 |
| 07-05-2018 | 10 |
| 08-05-2018 | 10 |
| 06-06-2018 | 10 |
| 26-09-2018 | 10 |
| 26-08-2020 | 10 |
| 04-02-2021 | 10 |
| 25-04-2021 | 10 |
| 16-03-2018 | 9 |
| 31-03-2018 | 9 |
| 11-04-2018 | 9 |
| 16-05-2018 | 9 |
| 29-05-2018 | 9 |
| 28-01-2022 | 9 |
| 17-03-2018 | 8 |
| 24-04-2018 | 8 |
| 09-06-2018 | 8 |
| 04-03-2020 | 8 |
| 04-05-2021 | 8 |
| 09-07-2023 | 8 |
| 23-10-2023 | 8 |
| 01-01-2024 | 8 |
| 25-01-2024 | 8 |
| 21-03-2018 | 7 |
| 22-03-2018 | 7 |
| 28-03-2018 | 7 |
| 15-05-2018 | 7 |
| 05-06-2018 | 7 |
| 07-06-2018 | 7 |
| 08-05-2019 | 7 |
| 21-10-2019 | 7 |
| 26-04-2021 | 7 |
| 10-09-2023 | 7 |
| 18-09-2023 | 7 |
| 18-11-2023 | 7 |
| 11-12-2023 | 7 |
| 24-03-2018 | 6 |
| 09-05-2018 | 6 |
| 17-05-2018 | 6 |
| 21-05-2018 | 6 |
| 31-05-2018 | 6 |
| 28-08-2018 | 6 |
| 09-05-2019 | 6 |
| 26-05-2019 | 6 |
| 18-09-2019 | 6 |
| 18-01-2020 | 6 |
| 27-04-2021 | 6 |
| 29-05-2021 | 6 |
| 09-06-2021 | 6 |
| 30-11-2021 | 6 |
| 21-01-2022 | 6 |
| 29-01-2022 | 6 |
| 20-12-2022 | 6 |
| 09-10-2023 | 6 |
| 14-03-2018 | 5 |
| 23-03-2018 | 5 |
| 29-03-2018 | 5 |
| 17-04-2018 | 5 |
| 19-06-2018 | 5 |
| 28-07-2019 | 5 |
| 21-08-2020 | 5 |
| 24-08-2020 | 5 |
| 01-04-2021 | 5 |
| 16-11-2021 | 5 |
| 21-12-2021 | 5 |
| 31-08-2022 | 5 |
| 11-07-2023 | 5 |
| 08-09-2023 | 5 |
| 11-09-2023 | 5 |
| 06-10-2023 | 5 |
| 16-10-2023 | 5 |
| 27-10-2023 | 5 |
| 07-11-2023 | 5 |
| 12-02-2024 | 5 |
| 09-04-2018 | 4 |
| 01-06-2018 | 4 |
| 18-06-2018 | 4 |
| 29-10-2018 | 4 |
| 17-05-2020 | 4 |
| 22-05-2020 | 4 |
| 20-08-2020 | 4 |
| 31-12-2020 | 4 |
| 22-02-2021 | 4 |
| 07-05-2021 | 4 |
| 08-05-2021 | 4 |
| 10-05-2021 | 4 |
| 16-05-2021 | 4 |
| 01-06-2021 | 4 |
| 16-07-2021 | 4 |
| 30-10-2021 | 4 |
| 31-10-2021 | 4 |
| 14-11-2021 | 4 |
| 05-12-2021 | 4 |
| 14-12-2021 | 4 |
| 07-01-2022 | 4 |
| 10-01-2022 | 4 |
| 22-01-2022 | 4 |
| 30-01-2022 | 4 |
| 23-02-2022 | 4 |
| 28-03-2022 | 4 |
| 11-04-2022 | 4 |
| 09-05-2022 | 4 |
| 14-07-2022 | 4 |
| 19-07-2022 | 4 |
| 25-09-2022 | 4 |
| 03-10-2022 | 4 |
| 10-12-2022 | 4 |
| 17-12-2022 | 4 |
| 28-03-2023 | 4 |
| 10-07-2023 | 4 |
| 16-07-2023 | 4 |
| 28-10-2023 | 4 |
| 27-01-2024 | 4 |
| 05-02-2024 | 4 |
| 15-03-2024 | 4 |
| 30-03-2018 | 3 |
| 05-04-2018 | 3 |
| 21-04-2018 | 3 |
| 14-05-2018 | 3 |
| 23-05-2018 | 3 |
| 08-06-2018 | 3 |
| 02-07-2018 | 3 |
| 23-08-2018 | 3 |
| 17-09-2018 | 3 |
| 18-09-2018 | 3 |
| 27-09-2018 | 3 |
| 13-11-2018 | 3 |
| 04-02-2019 | 3 |
| 06-02-2019 | 3 |
| 16-03-2019 | 3 |
| 02-05-2019 | 3 |
| 15-06-2019 | 3 |
| 24-07-2019 | 3 |
| 13-10-2019 | 3 |
| 14-10-2019 | 3 |
| 29-10-2019 | 3 |
| 03-03-2020 | 3 |
| 09-03-2020 | 3 |
| 17-03-2020 | 3 |
| 27-03-2020 | 3 |
| 09-05-2020 | 3 |
| 16-05-2020 | 3 |
| 21-05-2020 | 3 |
| 15-06-2020 | 3 |
| 12-08-2020 | 3 |
| 25-08-2020 | 3 |
| 05-09-2020 | 3 |
| 10-09-2020 | 3 |
| 19-10-2020 | 3 |
| 30-10-2020 | 3 |
| 22-04-2021 | 3 |
| 28-04-2021 | 3 |
| 09-05-2021 | 3 |
| 30-05-2021 | 3 |
| 03-06-2021 | 3 |
| 07-06-2021 | 3 |
| 10-06-2021 | 3 |
| 13-06-2021 | 3 |
| 28-08-2021 | 3 |
| 25-09-2021 | 3 |
| 21-10-2021 | 3 |
| 27-10-2021 | 3 |
| 15-11-2021 | 3 |
| 25-11-2021 | 3 |
| 01-12-2021 | 3 |
| 15-12-2021 | 3 |
| 13-01-2022 | 3 |
| 23-01-2022 | 3 |
| 26-01-2022 | 3 |
| 24-03-2022 | 3 |
| 30-03-2022 | 3 |
| 02-04-2022 | 3 |
| 09-04-2022 | 3 |
| 08-05-2022 | 3 |
| 11-06-2022 | 3 |
| 27-06-2022 | 3 |
| 26-07-2022 | 3 |
| 29-07-2022 | 3 |
| 13-08-2022 | 3 |
| 26-09-2022 | 3 |
| 28-09-2022 | 3 |
| 06-10-2022 | 3 |
| 08-12-2022 | 3 |
| 12-12-2022 | 3 |
| 22-01-2023 | 3 |
| 18-03-2023 | 3 |
| 19-03-2023 | 3 |
| 24-05-2023 | 3 |
| 12-06-2023 | 3 |
| 19-08-2023 | 3 |
| 01-10-2023 | 3 |
| 06-11-2023 | 3 |
| 08-11-2023 | 3 |
| 20-11-2023 | 3 |
| 09-12-2023 | 3 |
| 28-01-2024 | 3 |
| 29-01-2024 | 3 |
| 01-02-2024 | 3 |
| 01-04-2018 | 2 |
| 02-04-2018 | 2 |
| 12-04-2018 | 2 |
| 13-04-2018 | 2 |
| 03-05-2018 | 2 |
| 18-05-2018 | 2 |
| 19-05-2018 | 2 |
| 22-05-2018 | 2 |
| 28-05-2018 | 2 |
| 10-06-2018 | 2 |
| 13-06-2018 | 2 |
| 16-06-2018 | 2 |
| 21-06-2018 | 2 |
| 19-08-2018 | 2 |
| 20-08-2018 | 2 |
| 26-08-2018 | 2 |
| 25-09-2018 | 2 |
| 23-11-2018 | 2 |
| 29-11-2018 | 2 |
| 05-01-2019 | 2 |
| 18-01-2019 | 2 |
| 05-02-2019 | 2 |
| 07-03-2019 | 2 |
| 15-03-2019 | 2 |
| 06-05-2019 | 2 |
| 06-06-2019 | 2 |
| 25-06-2019 | 2 |
| 05-08-2019 | 2 |
| 21-08-2019 | 2 |
| 04-09-2019 | 2 |
| 10-10-2019 | 2 |
| 15-10-2019 | 2 |
| 27-10-2019 | 2 |
| 30-10-2019 | 2 |
| 22-11-2019 | 2 |
| 24-05-2020 | 2 |
| 01-06-2020 | 2 |
| 24-06-2020 | 2 |
| 06-07-2020 | 2 |
| 15-07-2020 | 2 |
| 16-07-2020 | 2 |
| 13-08-2020 | 2 |
| 14-08-2020 | 2 |
| 31-08-2020 | 2 |
| 01-09-2020 | 2 |
| 06-09-2020 | 2 |
| 27-09-2020 | 2 |
| 28-09-2020 | 2 |
| 27-10-2020 | 2 |
| 31-10-2020 | 2 |
| 13-11-2020 | 2 |
| 28-12-2020 | 2 |
| 27-12-2020 | 2 |
| 15-01-2021 | 2 |
| 09-02-2021 | 2 |
| 10-02-2021 | 2 |
| 14-02-2021 | 2 |
| 15-02-2021 | 2 |
| 01-03-2021 | 2 |
| 04-03-2021 | 2 |
| 05-03-2021 | 2 |
| 06-03-2021 | 2 |
| 29-03-2021 | 2 |
| 11-04-2021 | 2 |
| 12-04-2021 | 2 |
| 01-05-2021 | 2 |
| 06-05-2021 | 2 |
| 11-05-2021 | 2 |
| 12-05-2021 | 2 |
| 13-05-2021 | 2 |
| 17-05-2021 | 2 |
| 24-05-2021 | 2 |
| 25-05-2021 | 2 |
| 26-05-2021 | 2 |
| 31-05-2021 | 2 |
| 12-06-2021 | 2 |
| 22-06-2021 | 2 |
| 12-07-2021 | 2 |
| 24-07-2021 | 2 |
| 06-08-2021 | 2 |
| 22-08-2021 | 2 |
| 24-08-2021 | 2 |
| 25-08-2021 | 2 |
| 29-08-2021 | 2 |
| 19-09-2021 | 2 |
| 29-09-2021 | 2 |
| 11-11-2021 | 2 |
| 12-11-2021 | 2 |
| 17-11-2021 | 2 |
| 29-11-2021 | 2 |
| 02-12-2021 | 2 |
| 04-12-2021 | 2 |
| 16-12-2021 | 2 |
| 20-12-2021 | 2 |
| 25-12-2021 | 2 |
| 30-12-2021 | 2 |
| 11-01-2022 | 2 |
| 14-01-2022 | 2 |
| 17-01-2022 | 2 |
| 20-01-2022 | 2 |
| 24-01-2022 | 2 |
| 02-02-2022 | 2 |
| 08-02-2022 | 2 |
| 11-02-2022 | 2 |
| 20-02-2022 | 2 |
| 04-03-2022 | 2 |
| 07-03-2022 | 2 |
| 08-03-2022 | 2 |
| 16-03-2022 | 2 |
| 21-03-2022 | 2 |
| 26-03-2022 | 2 |
| 03-04-2022 | 2 |
| 06-04-2022 | 2 |
| 28-04-2022 | 2 |
| 26-05-2022 | 2 |
| 13-07-2022 | 2 |
| 02-08-2022 | 2 |
| 12-08-2022 | 2 |
| 22-08-2022 | 2 |
| 26-08-2022 | 2 |
| 30-08-2022 | 2 |
| 15-09-2022 | 2 |
| 22-09-2022 | 2 |
| 23-09-2022 | 2 |
| 24-10-2022 | 2 |
| 25-10-2022 | 2 |
| 26-10-2022 | 2 |
| 27-10-2022 | 2 |
| 31-10-2022 | 2 |
| 15-12-2022 | 2 |
| 26-12-2022 | 2 |
| 29-12-2022 | 2 |
| 18-01-2023 | 2 |
| 30-01-2023 | 2 |
| 31-01-2023 | 2 |
| 04-02-2023 | 2 |
| 17-03-2023 | 2 |
| 20-03-2023 | 2 |
| 03-04-2023 | 2 |
| 03-05-2023 | 2 |
| 15-05-2023 | 2 |
| 19-05-2023 | 2 |
| 23-06-2023 | 2 |
| 24-06-2023 | 2 |
| 04-07-2023 | 2 |
| 18-07-2023 | 2 |
| 22-07-2023 | 2 |
| 27-07-2023 | 2 |
| 03-08-2023 | 2 |
| 09-09-2023 | 2 |
| 28-09-2023 | 2 |
| 17-10-2023 | 2 |
| 25-10-2023 | 2 |
| 29-10-2023 | 2 |
| 31-10-2023 | 2 |
| 17-11-2023 | 2 |
| 19-11-2023 | 2 |
| 24-11-2023 | 2 |
| 04-12-2023 | 2 |
| 07-12-2023 | 2 |
| 22-12-2023 | 2 |
| 28-12-2023 | 2 |
| 27-12-2023 | 2 |
| 19-01-2024 | 2 |
| 22-01-2024 | 2 |
| 28-02-2024 | 2 |
| 13-03-2024 | 2 |
| 22-03-2024 | 2 |
| 19-03-2018 | 1 |
| 27-03-2018 | 1 |
| 03-04-2018 | 1 |
| 06-04-2018 | 1 |
| 16-04-2018 | 1 |
| 19-04-2018 | 1 |
| 22-04-2018 | 1 |
| 25-04-2018 | 1 |
| 26-04-2018 | 1 |
| 27-04-2018 | 1 |
| 28-04-2018 | 1 |
| 29-04-2018 | 1 |
| 30-04-2018 | 1 |
| 04-05-2018 | 1 |
| 10-05-2018 | 1 |
| 12-05-2018 | 1 |
| 20-05-2018 | 1 |
| 24-05-2018 | 1 |
| 26-05-2018 | 1 |
| 30-05-2018 | 1 |
| 03-06-2018 | 1 |
| 12-06-2018 | 1 |
| 15-06-2018 | 1 |
| 20-06-2018 | 1 |
| 23-06-2018 | 1 |
| 09-07-2018 | 1 |
| 18-07-2018 | 1 |
| 22-07-2018 | 1 |
| 18-08-2018 | 1 |
| 21-08-2018 | 1 |
| 27-08-2018 | 1 |
| 08-09-2018 | 1 |
| 10-09-2018 | 1 |
| 28-09-2018 | 1 |
| 09-10-2018 | 1 |
| 19-10-2018 | 1 |
| 27-10-2018 | 1 |
| 08-11-2018 | 1 |
| 20-11-2018 | 1 |
| 10-12-2018 | 1 |
| 15-12-2018 | 1 |
| 31-12-2018 | 1 |
| 12-01-2019 | 1 |
| 29-01-2019 | 1 |
| 07-02-2019 | 1 |
| 13-02-2019 | 1 |
| 14-02-2019 | 1 |
| 15-02-2019 | 1 |
| 20-02-2019 | 1 |
| 22-02-2019 | 1 |
| 24-02-2019 | 1 |
| 17-03-2019 | 1 |
| 20-03-2019 | 1 |
| 25-03-2019 | 1 |
| 05-05-2019 | 1 |
| 15-05-2019 | 1 |
| 16-05-2019 | 1 |
| 20-05-2019 | 1 |
| 21-05-2019 | 1 |
| 16-06-2019 | 1 |
| 28-06-2019 | 1 |
| 29-06-2019 | 1 |
| 02-07-2019 | 1 |
| 16-07-2019 | 1 |
| 22-07-2019 | 1 |
| 23-07-2019 | 1 |
| 25-07-2019 | 1 |
| 03-08-2019 | 1 |
| 04-08-2019 | 1 |
| 13-08-2019 | 1 |
| 23-08-2019 | 1 |
| 17-09-2019 | 1 |
| 01-10-2019 | 1 |
| 02-10-2019 | 1 |
| 11-10-2019 | 1 |
| 24-10-2019 | 1 |
| 25-10-2019 | 1 |
| 31-10-2019 | 1 |
| 08-11-2019 | 1 |
| 23-11-2019 | 1 |
| 25-11-2019 | 1 |
| 29-11-2019 | 1 |
| 30-11-2019 | 1 |
| 04-12-2019 | 1 |
| 08-12-2019 | 1 |
| 11-12-2019 | 1 |
| 16-12-2019 | 1 |
| 02-01-2020 | 1 |
| 23-01-2020 | 1 |
| 26-01-2020 | 1 |
| 10-02-2020 | 1 |
| 12-03-2020 | 1 |
| 29-03-2020 | 1 |
| 05-04-2020 | 1 |
| 12-04-2020 | 1 |
| 17-04-2020 | 1 |
| 24-04-2020 | 1 |
| 30-04-2020 | 1 |
| 13-05-2020 | 1 |
| 15-05-2020 | 1 |
| 18-05-2020 | 1 |
| 20-05-2020 | 1 |
| 23-05-2020 | 1 |
| 26-05-2020 | 1 |
| 03-06-2020 | 1 |
| 17-06-2020 | 1 |
| 16-06-2020 | 1 |
| 18-06-2020 | 1 |
| 20-06-2020 | 1 |
| 01-07-2020 | 1 |
| 08-07-2020 | 1 |
| 11-07-2020 | 1 |
| 13-07-2020 | 1 |
| 22-07-2020 | 1 |
| 01-08-2020 | 1 |
| 17-08-2020 | 1 |
| 18-08-2020 | 1 |
| 27-08-2020 | 1 |
| 28-08-2020 | 1 |
| 04-09-2020 | 1 |
| 08-09-2020 | 1 |
| 13-09-2020 | 1 |
| 19-09-2020 | 1 |
| 02-10-2020 | 1 |
| 07-10-2020 | 1 |
| 09-10-2020 | 1 |
| 12-10-2020 | 1 |
| 18-10-2020 | 1 |
| 01-11-2020 | 1 |
| 06-11-2020 | 1 |
| 09-11-2020 | 1 |
| 24-11-2020 | 1 |
| 25-11-2020 | 1 |
| 09-12-2020 | 1 |
| 13-12-2020 | 1 |
| 02-01-2021 | 1 |
| 03-01-2021 | 1 |
| 08-01-2021 | 1 |
| 09-01-2021 | 1 |
| 12-01-2021 | 1 |
| 11-01-2021 | 1 |
| 13-01-2021 | 1 |
| 16-01-2021 | 1 |
| 17-01-2021 | 1 |
| 27-01-2021 | 1 |
| 01-02-2021 | 1 |
| 02-02-2021 | 1 |
| 11-02-2021 | 1 |
| 16-02-2021 | 1 |
| 20-02-2021 | 1 |
| 24-02-2021 | 1 |
| 28-02-2021 | 1 |
| 02-03-2021 | 1 |
| 08-03-2021 | 1 |
| 16-03-2021 | 1 |
| 18-03-2021 | 1 |
| 20-03-2021 | 1 |
| 21-03-2021 | 1 |
| 24-03-2021 | 1 |
| 26-03-2021 | 1 |
| 04-04-2021 | 1 |
| 06-04-2021 | 1 |
| 07-04-2021 | 1 |
| 08-04-2021 | 1 |
| 10-04-2021 | 1 |
| 16-04-2021 | 1 |
| 19-04-2021 | 1 |
| 02-05-2021 | 1 |
| 03-05-2021 | 1 |
| 05-05-2021 | 1 |
| 19-05-2021 | 1 |
| 27-05-2021 | 1 |
| 02-06-2021 | 1 |
| 08-06-2021 | 1 |
| 15-06-2021 | 1 |
| 17-06-2021 | 1 |
| 24-06-2021 | 1 |
| 04-07-2021 | 1 |
| 08-07-2021 | 1 |
| 10-07-2021 | 1 |
| 11-07-2021 | 1 |
| 13-07-2021 | 1 |
| 15-07-2021 | 1 |
| 22-07-2021 | 1 |
| 27-07-2021 | 1 |
| 28-07-2021 | 1 |
| 11-08-2021 | 1 |
| 12-08-2021 | 1 |
| 16-08-2021 | 1 |
| 18-08-2021 | 1 |
| 20-08-2021 | 1 |
| 26-08-2021 | 1 |
| 27-08-2021 | 1 |
| 30-08-2021 | 1 |
| 31-08-2021 | 1 |
| 01-09-2021 | 1 |
| 05-09-2021 | 1 |
| 06-09-2021 | 1 |
| 13-09-2021 | 1 |
| 18-09-2021 | 1 |
| 23-09-2021 | 1 |
| 28-09-2021 | 1 |
| 02-10-2021 | 1 |
| 04-10-2021 | 1 |
| 05-10-2021 | 1 |
| 12-10-2021 | 1 |
| 19-10-2021 | 1 |
| 28-10-2021 | 1 |
| 01-11-2021 | 1 |
| 06-11-2021 | 1 |
| 18-11-2021 | 1 |
| 20-11-2021 | 1 |
| 21-11-2021 | 1 |
| 26-11-2021 | 1 |
| 27-11-2021 | 1 |
| 03-12-2021 | 1 |
| 06-12-2021 | 1 |
| 07-12-2021 | 1 |
| 13-12-2021 | 1 |
| 18-12-2021 | 1 |
| 02-01-2022 | 1 |
| 15-01-2022 | 1 |
| 16-01-2022 | 1 |
| 19-01-2022 | 1 |
| 25-01-2022 | 1 |
| 31-01-2022 | 1 |
| 01-02-2022 | 1 |
| 07-02-2022 | 1 |
| 21-02-2022 | 1 |
| 28-02-2022 | 1 |
| 03-03-2022 | 1 |
| 05-03-2022 | 1 |
| 14-03-2022 | 1 |
| 15-03-2022 | 1 |
| 23-03-2022 | 1 |
| 31-03-2022 | 1 |
| 04-04-2022 | 1 |
| 05-04-2022 | 1 |
| 10-04-2022 | 1 |
| 14-04-2022 | 1 |
| 15-04-2022 | 1 |
| 21-04-2022 | 1 |
| 02-05-2022 | 1 |
| 03-05-2022 | 1 |
| 06-05-2022 | 1 |
| 18-05-2022 | 1 |
| 21-05-2022 | 1 |
| 01-06-2022 | 1 |
| 04-06-2022 | 1 |
| 10-06-2022 | 1 |
| 13-06-2022 | 1 |
| 14-06-2022 | 1 |
| 17-06-2022 | 1 |
| 20-06-2022 | 1 |
| 23-06-2022 | 1 |
| 28-06-2022 | 1 |
| 03-07-2022 | 1 |
| 06-07-2022 | 1 |
| 11-07-2022 | 1 |
| 16-07-2022 | 1 |
| 17-07-2022 | 1 |
| 27-07-2022 | 1 |
| 28-07-2022 | 1 |
| 31-07-2022 | 1 |
| 01-08-2022 | 1 |
| 03-08-2022 | 1 |
| 05-08-2022 | 1 |
| 10-08-2022 | 1 |
| 20-08-2022 | 1 |
| 01-09-2022 | 1 |
| 05-09-2022 | 1 |
| 06-09-2022 | 1 |
| 14-09-2022 | 1 |
| 13-09-2022 | 1 |
| 19-09-2022 | 1 |
| 02-10-2022 | 1 |
| 05-10-2022 | 1 |
| 11-10-2022 | 1 |
| 10-10-2022 | 1 |
| 12-10-2022 | 1 |
| 15-10-2022 | 1 |
| 17-10-2022 | 1 |
| 01-11-2022 | 1 |
| 05-11-2022 | 1 |
| 08-11-2022 | 1 |
| 09-11-2022 | 1 |
| 10-11-2022 | 1 |
| 11-11-2022 | 1 |
| 14-11-2022 | 1 |
| 21-11-2022 | 1 |
| 09-12-2022 | 1 |
| 11-12-2022 | 1 |
| 14-12-2022 | 1 |
| 16-12-2022 | 1 |
| 19-12-2022 | 1 |
| 23-12-2022 | 1 |
| 31-12-2022 | 1 |
| 02-01-2023 | 1 |
| 11-01-2023 | 1 |
| 14-01-2023 | 1 |
| 15-01-2023 | 1 |
| 16-01-2023 | 1 |
| 17-01-2023 | 1 |
| 20-01-2023 | 1 |
| 21-01-2023 | 1 |
| 23-01-2023 | 1 |
| 24-01-2023 | 1 |
| 28-01-2023 | 1 |
| 01-02-2023 | 1 |
| 03-02-2023 | 1 |
| 05-02-2023 | 1 |
| 06-02-2023 | 1 |
| 07-02-2023 | 1 |
| 13-02-2023 | 1 |
| 27-02-2023 | 1 |
| 07-03-2023 | 1 |
| 09-03-2023 | 1 |
| 11-03-2023 | 1 |
| 15-03-2023 | 1 |
| 16-03-2023 | 1 |
| 27-03-2023 | 1 |
| 13-04-2023 | 1 |
| 14-04-2023 | 1 |
| 17-04-2023 | 1 |
| 19-04-2023 | 1 |
| 24-04-2023 | 1 |
| 28-04-2023 | 1 |
| 08-05-2023 | 1 |
| 16-05-2023 | 1 |
| 22-05-2023 | 1 |
| 25-05-2023 | 1 |
| 29-05-2023 | 1 |
| 31-05-2023 | 1 |
| 01-06-2023 | 1 |
| 10-06-2023 | 1 |
| 15-06-2023 | 1 |
| 19-06-2023 | 1 |
| 20-06-2023 | 1 |
| 25-06-2023 | 1 |
| 26-06-2023 | 1 |
| 27-06-2023 | 1 |
| 28-06-2023 | 1 |
| 30-06-2023 | 1 |
| 05-07-2023 | 1 |
| 17-07-2023 | 1 |
| 23-07-2023 | 1 |
| 28-07-2023 | 1 |
| 31-07-2023 | 1 |
| 01-08-2023 | 1 |
| 08-08-2023 | 1 |
| 09-08-2023 | 1 |
| 14-08-2023 | 1 |
| 15-08-2023 | 1 |
| 16-08-2023 | 1 |
| 18-08-2023 | 1 |
| 22-08-2023 | 1 |
| 26-08-2023 | 1 |
| 03-09-2023 | 1 |
| 06-09-2023 | 1 |
| 07-09-2023 | 1 |
| 13-09-2023 | 1 |
| 22-09-2023 | 1 |
| 24-09-2023 | 1 |
| 25-09-2023 | 1 |
| 02-10-2023 | 1 |
| 03-10-2023 | 1 |
| 05-10-2023 | 1 |
| 10-10-2023 | 1 |
| 20-10-2023 | 1 |
| 24-10-2023 | 1 |
| 26-10-2023 | 1 |
| 30-10-2023 | 1 |
| 01-11-2023 | 1 |
| 02-11-2023 | 1 |
| 04-11-2023 | 1 |
| 05-11-2023 | 1 |
| 23-11-2023 | 1 |
| 05-12-2023 | 1 |
| 06-12-2023 | 1 |
| 14-12-2023 | 1 |
| 11-01-2024 | 1 |
| 17-01-2024 | 1 |
| 24-01-2024 | 1 |
| 26-01-2024 | 1 |
| 30-01-2024 | 1 |
| 02-02-2024 | 1 |
| 07-02-2024 | 1 |
| 10-02-2024 | 1 |
| 25-02-2024 | 1 |
| 26-02-2024 | 1 |
| 01-03-2024 | 1 |
| 02-03-2024 | 1 |
| 09-03-2024 | 1 |
| 12-03-2024 | 1 |
| 16-03-2024 | 1 |
| 18-03-2024 | 1 |
| 19-03-2024 | 1 |
| 20-03-2024 | 1 |
| 01-04-2024 | 1 |
## Commit Distribution by Hour

| Hour | Number of Commits |
| --- | --- |
| 17:00 | 137 |
| 15:00 | 120 |
| 18:00 | 119 |
| 16:00 | 116 |
| 19:00 | 107 |
| 21:00 | 99 |
| 11:00 | 99 |
| 22:00 | 98 |
| 20:00 | 93 |
| 14:00 | 85 |
| 12:00 | 82 |
| 10:00 | 80 |
| 23:00 | 77 |
| 13:00 | 66 |
| 7:00 | 65 |
| 0:00 | 61 |
| 9:00 | 59 |
| 8:00 | 55 |
| 6:00 | 50 |
| 3:00 | 36 |
| 2:00 | 36 |
| 5:00 | 34 |
| 1:00 | 34 |
| 4:00 | 32 |
