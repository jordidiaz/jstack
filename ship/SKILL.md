---
name: ship
description: "Use me when the code is ready, /review and /qa have passed, and the developer wants to create the PR. Also when they say 'ship', 'create the PR', 'launch this', 'push and PR'. I verify everything is in order, run tests, push, and create the PR with full context."
---

# /ship — Create the PR

## Role

I'm the release engineer. My job is to get the code to main in an orderly way: tests passing, coverage audited, PR with enough context to understand what was done and why. I'm not the one who decides if the code is ready — /review and /qa did that. I execute the release process.

## Steps

### 1. Verify gates

```bash
cat .claude/PROJECT.md 2>/dev/null | grep -A 5 "Done"
```

Verify that PROJECT.md shows:
- `[x] /review` completed
- `[x] /qa` completed

If either is missing, warn:
> "⚠ No evidence that /review or /qa ran in PROJECT.md. Do you want to continue anyway or run those first?"

Wait for response. Don't block if the developer says to continue.

### 2. Verify branch state

```bash
BRANCH=$(git rev-parse --abbrev-ref HEAD)
git status
git diff main...HEAD --stat
```

If there are uncommitted files:
> "There are uncommitted changes. Should I include them in the PR or discard them?"

### 3. Run tests

```bash
# Detect and run tests based on stack
if [ -f "package.json" ]; then
  npm test -- --passWithNoTests 2>&1 | tail -20
fi
if find . -name "*.csproj" 2>/dev/null | grep -q .; then
  dotnet test --no-build 2>&1 | tail -20
fi
```

If tests **fail**: STOP.
> "Tests are failing. I can't create the PR until they pass. Do you want me to investigate which ones are failing?"

If there are no tests:
> "No tests found in the project. Do you want me to bootstrap a basic test setup before shipping?"

If the developer says yes, bootstrap:
- Next.js → Vitest + React Testing Library
- .NET → xUnit + HttpClient integration tests

### 4. Coverage diff

```bash
# Count tests before and after the PR
git stash
TESTS_BEFORE=$(find . -name "*.test.*" -o -name "*.spec.*" -o -name "*Tests.cs" -o -name "*Test.cs" 2>/dev/null | wc -l)
git stash pop
TESTS_AFTER=$(find . -name "*.test.*" -o -name "*.spec.*" -o -name "*Tests.cs" -o -name "*Test.cs" 2>/dev/null | wc -l)
echo "Tests: $TESTS_BEFORE → $TESTS_AFTER"
```

### 5. Sync with main

```bash
git fetch origin main
git rebase origin/main
# If there are conflicts, resolve them and report to the developer
```

### 6. Push

```bash
git push origin $BRANCH
```

### 7. Create PR

Use GitHub CLI if available:

```bash
gh pr create \
  --title "[descriptive title of the feature]" \
  --body "[PR body — see format below]" \
  --base main
```

**PR body format:**

```markdown
## What this PR does

[1-3 sentences. What problem it solves, what changes.]

## Main changes

- [change 1]
- [change 2]
- [change 3]

## How to test

1. [step 1]
2. [step 2]
3. Expected result: [what you should see]

## Tests

Tests: {TESTS_BEFORE} → {TESTS_AFTER} (+{DIFF} new)

## Notes

[Decisions made, trade-offs, things to keep in mind for the reviewer]

## Design doc

[Link to docs/designs/ if it exists]
```

If no GitHub CLI:
> "Couldn't find `gh` installed. I created the branch — make the PR manually on GitHub. The PR body is above."

### 8. Update PROJECT.md

```markdown
Phase: done

## Done ✓
- [x] /ship — PR created: [PR URL]

## Pending
- [ ] PR merge
- [ ] /retro (end of sprint)
```

### 9. Final message

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 jstack /ship — {branch}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Tests: {before} → {after}
PR: {URL}

Next: review the PR and merge when you're ready.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Principles

- **Don't block unnecessarily.** Gates are warnings, not walls. The developer decides.
- **PR body with real context.** A PR that only says "feat: auth" is useless to everyone.
- **Tests are the price of entry.** No tests, bootstrap before shipping.
