# CLAUDE.md

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