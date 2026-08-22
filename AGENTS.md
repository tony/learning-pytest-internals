# AGENTS.md

Personal, closed-license notes for learning pytest's internals — fixtures,
hooks, and plugin architecture — through a written curriculum and one lesson
template. No chapter has been implemented yet.

Follow the conventions already in the tree, and keep a change scoped to what
was asked for.

## What is here

| Path | What it is |
| ---- | ---------- |
| `notes/progression.md` | The 6-chapter curriculum: what each chapter teaches, why, and its exercises. |
| `notes/lesson_template.py` | The lesson skeleton every future lesson copies. Its two doctests are the entire test suite today. |
| `src/chapter_N/` (not created yet) | Where lessons land once written, one directory per chapter; `testpaths` in `pyproject.toml` already points here. |
| `pyproject.toml` | Project metadata; pytest, ruff, and mypy config; dev dependencies. |
| `.github/workflows/tests.yml` | CI: the gates in [CONTRIBUTING.md](.github/CONTRIBUTING.md#the-gates). |

## Which policy applies

- Documentation, user-facing text, commit messages, docstrings, and source
  comments: [.github/WRITING.md](.github/WRITING.md)
- Environment, the gates, tests, and pull requests:
  [.github/CONTRIBUTING.md](.github/CONTRIBUTING.md)

Each of those is the single home for its subject. Where a rule seems to be
stated twice, the file listed above is the one that governs.

## Change discipline

- Make the smallest coherent change that solves the verified problem; keep
  unrelated cleanup out of it.
- Reuse an existing file, helper, or test before adding a new one.
- Add a file only for a durable boundary, not a single-use helper.
- Every lesson demonstrates a pytest internal via `_pytest` introspection —
  `FixtureManager`, `FixtureRequest`, `hookimpl`, and similar — not only
  public-API usage. That is this repository's reason to exist.
- `src/` does not exist yet. `testpaths` in `pyproject.toml` and
  `[tool.mypy] files` both point there, so a bare `uv run mypy` fails today;
  use the commands in [CONTRIBUTING.md](.github/CONTRIBUTING.md#the-gates).

## References

- Curriculum: [notes/progression.md](notes/progression.md)
- Lesson skeleton: [notes/lesson_template.py](notes/lesson_template.py)
- Upstream: [pytest documentation](https://docs.pytest.org/)
