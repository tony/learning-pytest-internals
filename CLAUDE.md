# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is an educational project teaching experienced Python developers the deep internals of pytest. The focus is on advanced topics like fixtures, hooks, and plugin development through a structured 6-chapter curriculum.

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

### Lesson Structure
All lessons follow the template in `notes/lesson_template.py`:
- Self-contained Python files with comprehensive docstrings
- Inline doctests for simple examples
- Pytest tests for complex scenarios
- Type hints throughout
- Executable main() function

### Project Layout
- `src/` - Main lesson files organized by chapter
- `notes/progression.md` - Complete curriculum outline
- Tests use doctests and pytest, configured to run from `src/`

### Development Standards
- **Type Safety**: Mypy strict mode enabled - all code must be fully typed
- **Documentation**: NumPy-style docstrings required
- **Testing**: Both doctests and pytest tests expected
- **Linting**: Comprehensive ruff rules enforce code quality

## Pytest Internals Focus

When working on lessons:
1. Focus on pytest's internal architecture, not just usage
2. Examples should demonstrate internal mechanisms like FixtureManager, FixtureRequest, hooks
3. Use only standard library + pytest to keep focus clear
4. Each lesson builds on previous knowledge - check progression.md

## Creating New Lessons

1. Copy `notes/lesson_template.py` as starting point
2. Follow the 6-chapter progression outlined in `notes/progression.md`
3. Ensure all examples are runnable and educational
4. Include both simple (doctest) and complex (pytest) examples
5. Verify with: `uv run pytest <lesson_file>` and `uv run mypy <lesson_file>`