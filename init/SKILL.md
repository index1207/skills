---
name: init
description: >
  Generates an AGENTS.md file at the project root — a guidelines document for AI agents
  and human contributors. It auto-discovers project structure, tech stack, build system,
  test framework, code style, and Git conventions. Use this skill whenever the user asks
  to create, generate, or update an AGENTS.md file, project guidelines, agent conventions,
  or project documentation. If AGENTS.md already exists and the user asks to refresh or
  regenerate it, re-scan the project and update accordingly.
---

# AGENTS.md Generator Skill

Scans the project root and generates a guidelines document (`AGENTS.md`) for AI agents and human contributors.

## Workflow

### 1. Project Discovery

Explore the project root in this order:

1. **Project identity**: Read `README.md`, `package.json`, `pyproject.toml`, `Cargo.toml`, `go.mod`, `pom.xml`, `composer.json`, etc. to identify the project name, description, language, and framework.
2. **Build / package system**: Look for `package.json` (npm), `pyproject.toml` (uv/pip), `Cargo.toml` (cargo), `Makefile`, `Justfile`, `go.mod` (go modules), `CMakeLists.txt`, etc.
3. **Test framework**: Search for test file patterns (`test_*.py`, `*.test.ts`, `*_test.go`, `*.spec.js`, etc.), `pytest.ini`, `vitest.config.*`, `jest.config.*`, `CMakeLists.txt` (for Google Test), etc.
4. **Lint / formatter config**: Look for `.eslintrc*`, `ruff.toml`, lint sections in `pyproject.toml`, `.golangci.yml`, `tsconfig.json`, `prettier.config.*`, `.clang-format`, etc.
5. **Source code structure**: Analyze the main directory layout, module patterns, and naming conventions.
6. **Git conventions**: Check `.gitignore`, branch naming, and commit convention patterns.

### 2. AGENTS.md Section Structure

Generate the AGENTS.md using the following sections (adapt to the project):

```markdown
# AGENTS.md

[Project Name] — [One-line description]

[Project layout summary]

## Commands

[Build, test, lint, and run commands]

## Project layout & module conventions

[Module structure, naming conventions, file placement rules]

## [Language/Framework] style

[Code style guide — adapt to the detected language/framework]

## Tests

[Test framework, patterns, conventions]

## Git

[Commit conventions, branch strategy, PR rules]

## Editor tip

[Useful editor tips if available]
```

### 3. Generation Rules

- **Auto-detect**: Base all content on actual file structure and configuration found in the project. Do not guess.
- **Language-specific style**: Python → PEP 8 + project conventions; TypeScript/JavaScript → ESLint/Prettier config; Go → `gofmt` + `golangci-lint` rules; C/C++ → detected compiler flags and project patterns.
- **Commands**: Report the exact build/test/lint tools the project uses.
- **Git conventions**: If no explicit commit convention exists, propose Conventional Commits but mark it as `proposed`.
- **Existing file**: If `AGENTS.md` already exists, overwrite it and mention that it is being regenerated.
- **Uncertain info**: Mark anything not confirmed by file discovery as `[TBD]` or `[Needs confirmation]` and ask the user.

### 4. Output

Write the `AGENTS.md` file to the project root. Use UTF-8 encoding.

## Example

Project discovery result:
```
my-project/
├── pyproject.toml
├── src/
│   └── myapp/
├── tests/
└── README.md
```

Generated `AGENTS.md`:
```markdown
# AGENTS.md

Conventions for AI agents and humans contributing to **My Project** — a Python web application.

Layout: `src/myapp` is the main application package; `tests/` contains all test files.

## Commands

- `uv run pytest` — run the full test suite.
- `uv run ruff check --fix .` and `uv run ruff format .` — lint and format.
- `uv run myapp` — start the application.

## Project layout & module conventions

- `src/myapp/models.py` — Pydantic data models.
- `src/myapp/routes.py` — HTTP route handlers.
- Tests mirror the source layout under `tests/`.

## Python style

- Follow PEP 8.
- Use modern type hints with `|` unions.
- Use `pathlib.Path` instead of `os.path`.

## Tests

- Stack: `pytest` + `pytest-asyncio`.
- Test files: `test_<module>.py`.

## Git

- Use Conventional Commits: `feat:`, `fix:`, `docs:`, `refactor:`, `test:`.
- Always create new commits; never force-push to main.
```

## Notes

- Base all content on actual project files and configuration. Do not write content based on guesses or assumptions.
- AGENTS.md is the "source of truth" for AI agents working in the project. Provide accurate, practical information.
- If the user wants additional conventions for a specific language or framework, ask before scanning.
