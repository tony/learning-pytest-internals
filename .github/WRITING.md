# Writing

How this project writes prose, for humans and agents alike. It governs
`README.md`, commit messages, docstrings, and source comments — every surface
a reader reaches.

For environment setup, the gates, and the pull request workflow, see
[CONTRIBUTING.md](CONTRIBUTING.md).

## Voice

A docstring says what a caller may rely on; prose says what happens. Both are
present tense, lead with the thing being described, and stop. Why something
was built that way belongs in the commit message, which is timestamped and
attached to the diff.

The most useful editing operation is deleting the introductory sentence.

Lead with verbs and name concrete things. Put identifiers in backticks. Prefer
short declarative sentences, one operational fact each. Do not explain Python
to Python developers; do explain pytest's internal semantics, which is the
whole point of this repository.

Type annotations describe shape. Documentation describes meaning. A sentence
that restates a signature has said nothing.

Use MUST, SHOULD, and MAY only where the normative sense is meant. Say what
actually happens rather than that something is "supported".

| Instead of                       | Prefer                            |
| --------------------------------- | --------------------------------- |
| "We added…"                      | "`main()` now returns…"           |
| "New and improved"               | "The fixture now…"                |
| "powerful", "seamless"           | state the capability              |
| "easily", "simply", "just"       | omit                              |
| "simple", "obvious", "intuitive" | omit                              |
| "robust"                         | name the failure that is handled  |
| "comprehensive"                  | name what is covered              |
| "optimized", "blazingly fast"    | give the magnitude                |
| "under the hood"                 | omit unless observable            |
| "please note that", "note that"  | state the fact                    |
| "leverage", "utilize"            | "use"                             |
| "delve into"                     | "read", or omit                   |
| "best practices"                 | name the practice                 |
| "in order to"                    | "to"                              |

**Terminology.** Match pytest's own class and hook names rather than inventing
synonyms — write `FixtureDef`, `hookimpl`, `pytest_runtest_setup` as pytest
spells them. A lesson that renames pytest's own vocabulary teaches the wrong
name.

## Who you are writing for

The primary reader is future you, opening a lesson file or
`notes/progression.md` without today's context. Serve that reader first: say
what a lesson teaches and why before showing the mechanism, and let the
docstring's first sentence stand alone.

A second reader is anyone else who ends up with this repository. Mark material
that assumes deep pytest-internals background as such, so a skimmer can tell
where they can stop.

## README

`README.md` is the shortest path from "what is this?" to running the tests
that exist today — not a syllabus for the curriculum. Get to a runnable
command before anything skippable; a logo or a mission statement costs the
reader the same as three paragraphs of history.

State the minimum Python version in prose; `requires-python` in
`pyproject.toml` is the authority. This project has no `[build-system]` table
and is not published anywhere — say that plainly instead of writing an install
command that would not work.

Headings stay conventional and stable, because people deep-link them.

## Documented examples that run

Examples here are tests only when they carry a `>>> ` prompt, and only when
they live in a `.py` file. **Markdown is never executed** — no docutils-based
doctest collector is installed, so a `>>> ` block inside `README.md` or any
future documentation page is illustration, not a test. Believing otherwise is
the expensive mistake in this repository.

**A fence tag is cosmetic. Only a `>>> ` prompt executes.** A block written as

    ```python
    add(2, 2)
    ```

is prose that looks like a test; nothing collects it, and it can be wrong for
years. The same block written with prompts is a test:

    ```python
    >>> add(2, 2)
    4
    ```

Removing the prompts from an existing example leaves a green test suite and a
silently deleted test. When editing a file that contains examples, count the
prompts before and after.

**The fence tag is `python`.** Not `pycon`, not bare.

**Where examples run.** `[tool.pytest]` in `pyproject.toml` sets `addopts =
[..., "--doctest-modules"]` (pytest's native-TOML config table, not the older
`[tool.pytest.ini_options]`), so every `.py` file pytest collects has its
docstring doctests executed — there is no separate doctest step.
`testpaths = ["src"]` names where lessons will live once chapters exist; that
directory does not exist yet, so pytest warns and falls back to a recursive
search from the repository root. That fallback is how
`notes/lesson_template.py`'s two doctests run today.

