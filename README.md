# learning-pytest-internals

Personal, closed-license (`All Rights Reserved`) notes for learning pytest's
internals — fixtures, hooks, and plugin architecture — down to the `_pytest`
classes that implement them. It is not a published package: there is no
`[build-system]` table in `pyproject.toml`, and nothing here is meant to be
installed or imported.

What exists today: a six-chapter curriculum
([notes/progression.md](notes/progression.md)) and a lesson-file template
([notes/lesson_template.py](notes/lesson_template.py)) whose two doctests are
the entire test suite. No chapter has been written yet.

Requires Python 3.14 or later (`requires-python` in `pyproject.toml`).

## Setup

```console
$ uv sync --all-extras --dev
```

## Running the suite

```console
$ uv run py.test
```

Both commands, and the rest of the workflow, are in
[.github/CONTRIBUTING.md](.github/CONTRIBUTING.md). How prose and doctests in
this repository are written is in [.github/WRITING.md](.github/WRITING.md).
