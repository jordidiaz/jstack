---
name: status
description: "Use me at the start of each work session, or when the developer writes 'where am I', 'what's left', 'resume', 'status' or any variation. I read the project state and tell the developer exactly where they left off, what to do next, and whether there are any blockers."
---

# /status — Resume the project

## What I do

I read `.claude/PROJECT.md` and `DECISIONS.md` from the current project and generate a concise session report. The developer doesn't have to remember anything — I remember it for them.

## Steps

### 1. Read state

```bash
cat .claude/PROJECT.md 2>/dev/null || echo "NO_PROJECT_FILE"
cat DECISIONS.md 2>/dev/null || echo "NO_DECISIONS_FILE"
git rev-parse --abbrev-ref HEAD 2>/dev/null
git log --oneline -5 2>/dev/null
```

If `.claude/PROJECT.md` doesn't exist, tell the developer:
> "No jstack state found in this project. Do you want to initialize it with `/think`?"

### 2. Generate session report

Always display in this format, never inventing anything that isn't in PROJECT.md:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 jstack — resuming project
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Project:      [name]
Branch:       [current branch]
Last session: [date from PROJECT.md]
Sprint:       [current sprint goal]

✓ Done
  [list of completed items]

→ Pending
  [list of what's left, in order]

⚠ Blockers
  [if any, what they are — omit this section if none]

Relevant decisions
  [the 2-3 most recent decisions from DECISIONS.md]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Suggested next step: /[skill]
```

### 3. Suggest the next command

Based on the current phase in PROJECT.md:

| Phase in PROJECT.md | Suggest |
|---|---|
| `thinking` | `/think` — continue or revise the design doc |
| `planning` | `/plan` — generate the technical plan |
| `building` | nothing — the developer uses Claude Code directly |
| `reviewing` | `/review` — review the branch code |
| `testing` | `/qa` — run QA with Playwright |
| `shipping` | `/ship` — create the PR |
| `done` | `/retro` if it's end of sprint, or `/think` for the next sprint |

## Notes

- Never invent state. If PROJECT.md doesn't have something, say "not recorded".
- If there are blockers, highlight them clearly with ⚠.
- Be concise. The developer wants to know where they are in 10 seconds, not read an essay.
