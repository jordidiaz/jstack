---
name: freeze
description: "Use me when the developer wants to restrict edits to a single directory. 'Freeze src/', 'only touch the frontend', 'don't touch the DB'. Useful when debugging something specific and you don't want the agent to accidentally touch other parts of the code."
---

# /freeze — Lock edits outside a directory

## What I do

When active with a specified directory, I block any attempt to edit, create, or delete files **outside** that directory. If something requires touching a file outside the scope, I pause and ask.

## Usage

```
/freeze src/components/
/freeze app/api/
/freeze backend/
```

## While freeze is active

Before any edit to a file:
1. Check if it's inside the frozen directory
2. If inside → proceed normally
3. If outside → STOP

```
🔒 FREEZE ACTIVE: {directory}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Attempted to edit: {file}
This file is outside the frozen scope.

Do you want me to edit it anyway? (yes/no)
Or use /unfreeze to exit freeze mode.
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

## Automatic exceptions

Always allowed even with freeze active:
- Reading any file (read-only, no writing)
- Editing files in `/tmp`
- Running commands that don't modify files

## State

When activating, report:
```
🔒 Freeze activated: I can only edit files in {directory}
Use /unfreeze to deactivate it.
```
