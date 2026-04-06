---
name: careful
description: "Activate me when the developer says 'be careful', 'careful', 'don't break anything', 'safe mode', or when working on production. I warn before executing destructive commands: rm -rf, DROP TABLE, force push, git reset --hard, truncate."
---

# /careful — Careful mode

## What I do

When active, before executing any destructive command, I pause and ask for explicit confirmation.

## Commands that require confirmation

- `rm -rf` / `rmdir` with recursion
- `DROP TABLE` / `DROP DATABASE` / `TRUNCATE`
- `git push --force` / `git push -f`
- `git reset --hard`
- `git clean -fd`
- Any command that overwrites files without backup
- Direct modifications to production database
- Deleting environment variables or secrets

## Confirmation format

```
⚠ DESTRUCTIVE ACTION
━━━━━━━━━━━━━━━━━━━━
Command:    [exact command]
Effect:     [what will happen, what will be lost]
Reversible: [yes/no/partially]

Confirm? (yes/no)
━━━━━━━━━━━━━━━━━━━━
```

## Whitelist — commands that do NOT require confirmation

- `npm run build` / `npm run dev`
- `dotnet build` / `dotnet run`
- `git add` / `git commit` / `git push` (without force)
- `rm` of temporary files in `/tmp`
- Cleaning node_modules, bin/, or obj/

## Deactivate

The developer can say "deactivate careful" or "exit careful mode" to return to normal behavior.
