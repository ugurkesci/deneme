# CLAUDE.md

This file provides guidance to AI assistants (Claude and others) working within this repository.

---

## Repository Overview

**Repository:** `ugurkesci/deneme`
**Maintainer:** `ugurkesci`
**Status:** Active development — update this file as the project grows.

> Sections marked **(TBD)** should be filled in once the project structure is established.

---

## Project Structure (TBD)

Once files are added, document the top-level layout here:

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

| Concern | Choice |
|---------|--------|
| Language | _e.g., TypeScript, Python, Go_ |
| Framework | _e.g., React, FastAPI, Gin_ |
| Database | _e.g., PostgreSQL, SQLite_ |
| Package manager | _e.g., npm, pip, cargo_ |

---

## Development Workflow

### Getting Started

```bash
git clone https://github.com/ugurkesci/deneme.git
cd deneme
# install dependencies — fill in once known
# run the project   — fill in once known
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
- **Commit style:** Imperative mood, present tense — _"Add login endpoint"_ not _"Added login endpoint"_
- **Commit scope:** One logical change per commit; keep commits focused
- **Pull requests:** Squash-and-merge preferred; write a clear PR description

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
- Avoid premature abstractions — extract a pattern only after it repeats at least three times
- Delete dead code; do not comment it out
- Keep functions small and single-purpose

### Naming

- Use descriptive names; avoid single-letter variables except in short, obvious loops
- Stay consistent — if the codebase uses `camelCase`, don't introduce `snake_case`

### Comments

- Write comments for *why*, not *what*
- Do not add comments that merely restate the code
- Keep comments current — stale comments are worse than none

### Error Handling

- Handle errors at the appropriate level; never swallow them silently
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

| Variable | Description | Required |
|----------|-------------|----------|
| _(none yet)_ | | |

Keep a `.env.example` file in the repo with all required variable names (no real values) so new contributors know what to set up.

---

## AI Assistant Guidelines

When working in this repository, AI assistants (including Claude) **must**:

1. **Read before writing** — always read a file in full before editing it; never modify code you haven't seen
2. **Stay minimal** — only change what is necessary to complete the task; avoid unsolicited refactoring or cleanup
3. **No over-engineering** — don't add error handling, abstractions, or feature flags for hypothetical future scenarios
4. **No new files unless necessary** — prefer editing existing files over creating new ones
5. **Respect existing style** — match the formatting, naming, and structure already present in the file being edited
6. **Security awareness** — never introduce command injection, XSS, SQL injection, or other OWASP Top 10 vulnerabilities
7. **Commit hygiene** — use clear, imperative-mood commit messages; reference issue numbers where relevant
8. **Ask when uncertain** — if the task is ambiguous or requires a significant architectural decision, ask before implementing
9. **No stale TODOs** — don't leave `// TODO` comments or placeholder code; complete the task or note the blocker explicitly

### Claude Code Specifics

- This project uses [Claude Code](https://claude.ai/code) for AI-assisted development
- Update this CLAUDE.md whenever the project structure, tech stack, or conventions change significantly
- Prefer small, reviewable commits over large, sweeping changes

---

## CI / CD (TBD)

Document the pipeline once configured (GitHub Actions, GitLab CI, etc.):

- Trigger conditions (push, PR, schedule)
- Steps: lint → test → build → deploy
- Required secrets / environment variables

---

## Contact / Ownership

- **Maintainer:** `ugurkesci`
- **Issues / feedback:** Open a GitHub issue on this repository
