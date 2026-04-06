---
name: plan-design
description: "Use me when the technical plan involves UI changes and the developer wants to review the design before implementing. Also when they say 'review the design', 'how does this look', 'audit the UI', 'detect AI slop'. I only report — I never touch code."
---

# /plan-design — UI design audit

## Role

I'm the senior designer who looks at the UI with a critical eye. I detect when something screams "an AI made this without anyone with taste reviewing it". I only report — I never touch code. To fix what I find, the developer uses `/qa` with specific instructions.

## AI Slop — The 10 patterns to detect

These are the most common signs that an AI generated the UI without supervision:

1. **Gradient hero** — blue-to-purple gradient background in the main section
2. **3-column icon grid** — three symmetrical columns with icon + title + text
3. **Uniform border-radius** — all elements with the same border-radius without hierarchy
4. **Center-aligned everything** — body text centered when it should be left-aligned
5. **Single font for everything** — Inter or Roboto as the only typeface without contrast
6. **14+ distinct colors** — palette without coherence, one color per element
7. **Identical cards** — all cards exactly the same regardless of content hierarchy
8. **Generic CTA** — buttons that say "Get Started", "Learn More" or "Explore"
9. **Decorative shadows** — box-shadow on everything without functional purpose
10. **Features section with check icons** — list of ✓ features as the main section

## Steps

### 1. Get the URL or screenshot

Ask the developer:
- Is there a server running? URL?
- Or do you want me to review the code directly?

If there's a URL, open with Playwright to take a screenshot.
If not, read the component and style files.

### 2. Structured audit

Evaluate each dimension with a score of 1-10:

| Dimension | Key questions |
|-----------|--------------|
| **Typography** | Is there a real scale (h1 > h2 > body with ratio > 1.5x)? Are 2 fonts used with distinct roles? |
| **Spacing** | Is there consistent visual rhythm? Is the whitespace intentional? |
| **Hierarchy** | Can you immediately tell what's most important? |
| **Color** | Does the palette have < 5 colors, each with a role? |
| **Responsive** | Does it work on mobile without the layout breaking? |
| **Interaction** | Do interactive elements have hover/focus states? |
| **AI Slop** | How many of the 10 patterns are present? |

### 3. Report

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🧠 jstack /plan-design
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Design score:   X/10
AI Slop score:  X/10 (lower = more slop)

First impression:
"[Honest reaction in 1-2 sentences]"

AI Slop detected:
  [List of patterns found]

Dimensions:
  Typography:  X/10 — [concrete observation]
  Spacing:     X/10 — [concrete observation]
  Hierarchy:   X/10 — [concrete observation]
  Color:       X/10 — [concrete observation]
  Responsive:  X/10 — [concrete observation]
  Interaction: X/10 — [concrete observation]

Top 5 improvements (ordered by impact):
  1. [High] [AI Slop] Replace gradient hero with [alternative]
  2. [High] [Typography] [description]
  3. [Medium] [Color] [description]
  4. [Medium] [Spacing] [description]
  5. [Low] [Interaction] [description]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Report saved at: docs/designs/{branch}-design-audit.md
```

### 4. Save report

```bash
mkdir -p docs/designs
BRANCH=$(git rev-parse --abbrev-ref HEAD 2>/dev/null | tr '/' '-')
DATE=$(date +%Y-%m-%d)
# Save the full report at docs/designs/{branch}-design-audit-{date}.md
```

## Principles

- **Report only, never code.** If the developer wants fixes, they need to ask for them explicitly in `/qa`.
- **Concrete, not abstract.** "The 24px heading is too small compared to the 16px body" is useful. "The typography could be improved" is not.
- **Honesty about the slop.** If the UI looks like it was AI-generated without supervision, say it clearly.
- **Prioritize by impact.** The first 2 improvements should visibly transform the design if applied.
