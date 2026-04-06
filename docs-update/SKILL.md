---
name: docs-update
description: "Use me after shipping to update the project documentation. When the developer says 'update the docs', 'the README is outdated', or after a successful /ship. I read what changed in the PR and update the README, CHANGELOG, and any docs that have become stale."
---

# /docs-update — Update documentation

## Role

I'm the technical writer who closes the loop. Every shipped feature leaves docs outdated: the README that doesn't mention the new functionality, the CHANGELOG without an entry, the API examples that are no longer correct. I find it and fix it.

## Steps

### 1. Read what changed

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null)
git diff main...HEAD --name-only 2>/dev/null
git diff main...HEAD -- "*.md" 2>/dev/null
cat .claude/PROJECT.md 2>/dev/null
```

### 2. Find docs to update

```bash
# List all documentation files
find . -name "*.md" -not -path "*/node_modules/*" \
       -not -path "*/.git/*" \
       -not -path "*/docs/designs/*" \
       -not -path "*/docs/plans/*" \
       2>/dev/null
```

### 3. Review each relevant doc

For each `.md` file found, check:

- Does it mention functionality that changed?
- Does it have installation/usage instructions that changed?
- Are the code examples still valid?
- Is the described folder structure still correct?

### 4. Update docs

**README.md** — always review:
- Listed features
- Setup instructions
- Usage examples
- Project structure

**CHANGELOG.md** — add entry if it exists:

```markdown
## [Unreleased] / {date}

### Added
- [New feature]

### Changed
- [Behavior that changed]

### Fixed
- [Bug that was fixed]
```

If CHANGELOG.md doesn't exist, create it with a basic format.

**Other docs** — update only if there are real changes. Don't touch if nothing relevant changed.

### 5. Commit

```bash
git add -A "*.md"
git commit -m "docs: update documentation post-ship {branch}"
```

### 6. Report

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 jstack /docs-update
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Docs reviewed: X
Docs updated: Y
  - [list of modified files]
Docs unchanged: Z
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Principles

- **Only update what changed.** Don't rewrite docs just to rewrite them.
- **Code examples must work.** If there are examples, verify they're valid against the current code.
- **CHANGELOG is for humans.** Write in plain language, not in commit message names.
