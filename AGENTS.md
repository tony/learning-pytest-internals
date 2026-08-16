# AGENTS.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an educational project teaching experienced Python developers the deep internals of pytest. The focus is on advanced topics like fixtures, hooks, and plugin development through a structured 6-chapter curriculum.

**Important**: This is a learning resource focused on pytest internals, not just usage. All lessons should demonstrate internal mechanisms and show how pytest works under the hood.

## Key Commands

```bash
# Install dependencies
uv sync --all-extras --dev

# Run tests
uv run pytest

# Run tests in watch mode
uv run pytest-watcher

# Run specific test
uv run pytest path/to/test.py::test_function

# Linting and formatting
uv run ruff check .
uv run ruff format .

# Type checking
uv run mypy .
```

## Code Architecture

### Lesson Template Structure
All lessons must follow the exact pattern in `notes/lesson_template.py`:

```python
#!/usr/bin/env python
"""
[Lesson Title].

[Context]
- State the concept being taught
- List prerequisites from previous lessons
- Include references to pytest documentation

[Summary]
- What the lesson covers
- What learners will understand after completion

Tests:
- Doctests for simple examples (<5 lines)
- Pytest tests for complex examples

Type Hints & Mypy:
- All functions must have type hints
- Return types explicitly specified

Execution:
- Running `python thisfile.py` executes main()
- Keep examples minimal and self-contained
"""
```

Key requirements:
- **Standard library only** (beyond pytest itself)
- **Executable files** with `if __name__ == "__main__": main()`
- **Both doctests and pytest tests** in every lesson
- **NumPy-style docstrings** with Examples section
- **Type annotations** for all parameters and returns
- **Self-contained examples** that can be understood without external context
- **Educational focus** on clarity over complexity

### Project Layout
```
src/
├── chapter_1/           # Intermediate Fixture Mechanics
├── chapter_2/           # Internals of Fixtures
├── chapter_3/           # Custom Fixture Factories
├── chapter_4/           # Hooks and Plugin Architecture
├── chapter_5/           # Writing Advanced Plugins
└── chapter_6/           # Contributing to Pytest Core
```

### Development Standards
- **Type Safety**: Mypy strict mode - all code must be fully typed
- **Documentation**: NumPy-style docstrings with Context, Summary, Examples sections
- **Testing**: Doctests for simple demos, pytest for complex scenarios
- **Linting**: Comprehensive ruff rules (pycodestyle, pyflakes, isort, pydocstyle)

### Classes with fields

**Classes with fields** — `NamedTuple`, dataclasses — document every field in
an `Attributes` section:

```python
class HookCall(NamedTuple):
    """One recorded call into a pytest hook.

    Attributes
    ----------
    hook_name : str
        Hook that fired, e.g. ``pytest_collection_modifyitems``.
    plugin : str
        Plugin whose implementation ran.
    """
```

A type says how a field is shaped, not what it holds. Describing each one
keeps that meaning next to the code, and anything that renders the class —
autodoc, a REPL, an editor tooltip — has a description to show instead of a
bare name.

### Doctests

**All functions and methods MUST have working doctests.** Doctests serve as both documentation and tests.

