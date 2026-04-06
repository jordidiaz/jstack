# jstack

Skills framework for Claude Code. Turns Claude Code into a virtual development team for weekend projects: think through the problem, plan the implementation, review the code, test in a real browser, and create the PR.

The core idea: **the agent remembers where you left off**. You can close Claude Code on Sunday and resume the following Saturday with `/status` without having to remember anything.

## Skills

| Skill | Role | What it does |
|-------|------|--------------|
| `/status` | Memory | Reads the project state and tells you exactly where you left off |
| `/think` | YC Partner | Defines the problem with 6 questions before writing a single line of code |
| `/plan` | Tech Lead | Architecture, ASCII diagrams, edge cases, test matrix |
| `/review` | Staff Engineer | Finds bugs that pass CI, auto-fixes the obvious ones |
| `/qa` | QA Lead | Playwright in a real browser, atomic commits per bug |
| `/debug` | Debugger | Root cause analysis, never touches code without understanding |
| `/ship` | Release Eng | Tests, push, PR with full context |
| `/docs-update` | Tech Writer | Updates README and CHANGELOG post-ship |
| `/retro` | Eng Manager | Real sprint metrics, no fluff |
| `/careful` | Safety | Warns before destructive commands |
| `/freeze` | Safety | Restricts edits to one directory |
| `/unfreeze` | Safety | Deactivates freeze |

## The sprint flow

```
/status          ← session start: where am I?
/think           ← Gate 1: define and approve the problem
/plan            ← technical plan with diagrams
[Claude Code]    ← implement the plan
/review          ← Gate 2: review and approve before shipping
/qa              ← test in a real browser
/ship            ← PR
/docs-update     ← update docs
/retro           ← end of sprint: what was accomplished
```

You show up at **Gate 1** (approve the design doc) and **Gate 2** (approve the review/qa findings). Everything else is autonomous.

## Installation

### 1. Clone into your global skills folder

```bash
git clone https://github.com/jordidiaz/jstack ~/.claude/skills/jstack
```

### 2. Add to your project

In your project directory:

```bash
# Copy the CLAUDE.md template
cp ~/.claude/skills/jstack/CLAUDE.md.template CLAUDE.md

# Create the state folder
mkdir -p .claude
cp ~/.claude/skills/jstack/PROJECT.md.template .claude/PROJECT.md

# Create DECISIONS.md
echo "# Project decisions\n" > DECISIONS.md

# Edit PROJECT.md with your project name
# Edit CLAUDE.md with your stack and dev commands
```

### 3. Start

```
/status
```

For a new project:
```
/think
```

## Project structure with jstack

```
my-project/
├── .claude/
│   └── PROJECT.md          ← project state (committed)
├── docs/
│   ├── designs/            ← design docs from /think
│   └── plans/              ← technical plans from /plan
├── CLAUDE.md               ← activates jstack in this repo
├── DECISIONS.md            ← append-only decisions log
└── [project code]
```

## Supported stack

- **Next.js / TypeScript / React** — specific checklists in /review and /plan
- **.NET / C#** — specific checklists in /review and /plan
- **Playwright** — required for /qa

## Philosophy

jstack is not a copilot. It doesn't suggest code as you type. It's the process around the code: thinking clearly before starting, reviewing before shipping, and remembering where you left off next session.

The difference between a weekend project you finish and one you abandon is usually whether you have clarity on what's left and where to continue. jstack maintains that clarity.

MIT License
