# CLAUDE.md

This file provides guidance to AI assistants (Claude and others) working within this repository.

---

## Repository Overview

**Repository:** `ugurkesci/deneme`
**Status:** New / Empty — no source files have been committed yet.

This CLAUDE.md will evolve as the project grows. Sections marked **(TBD)** should be filled in once the project structure is established.

---

## Project Structure (TBD)

Once files are added, document the top-level layout here. Example:

```
/
├── src/          # Main source code
├── tests/        # Test suites
├── docs/         # Documentation
├── scripts/      # Build / utility scripts
└── CLAUDE.md     # This file
```

---

## Tech Stack (TBD)

- **Language:** _e.g., TypeScript, Python, Go_
- **Framework:** _e.g., React, FastAPI, Gin_
- **Database:** _e.g., PostgreSQL, SQLite_
- **Package manager:** _e.g., npm, pip, cargo_

---

## Development Workflow

### Getting Started

> Fill in setup commands once the project has dependencies.

```bash
# Example — replace with real commands
git clone <repo-url>
cd deneme
# install dependencies
# run the project
```

### Running Tests (TBD)

```bash
# Replace with the actual test command(s)
```

### Building (TBD)

```bash
# Replace with the actual build command(s)
```

### Linting / Formatting (TBD)

```bash
# Replace with the actual lint/format command(s)
```

---

## Git Conventions

- **Branch naming:** `feature/<short-description>`, `fix/<short-description>`, `chore/<short-description>`
- **Commit style:** Use imperative mood, present tense — _"Add login endpoint"_ not _"Added login endpoint"_
- **Commit scope:** Keep commits focused; one logical change per commit
- **Pull requests:** Squash-and-merge preferred; include a clear PR description

### Branch Strategy

| Branch | Purpose |
|--------|---------|
| `main` | Production-ready code |
| `feature/*` | New features |
| `fix/*` | Bug fixes |
| `chore/*` | Maintenance, dependency updates |

---

## Code Conventions

### General

- Prefer clarity over cleverness
- Avoid premature abstractions — wait until a pattern repeats at least three times before extracting it
- Delete dead code rather than commenting it out
- Keep functions small and single-purpose

### Naming

- Use descriptive names; avoid single-letter variables except in short, obvious loops
- Be consistent: if the codebase uses `camelCase` for variables, don't introduce `snake_case`

### Comments

- Write comments for *why*, not *what*
- Do not add comments that merely restate the code
- Keep comments up to date — stale comments are worse than no comments

### Error Handling

- Handle errors at the appropriate level; don't swallow them silently
- Validate at system boundaries (user input, external APIs); trust internal invariants

---

## Testing Conventions (TBD)

Once tests are set up, document:

- Where test files live (co-located vs. `/tests` directory)
- Naming conventions (`*.test.ts`, `test_*.py`, etc.)
- How to run a single test vs. the full suite
- Coverage requirements / thresholds

---

## Environment Variables (TBD)

Document required environment variables and where to find/set them:

| Variable | Description | Required |
|----------|-------------|----------|
| _(none yet)_ | | |

Use a `.env.example` file to document all required variables without exposing secrets.

---

## AI Assistant Guidelines

When working in this repository, AI assistants should:

1. **Read before writing** — always read a file before editing it; never modify code you haven't seen
2. **Stay minimal** — only change what is necessary to complete the task; avoid unsolicited refactoring
3. **No over-engineering** — don't add error handling, abstractions, or feature flags for hypothetical scenarios
4. **No new files unless necessary** — prefer editing existing files over creating new ones
5. **Respect existing style** — match the formatting, naming, and structure already present in the file being edited
6. **Security awareness** — never introduce command injection, XSS, SQL injection, or other OWASP Top 10 vulnerabilities
7. **Commit hygiene** — use clear, imperative-mood commit messages; reference issue numbers where relevant
8. **Ask when uncertain** — if the task is ambiguous or requires a significant architectural decision, ask before implementing

---

## CI / CD (TBD)

Document the pipeline once it is configured (GitHub Actions, GitLab CI, etc.).

---

## Contact / Ownership

- **Maintainer:** `ugurkesci`
- **Issues / feedback:** Open a GitHub issue on this repository
