# Shaping Documents

## Contents

- Document roles
- Document lifecycle
- Capturing source material
- File management
- Frontmatter
- Slicing handoff

## Document Roles

Shaping produces up to four documents. Each has a distinct role:

| Document | Contains | Purpose |
|----------|----------|---------|
| **Frame** | Source, Problem, Outcome | The "why" — concise, stakeholder-level |
| **Shaping doc** | Requirements, Options (CURRENT/A/B/...), See-Do tables, Breadboard, Coverage Check | The working document — exploration and iteration happen here |
| **Slices doc** | Slice details, per-slice Actions & Flows, sliced breadboard sequence diagram | The implementation plan — how to build incrementally |
| **Slice plans** | V1-plan.md, V2-plan.md, etc. | Individual implementation plans for each slice |

## Document Lifecycle

```text
Frame (problem/outcome)
    ↓
Shaping (explore, detail, breadboard)
    ↓
Slices (plan implementation)
```

**Frame** can be written first. It captures the "why" before any solution work begins. It contains:

- **Source** — Original requests, quotes, or material that prompted the work
- **Problem** — What's broken, what pain exists
- **Outcome** — What success looks like, without locking into a solution

**Shaping doc** is where active work happens. All exploration, requirements gathering, option comparison, breadboarding, and coverage checking happens here. This is the ground truth for R, options, parts, and coverage checks.

**Slices doc** is created when the selected option is breadboarded and ready to build. It contains the slice breakdown, element tables per slice, and detailed connections.

## Capturing Source Material

When the user provides source material during framing, always capture it verbatim in a `Source` section at the top of the frame document.

```markdown
## Source

> I'd like to ask again for your thoughts on a user scenario...
>
> Small reminder: at the moment, if I want to keep my country admin rights
> for Russia and Crimea while having Europe Center as my home center...

> [Additional source material added as received]

---

## Problem
...
```

Why this matters:

- The source is the ground truth; `Problem` and `Outcome` are interpretations
- It preserves context that may matter later
- It allows the team to revisit the original request if the distillation missed something
- Multiple sources can be added as they arrive during framing

Capture source when:

- The user pastes a request or quote
- The user shares an email or message from a stakeholder
- The user describes a scenario they were told about
- Any raw material materially informs the frame

## File Management

- **Shaping doc**: Update freely as you iterate; this is the working ground truth
- **Slices doc**: Create it when ready to slice; update it as slice scope clarifies
- **Slice plans**: Keep individual files such as `V1-plan.md` and `V2-plan.md` for implementation details

## Frontmatter

Every shaping document (`frame`, shaping doc, and `slices` doc) must include `shaping: true` in its YAML frontmatter. This enables tooling hooks such as ripple-check reminders that help maintain consistency across documents.

```markdown
---
shaping: true
---

# [Feature Name] — Shaping
...
```

## Keeping Documents in Sync

See **Multi-Level Consistency** in [SKILL.md](SKILL.md). Changes at any level must ripple to affected levels above and below.

## Slicing Handoff

After an option is breadboarded, slice it into implementation steps. Use the `/breadboarding` skill for the slicing process; it defines what implementation steps are, the procedure for creating them, and the visualization formats.

The flow:

1. **Parts** → high-level approaches in the option
2. **Screen Map & Breadboard** → See-Do tables per screen plus Actions and sequence diagrams
3. **Slices** → vertical increments that can each be demoed

Key principle: every implementation step must end in demo-able UI. A step without visible output is a horizontal layer, not a proper slice.

Document outputs:

- **Slices doc** — slice definitions, per-slice Actions Reference, sliced sequence diagram
- **Slice plans** — individual implementation plans such as `V1-plan.md` and `V2-plan.md`