**CRITICAL RULES:**
- Doctests MUST actually execute - never comment out function calls or similar
- Doctests MUST NOT be converted to `.. code-block::` as a workaround (code-blocks don't run)
- If you cannot create a working doctest, **STOP and ask for help**

**Available tools for doctests:**
- `doctest_namespace` fixtures: `tmp_path` (add more via `conftest.py`)
- Ellipsis for variable output: `# doctest: +ELLIPSIS`

**`# doctest: +SKIP` is NOT permitted** - it's just another workaround that doesn't test anything.

**Example doctest for pytest internals:**
```python
>>> import pytest
>>> from _pytest.fixtures import FixtureDef
>>> # Demonstrate how pytest creates fixture definitions
>>> def example_fixture():
...     return "fixture value"
>>> hasattr(FixtureDef, 'execute')
True
```

**When output varies, use ellipsis:**
```python
>>> import pytest
>>> pytest.__version__  # doctest: +ELLIPSIS
'...'
```

## 6-Chapter Curriculum Flow

### Chapter 1: Intermediate Fixture Mechanics
- Fixture scopes and lifecycle
- Autouse fixtures
- Advanced parameterization
- Yield fixtures and teardown

### Chapter 2: Internals of Fixtures
- `FixtureManager` and `FixtureDef` classes
- `FixtureRequest` and request context
- Dependency resolution (fixture DAG)
- Fixture lifecycle and caching

### Chapter 3: Custom Fixture Factories
- Factory-style fixtures (returning callables)
- Dynamic fixture generation
- Fixture composition patterns
- Performance optimization

### Chapter 4: Hooks and Plugin Architecture
- Hook mechanism overview
- Lifecycle hooks (configure, session, teardown)
- Implementing hook functions
- Conftest vs. external plugins

### Chapter 5: Writing Advanced Plugins
- Plugin structure and registration
- Implementing fixtures, CLI options, hooks
- Testing and maintaining plugins
- Distribution and community

### Chapter 6: Contributing to Pytest Core
- Pytest codebase structure
- Development environment setup
- Contribution guidelines
- Understanding core decisions

## Creating New Lessons

1. Copy `notes/lesson_template.py` as starting point
2. Place in appropriate chapter directory under `src/`
3. Follow the exact docstring format from template
4. Demonstrate pytest internals, not just usage:
   - Show internal classes (`FixtureManager`, `FixtureRequest`, etc.)
   - Explain how pytest mechanisms work under the hood
   - Use introspection to reveal internal state
5. Testing approach:
   - Doctests in docstrings for simple examples (<5 lines)
   - Pytest tests for complex scenarios
   - All examples must be executable
6. Verify lesson quality:
   ```bash
   uv run pytest src/chapter_X/lesson_Y.py
   uv run mypy src/chapter_X/lesson_Y.py
   uv run ruff check src/chapter_X/lesson_Y.py
   ```

## Important Patterns from Template

The lesson template (`notes/lesson_template.py`) uses asyncio as an example but should be adapted for pytest internals:
- Replace asyncio examples with pytest internal demonstrations
- Keep the same structure: demonstrate_concept() and main() functions
- Ensure doctests work with pytest's output
- Use minimal delays/sleeps only if needed for timing demonstrations

## Common Development Tasks

When working on lessons:
1. **Focus on internals**: Use `_pytest` modules and internal APIs to show how things work
2. **Progressive learning**: Each lesson builds on previous ones - check dependencies
3. **Practical examples**: Show real-world use cases for internal knowledge
4. **Performance considerations**: Discuss memory usage and efficiency of different approaches

## Git Commit Standards

Format commit messages as:

```
Scope(type[detail]): concise description

why: Explanation of necessity or impact.

what:
- Specific technical changes made
- Focused on a single topic
```

The blank line between the `why:` block and the `what:` block is
optional — useful when the `why:` body runs to multiple lines and the
two sections benefit from visual separation.

Common commit types:

- **feat**: New features or enhancements
- **fix**: Bug fixes
- **refactor**: Code restructuring without functional change
- **docs**: Documentation updates
- **chore**: Maintenance (dependencies, tooling, config)
- **test**: Test-related updates
- **style**: Code style and formatting
- **py(deps)**: Dependencies
- **py(deps[dev])**: Dev Dependencies
- **ai(rules[AGENTS])**: AI rule updates
- **ai(claude[rules])**: Claude Code rules (CLAUDE.md)

Subjects are plain English. Never put curriculum codes or other
repo-internal shorthand in the subject line — a reader of
`git log --oneline` should understand every title cold.

Example:

```
docs(README[setup]): Document the uv-based install

why: New clones failed on the old pip instructions.

what:
- Replace pip commands with uv equivalents
- Note the supported Python floor
```

For multi-line commits, use heredoc to preserve formatting:

```bash
git commit -m "$(cat <<'EOF'
Scope(type[detail]): concise description

why: Explanation of the change.

what:
- First change
- Second change
EOF
)"
```

Guidelines:

- Subject line at most 50 characters; body lines at most 72
- Imperative mood ("Add", "Fix" — not "Added", "Fixed")
- One topic per commit; blank line between subject and body
- Mark breaking changes with `BREAKING:`

## Documentation Standards

### Code Blocks

Code blocks are paste-and-run units: pasting one block runs exactly one
intended action. Doctests and other executed examples are exempt — the test
suite runs them, nobody pastes them.

- **One command per block.** Multiple steps may share a block only when
  explicitly chained with `&&`, `;`, or `\` continuations — the chain is
  then one logical command.
- **Explanations go in prose above the block**, never as `#` comments inside it.
- **Command menus are per-command blocks with prose lead-ins**, not tables.
- **Shell commands use the `console` tag with a `$ ` prefix.** This separates
  interactive commands from scripts and enables prompt-aware copy.
- **Split long commands with `\`** — one flag or flag+value pair per indented
  continuation line, positional arguments last.

Good:

Show the last ten commits as a graph:

```console
$ git log \
    --max-count=10 \
    --graph \
    --oneline
```

Bad:

```console
# Show the last ten commits as a graph
$ git log --max-count=10 --graph --oneline
```

## Comments earn their maintenance cost

A comment ships only if it passes all three gates. Fail any: delete or rewrite.
Borderline: delete — borderline means the information is reconstructible, which
is what makes deletion cheap.

**Loss.** Three years from now, would losing this cost a maintainer real time
rediscovering intent, an invariant, a constraint, or a failure mode the code and
tests do not already make obvious?

**Elite.** Would SQLite, Redis, the Go standard library, or CPython write this
comment, at this length? Those projects state the constraint and stop. They do
not argue with an imagined objector.

**Upkeep.** Will it stay true without maintenance? A comment that hand-syncs a
value the code owns — a count, an offset, a line reference, a duplicated
constant — is false the first time that value moves.

### Ceiling

One or two lines. A comment reaching four is either carrying several facts, in
which case split it, or arguing, in which case cut it to the fact.

Rationale, alternatives weighed, and the story of how the code got here belong
in the commit message: timestamped, attached to the exact diff, and free to
maintain.

A comment often holds both a constraint and the deliberation that found it. Keep
the constraint, cut the deliberation. "Runs at most once per second" survives;
"this is the right trade for now" does not.

### Keep

- Why over how: upstream quirks, protocol and compatibility constraints,
  performance tradeoffs still part of the contract.
- Invariants, preconditions, ordering, lifetime, and concurrency requirements
  that types and tests cannot express.
- Code that looks wrong but is not, so a later cleanup does not reintroduce the
  bug.
- A high-level sketch of an algorithm whose local operations do not reveal the
  whole.

### Delete

- Narration of the next lines; code translated into English.
- Restated names, types, defaults, or control flow.
- Values duplicated from the code and hand-synced.
- Justification, hedging, or apology for a choice.
- Speculation about future requirements.
- History version control already holds, including commented-out code.
- Ticket and issue numbers. They say nothing to a reader without tracker access,
  and they rot when the tracker moves. Unfinished work goes in the tracker, not
  the source.
- Transient observations — "currently", "for now", "the latest release" —
  that go stale with no nearby edit.

### The upkeep gate in practice

It reaches values that track our own code. It does not reach frozen external
facts.

Bad (Delete):

```python
# There are 321 tests to complete for servers.
```

Good (Keep):

```python
# CPython < 3.11 has no ExceptionGroup, so this branch stays.
```

### Documentation exception

Doctests, minimal usage examples, and param, return, and raises lines on public
API are exempt from the loss gate — they serve the caller, not the maintainer.
They are exempt from nothing else. Ceiling: a good man page entry.

NumPy-style `Parameters`, `Returns`, and `Attributes` sections and executable
doctests fall under this exception — autodoc ships every field whether or not
you describe it, and a doctest that runs is also a test.

## AI Slop Prevention

Treat AI slop as **review-hostile noise**, not as proof that text or
code is wrong. The goal is to maximize information density by removing
artifacts that make the repository harder to trust or navigate.

### The Anti-Slop Rubric

Before committing, audit all AI-assisted changes for these noise
patterns:

- **AI Signatures:** Remove "Generated by", footers, conversational
  filler ("Certainly!", "Here is..."), unexplained emojis (🤖, ✨), and
  AI-tool metadata.
- **Brittle References:** Avoid hard-coded line numbers, fragile
  file/test counts, dated "as of" claims, bare SHAs, and local
  absolute paths unless they are strict evidentiary artifacts (e.g.,
  benchmark logs).
- **Diff Narration:** Do not restate what moved, was renamed, or was
  removed in artifacts the downstream reader holds: code, docstrings,
  README, CHANGES, PR descriptions, or release notes. The diff and
  commit message already carry this history.
- **Branch-Internal Narrative:** Do not mention intermediate branch
  states, abandoned approaches, or "no longer" behavior unless users
  of a published release actually experienced the old state (**The
  Published-Release Test**).
- **Low-Value Scaffolding:** Remove ownerless TODOs (`TODO: revisit`),
  unused future-proofing, debug artifacts, and defensive wrappers that
  do not protect a currently reachable failure mode.
- **Prose Inflation:** Replace generic AI "tells" like *comprehensive,
  robust, seamless, production-ready, leverage, delve, tapestry,* and
  *best practices* with concrete descriptions of behavior,
  constraints, or trade-offs.
- **Coded Labels:** Write rules, options, and findings as plain
  imperatives. Don't tag them with codes like `[R1]`, `A1`, or
  `Option B` in artifacts a human reads — the reader shouldn't have to
  decode an index. Internal agent bookkeeping may use ids; shipped text
  may not.

### Durable Source Links

Link to a pinned revision, never to trunk. A pinned permalink is not a
brittle reference; an unlinked SHA dropped into prose is. `blob/main/…`
links rot silently — the file moves, lines shift, and the anchor lands
on unrelated code while still resolving.

- Prefer a release tag (`blob/v1.4.0/…`). Most durable, and it tells
  the reader which released version the claim held for.
- Otherwise use a 7-char commit ref (`blob/9a29b1a/…`) reachable from
  trunk. Use when there is no tag or the claim is about unreleased
  code. Never a PR-head SHA — it can be rebased or garbage-collected.
- Reserve `blob/main/…` for living documents meant to always show the
  latest state, such as a contributing guide.
- Line anchors (`#L120-L145`) are only safe on a pinned ref.

### Preservation & Context

Subjective cleanup must never remove load-bearing rationale. Adjudicate
comments with the comment policy above; borderline cases are deleted, not
kept.

- **Preserve the "Why":** You MUST NOT delete comments that document
  invariants, protocol constraints, platform quirks, security
  boundaries, and upstream workarounds.
- **Evidence is Immune:** Preserve exact counts, dates, and SHAs when
  they serve as evidence in benchmark results, release notes, stack
  traces, or lockfiles.
- **Behavior Over Inventory:** A useful description explains what
  changed for the *system or user*; it does not provide an inventory
  of files or functions the diff already shows.

### The Published-Release Test

Long-running branches accumulate tactical decisions — renames,
refactors, attempts-then-reverts. When deciding what counts as
branch-internal, use trunk or the parent branch as the baseline — not
intermediate states inside the current branch. Ask:

> Did users of the most recently published release ever experience
> this old name, old behavior, or bug?

If the answer is **no**, it is branch-internal narrative. Move it to
the commit message and describe only the final state in the artifact.

**Keep in shipped artifacts:**

- Deprecations and migration guides for symbols that actually shipped.
- `### Fixes` entries for bugs that affected users of a published
  release.
- Comments explaining *why the current code looks this way*
  (invariants, platform quirks) that make sense to a reader who never
  saw the previous version.

### Cleanup in Hindsight

When applying these rules retroactively from inside a feature branch,
first establish scope by diffing against the parent branch (or trunk)
to identify which commits this branch actually introduced. Then:

- **In-branch commits:** Prompt the user with two options: `fixup!`
  commits with `git rebase --autosquash` to address each causal commit
  at its source, or a single cleanup commit at branch tip.
- **Trunk/Parent commits:** Default to leaving them alone. Act only on
  explicit user instruction. If the user opts in, fold the cleanup
  into a single commit at branch tip; do not rewrite shared history.
- **Scope guard:** If cleaning prior slop would touch a colleague's
  work or expand the branch beyond its stated goal, stay in lane:
  protect the current goal and leave prior slop alone.

### Change Discipline

- Make the smallest coherent change that solves the verified problem;
  keep unrelated cleanup out of it.
- Reuse an existing file, component, helper, API, or test before adding
  a new one. Modify in place when the change fits the file's
  responsibility.
- Keep new APIs private until a caller outside the module needs them.
- Add a file only for a durable boundary — a distinct responsibility,
  independent reuse, or splitting an oversized high-touch module — not
  for a single-use helper or a one-line re-export.

### Keep Instructions Lean

Treat this file like code and prune it.

- Delete a line whose removal would not cause a mistake.
- Move multi-step procedures into skills, path-specific rules into
  nested AGENTS.md files, and hard limits into hooks or CI.
- Keep only non-obvious, broadly applicable defaults here. Anything a
  reader can infer from the code, a manifest, or a linter does not
  belong.