**Module scope, not block scope.** There is no `conftest.py` and no
`doctest_namespace` fixture, but a `>>> ` block is not empty either:
`doctest` runs each doctest against a copy of its own module's namespace,
so a name imported or defined at module level — like
`notes/lesson_template.py`'s top-level `import asyncio` — is visible
inside every doctest in that file without re-importing it. What a block
does not get for free: state left behind by a *different* doctest (each
doctest gets its own copy of the module namespace, so a name one `>>> `
block defines does not survive into another function's doctest) and
anything defined only in a different module.

**`# doctest: +SKIP` is not permitted.** It tests nothing. If an example
cannot pass, fix the example or fix the code — never downgrade a doctest to an
unprompted fence or a `.. code-block::` to make it pass.

**Option flags.** `ELLIPSIS` and `NORMALIZE_WHITESPACE` are enabled globally
(`doctest_optionflags` in `pyproject.toml`), so `...` elides variable output
and whitespace differences do not fail a comparison. Reach for an inline
`# doctest: +FLAG` only for the block that needs something more.

**Every lesson function carries a working doctest.**
`notes/lesson_template.py` is the skeleton every lesson file copies: a module
docstring with Context, Summary, Tests, Type Hints, and Execution sections,
then functions whose `Examples` section is a real, passing doctest. If a
function cannot get a working doctest, write a `pytest` test for it instead of
skipping the example.

**Docstring examples** use the NumPy `Examples` section:

    Examples
    --------
    >>> add(2, 2)
    4

## Docstrings

The prime directive: never restate the type. The annotation is the source of
truth; the docstring carries what the annotation cannot.

This is documentation debt wearing a docstring:

    def get_id(pane: Pane) -> str:
        """Get the pane's identifier.

        Parameters
        ----------
        pane : Pane
            The pane.

        Returns
        -------
        str
            The identifier.
        """

Document instead the dimensions the type system cannot encode:

- **Mutation.** What it changes in place.
- **Ordering.** Whether results come back in a guaranteed order.
- **Timing.** What has finished by the time an awaitable resolves.
- **Failure.** Which exceptions are raised and what triggers each.
- **Idempotence.** Whether calling twice does anything the second time.
- **Concurrency.** Whether the object is thread-safe, or the operation
  coalesces or queues concurrent calls.
- **Boundary behaviour.** What zero, empty, and the maximum do.

**Classes with fields** — `NamedTuple`, dataclasses — document every field in
an `Attributes` section:

    class HookCall(NamedTuple):
        """One recorded call into a pytest hook.

        Attributes
        ----------
        hook_name : str
            Hook that fired, e.g. ``pytest_collection_modifyitems``.
        plugin : str
            Plugin whose implementation ran.
        """

A type says how a field is shaped, not what it holds. Describing each one
keeps that meaning next to the code, and anything that renders the class —
autodoc, a REPL, an editor tooltip — has a description to show instead of a
bare name.

The first sentence stands alone; tooling truncates there. PEP 257 applies:
triple double quotes, an imperative one-line summary ending in a period, a
blank line before any extended description. Do not repeat an introspectable
signature.

One docstring dialect per repository, enforced by the linter (`ruff`'s
`pydocstyle` convention is `numpy`) rather than relitigated in review.

## Source comments

A comment ships only if it passes all three gates. Fail any: delete or
rewrite. Borderline: delete — borderline means the information is
reconstructible, which is what makes deletion cheap.

**Loss.** Three years from now, would losing this cost a maintainer real time
rediscovering intent, an invariant, a constraint, or a failure mode the code
and tests do not already make obvious?

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

### Keep

- Why over how: upstream quirks, protocol and compatibility constraints,
  performance tradeoffs still part of the contract.
- Invariants, preconditions, ordering, lifetime, and concurrency requirements
  that types and tests cannot express.
- Code that looks wrong but is not, so a later cleanup does not reintroduce
  the bug.
- A high-level sketch of an algorithm whose local operations do not reveal the
  whole.

### Delete

- Narration of the next lines; code translated into English.
- Restated names, types, defaults, or control flow.
- Values duplicated from the code and hand-synced.
- Justification, hedging, or apology for a choice.
- Speculation about future requirements.
- History version control already holds, including commented-out code.
- Ticket and issue numbers. They say nothing to a reader without tracker
  access, and they rot when the tracker moves.
- Transient observations — "currently", "for now", "the latest release" —
  that go stale with no nearby edit.

### The upkeep gate in practice

It reaches values that track our own code. It does not reach frozen external
facts.

Bad (Delete):

    # There are 321 tests to complete for servers.

Good (Keep):

    # CPython < 3.11 has no ExceptionGroup, so this branch stays.

### Documentation exception

Doctests, minimal usage examples, and parameter, return, and raises entries on
public API are exempt from the loss gate — they serve the caller, not the
maintainer. They are exempt from nothing else. Ceiling: a good man page entry.

## Markdown

Prose wraps at 80 columns. Table rows, badge lines, and long links are exempt,
because breaking them harms rendering. A pull request or issue body does not
wrap at all: GitHub renders a single newline as a space in a file and as a
line break in a comment, so a wrapped comment body arrives as ragged stubs.

GitHub alert blocks — `> [!NOTE]`, `> [!WARNING]` — render as literal text
outside GitHub, so reserve them for at most one load-bearing warning per
document.

Do not use a local absolute path or an email address in anything published.

## Code blocks

Code blocks are paste-and-run units: pasting one block runs exactly one
intended action. Executed examples are exempt — the test suite runs them,
nobody pastes them.

- **One command per block.** Multiple steps may share a block only when
  explicitly chained with `&&`, `;`, or `\` continuations — the chain is then
  one logical command.
- **Explanations go in prose above the block**, never as `#` comments inside
  it.
- **Command menus are per-command blocks with prose lead-ins**, not tables.
- **Shell commands use the `console` tag with a `$ ` prefix.** This separates
  interactive commands from scripts and enables prompt-aware copy.
- **Split long commands with `\`** — one flag or flag+value pair per indented
  continuation line, positional arguments last.

Good — show the last ten commits as a graph:

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

## Commits

```
Scope(type[detail]): concise description

why: Explanation of necessity or impact.

what:
- Specific technical changes made
- Focused on a single topic
```

Keep the subject to 50 characters or fewer and wrap body lines at 72. A blank
line between the `why:` and `what:` blocks is optional — useful when the
`why:` body runs to multiple lines and the two sections benefit from visual
separation.

Routine maintenance commits drop the colon and take a capitalised description,
which is what distinguishes them at a glance in `git log --oneline`:

```
py(deps[dev]) Bump dev packages
ai(rules[AGENTS]) Judge comments by three gates
```

Everything that changes behaviour keeps the colon. Mark a breaking change with
a `BREAKING:` line in the body. Never put a curriculum code — a chapter or
lesson number — in the subject line; a reader of `git log --oneline` should
understand every title without the outline open.

Common types:

- **feat**: New features or enhancements
- **fix**: Bug fixes
- **refactor**: Code restructuring without functional change
- **docs**: Documentation updates
- **chore**: Maintenance (dependencies, tooling, config)
- **test**: Test-related updates
- **style**: Code style and formatting
- **ci**: Workflow and pipeline changes
- **py(deps)**: Dependencies
- **py(deps[dev])**: Dev dependencies
- **ai(rules[AGENTS])**: AI rule updates

Example:

```
notes(style[template]): Put summaries in imperative mood

why: PEP 257 summaries read as commands, not descriptions.

what:
- Rewrite the template's example summary line
```

For a multi-line message, use a heredoc so the formatting survives:

```console
$ git commit -m "$(cat <<'EOF'
Scope(feat[detail]): Concise description

why: Explanation of the change.

what:
- First change
- Second change
EOF
)"
```

## Slop prevention

Treat AI slop as review-hostile noise, not as proof that text or code is
wrong. The goal is to maximise information density.

- **AI signatures.** No "Generated by", no conversational filler, no
  unexplained emoji, no tool metadata.
- **Brittle references.** No hard-coded line numbers, fragile file counts,
  dated "as of" claims, bare SHAs, or local absolute paths — unless they are
  strict evidentiary artefacts such as a benchmark log.
- **Diff narration.** Do not restate what moved, was renamed, or was removed
  in anything the reader holds alongside the diff: code, docstrings, README,
  or a pull request description. The diff and the commit message already
  carry it.
- **Branch-internal narrative.** Do not mention intermediate states,
  abandoned approaches, or "no longer" behaviour that no published state of
  this repository ever exhibited to a reader.
- **Low-value scaffolding.** No ownerless TODOs, unused future-proofing,
  debug artefacts, or defensive wrappers around failure modes nothing can
  reach.
- **Prose inflation.** The diction table under [Voice](#voice) governs;
  replace an inflated word with a concrete description of behaviour,
  constraints, or trade-offs.
- **Coded labels.** Write rules and findings as plain imperatives. No `[R1]`,
  `Option B`, or any index a reader has to decode.

**Durable source links.** A link into this repository's history should point
at a commit reachable from `main` (a short SHA is enough — there are no
release tags), not at `blob/main/…`, which rots silently as the file moves and
the anchor lands on unrelated code.

Preserve the "why". Never delete a comment documenting an invariant, a
protocol constraint, or an upstream workaround — those are the facts
[Source comments](#source-comments) keeps, and every other comment is judged
by it.
