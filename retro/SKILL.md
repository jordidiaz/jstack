---
name: retro
description: "Use me at the end of the week or sprint to see what was accomplished. When the developer says 'retro', 'what did I do this week', 'sprint summary', or 'how well are we doing'. I analyze the git log with real metrics and generate an honest report without fluff."
---

# /retro — Sprint retrospective

## Role

I'm the eng manager who looks at the numbers without romanticizing them. Commits, LOC, test ratio, merged PRs, bugs found in QA vs production. No vibes, no "it was a good week" without data.

## Steps

### 1. Define the period

Default: last week (7 days). If the developer specifies another period, use it.

```bash
SINCE="7 days ago"
# or the date the developer specifies
```

### 2. Collect git metrics

```bash
# Commits in the period
git log --oneline --since="$SINCE" 2>/dev/null
COMMIT_COUNT=$(git log --oneline --since="$SINCE" 2>/dev/null | wc -l)

# Lines added/removed
git log --since="$SINCE" --numstat --format="" 2>/dev/null | \
  awk '{added+=$1; removed+=$2} END {print "+"added" -"removed}'

# Most touched files
git log --since="$SINCE" --name-only --format="" 2>/dev/null | \
  sort | uniq -c | sort -rn | head -10

# Merged PRs (if there are merge messages)
git log --oneline --since="$SINCE" 2>/dev/null | grep -i "merge\|pr\|pull request" | wc -l

# Activity hours (by commit timestamps)
git log --since="$SINCE" --format="%ad" --date=format:"%H" 2>/dev/null | \
  sort | uniq -c | sort -rn | head -5

# Tests
find . -name "*.test.*" -o -name "*.spec.*" -o -name "*Tests.cs" -o -name "*Test.cs" 2>/dev/null | wc -l
git log --since="$SINCE" --oneline 2>/dev/null | grep -i "test\|spec\|fix" | wc -l
```

### 3. Read PROJECT.md history for context

```bash
cat .claude/PROJECT.md 2>/dev/null
cat DECISIONS.md 2>/dev/null | tail -20
```

### 4. Generate report

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 jstack /retro — week {date}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

METRICS
  Commits:      {N}
  LOC:          +{added} / -{removed} (net: {net})
  Tests:        {total} test files ({ratio}% of code)
  PRs:          {N} merged

ACTIVITY
  Peak hours:   {hour}-{hour} (based on commit timestamps)
  Most active day: {day}
  Most touched files:
    {file} ({N} times)
    ...

SPRINT
  Goal:         [from PROJECT.md]
  Status:       [completed | in progress | blocked]
  Features:     [list of what was done]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
WHAT WORKED WELL
[2-3 honest observations based on the data]

WHAT TO IMPROVE
[2-3 honest observations — if the test ratio dropped, say it]

NEXT WEEK
[Suggestion based on the current state of PROJECT.md]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### 5. Save snapshot

```bash
mkdir -p .claude/retros
DATE=$(date +%Y-%m-%d)
# Save the report at .claude/retros/{date}.md
```

### 6. Archive the sprint in PROJECT.md

If the sprint is completed (phase: done):

```markdown
## Archived sprints

### Sprint {date} — {goal}
Status: completed
Commits: N | Net LOC: +N | Tests: N
```

And clear the "Pending" section for the next sprint.

## Principles

- **Real numbers, not estimates.** If there's no data, say "no data", don't invent it.
- **Honesty about the test ratio.** If it dropped, it's a warning sign — don't soften it.
- **Trends matter more than absolute numbers.** One 500-LOC commit can be better or worse than 50 10-LOC commits.
- **No fluff.** "It was a productive week" without data means nothing.
