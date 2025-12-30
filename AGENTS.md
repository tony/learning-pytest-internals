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

### Commit Message Format
```
Component/File(commit-type[Subcomponent/method]): Concise description

why: Explanation of necessity or impact.
what:
- Specific technical changes made
- Focused on a single topic
```

### Common Commit Types
- **feat**: New features or enhancements
- **fix**: Bug fixes
- **refactor**: Code restructuring without functional change
- **docs**: Documentation updates
- **chore**: Maintenance (dependencies, tooling, config)
- **test**: Test-related updates
- **style**: Code style and formatting

### Dependencies Commit Format
- Python packages: `py(deps): Package update`
- Python dev packages: `py(deps[dev]): Dev package update`

### Examples

#### Feature Addition
```
core/schema(feat[Query]): Add fruit filtering by color

why: Users need to filter fruits by color in GraphQL queries
what:
- Add color filter parameter to fruits query
- Update resolver to handle color filtering
- Add tests for color filtering
```

#### Bug Fix
```
core/types(fix[FruitType]): Correct optional color relationship

why: Color field was incorrectly marked as required
what:
- Change color field to use Optional type
- Update tests to handle None values
```

#### Dependencies Update
```
py(deps[dev]): Add django-stubs for type checking

why: Improve type safety for Django models and ORM
what:
- Add django-stubs to dev dependencies
- Configure MyPy to use django-stubs plugin
```
For multi-line commits, use heredoc to preserve formatting:
```bash
git commit -m "$(cat <<'EOF'
feat(Component[method]) add feature description

why: Explanation of the change.
what:
- First change
- Second change
EOF
)"
```

### Guidelines
- Subject line: Maximum 50 characters
- Body lines: Maximum 72 characters
- Use imperative mood ("Add", "Fix", not "Added", "Fixed")
- One topic per commit
- Separate subject from body with blank line
- Mark breaking changes: `BREAKING:`

