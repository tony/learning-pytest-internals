# Contributing

Thanks for looking. This is a personal, closed-license set of learning notes
(`All Rights Reserved`) — there is no external contribution flow. This file
documents how a change here gets verified, for whoever, human or agent, makes
one.

How this project writes prose — README, commit messages, docstrings, and
source comments — is set out separately in [WRITING.md](WRITING.md). Read
that before changing any of it. The constraints every change is held to, and
the map of what is where, are in [AGENTS.md](../AGENTS.md).

## Getting set up

```console
$ uv sync --all-extras --dev
```

## The gates

Format:

```console
$ uv run ruff format . --check
```

Lint:

```console
$ uv run ruff check .
```

Type-check:

```console
$ uv run mypy .
```

Bare `uv run mypy` (no path) fails today: `[tool.mypy] files = ["src/"]`
points at a directory that does not exist yet. Always pass `.`.

Test:

```console
$ uv run py.test
```

Doctests inside lesson files are executed by this same command, not by a
separate step — the flags live in `[tool.pytest]` in `pyproject.toml`. Which
blocks qualify, and the one mistake that silently deletes a test, are in
[WRITING.md](WRITING.md#documented-examples-that-run).

Before claiming a test or a gate works, show it failing. A gate that has
never been red is an assumption.

## Tests

There is no `conftest.py`, no fixtures, and no custom markers — `pytest` runs
with `--doctest-modules` against whatever `.py` files it finds (see
[WRITING.md](WRITING.md#documented-examples-that-run) for exactly where).
`pytest-watcher` re-runs the suite on save:

```console
$ uv run pytest-watcher
```

`pytest-rerunfailures` is an installed dev dependency for retrying a flaky
test; nothing uses it yet.

## Adding a lesson

Copy `notes/lesson_template.py` and keep its shape: a module docstring with
Context, Summary, Tests, Type Hints, and Execution sections, an
`if __name__ == "__main__":` block that runs `main()`, and every function
typed and carrying a NumPy `Examples` doctest. Replace the template's asyncio
placeholder with a demonstration of the actual pytest internal the lesson
covers — introspect `_pytest` classes (`FixtureDef`, `FixtureRequest`,
`hookimpl`, and similar) rather than only showing public-API usage.

Lessons depend on nothing beyond pytest and the standard library.
`testpaths` in `pyproject.toml` already points at `src/`; a new chapter
directory there (`src/chapter_N/`, matching the outline in
[notes/progression.md](../notes/progression.md)) is collected automatically
once it exists.

Run the gates against just the new file before committing:

```console
$ uv run py.test path/to/lesson.py
```

```console
$ uv run mypy path/to/lesson.py
```

```console
$ uv run ruff check path/to/lesson.py
```

## Pull requests

One subject per pull request. Unrelated cleanup found along the way belongs
in its own commit.

Commit format is in [WRITING.md](WRITING.md#commits).
