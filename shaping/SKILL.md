---
name: shaping
description: Guides collaborative shaping by iterating on problem definition (requirements) and solution options. Use when defining a problem, exploring alternatives, or refining a selected option before implementation.
---

# Shaping Methodology

A structured approach for collaboratively defining problems and exploring solution options.

---

## Multi-Level Consistency (Critical)

Shaping produces documents at different levels of abstraction. **Truth must stay consistent across all levels.**

### The Document Hierarchy (high to low)

1. **Shaping doc** — ground truth for R's, options, parts, coverage checks
2. **Slices doc** — ground truth for slice definitions, breadboards
3. **Individual slice plans** (V1-plan, etc.) — ground truth for implementation details

### The Principle

Each level summarizes or provides a view into the level(s) below it. Lower levels contain more detail; higher levels are designed views that help acquire context quickly.

**Changes ripple in both directions:**

- **Change at high level → trickles down:** If you change the shaping doc's parts table, update the slices doc too.
- **Change at low level → trickles up:** If a slice plan reveals a new approach or changes the scope of a slice, the Slices doc and shaping doc must reflect that.

### The Practice

Whenever making a change:

1. **Identify which level you're touching**
2. **Ask: "Does this affect documents above or below?"**
3. **Update all affected levels in the same operation**
4. **Never let documents drift out of sync**

The system only works if the levels are consistent with each other.

---

## Starting a Session

When kicking off a new shaping session, offer the user both entry points:

- **Start from R (Requirements)** — Describe the problem, pain points, or constraints. Build up requirements and let options emerge.
- **Start from S (Options)** — Sketch a solution already in mind. Capture it as an option and extract requirements as you go.

There is no required order. Shaping is iterative — R and S inform each other throughout.

## Working with an Existing Shaping Doc

When the shaping doc already has a selected option:

1. **Display the coverage check for the selected option only** — Show R × [selected option] (e.g., R × F), not all options
2. **Summarize what is unsolved** — Call out any requirements that are Undecided, or where the selected option has ❌

This gives the user immediate context on where the shaping stands and what needs attention.

---

## Core Concepts

### R: Requirements
A numbered set defining the problem space.

- **R0, R1, R2...** are members of the requirements set
- Requirements are negotiated collaboratively - not filled in automatically
- Track status: Core goal, Undecided, Leaning yes/no, Must-have, Nice-to-have, Out
- Requirements extracted from coverage checks should be made standalone (not dependent on any specific option)
- **R states what's needed, not what's satisfied** — satisfaction is always shown in a coverage check (R × S)
- **Chunking policy:** Never have more than 9 top-level requirements. When R exceeds 9, group related requirements into chunks with sub-requirements (R3.1, R3.2, etc.) so the top level stays at 9 or fewer. This keeps the requirements scannable and forces meaningful grouping.

### S: Options (Solution Options)
Letters represent mutually exclusive solution approaches.

- **A, B, C...** are top-level options (you pick one)
- **C1, C2, C3...** are components/parts of Option C (they combine)
- **C3-A, C3-B, C3-C...** are alternative approaches to component C3 (you pick one)

### Option Titles
Give options a short descriptive title that characterizes the approach. Display the title when showing the option:

```markdown
## E: Modify CUR in place to follow S-CUR

| Part | Approach |
|------|----------|
| E1 | ... |
```

Good titles capture the essence of the approach in a few words:
- ✅ "E: Modify CUR in place to follow S-CUR"
- ✅ "C: Two data sources with hybrid pagination"
- ❌ "E: The solution" (too vague)
- ❌ "E: Add search to widget-grid by swapping..." (too long)

### Notation Hierarchy

| Level | Notation | Meaning | Relationship |
|-------|----------|---------|--------------|
| Requirements | R0, R1, R2... | Problem constraints | Members of set R |
| Options | A, B, C... | Solution options | Pick one from S |
| Components | C1, C2, C3... | Parts of an option | Combine within option |
| Alternatives | C3-A, C3-B... | Approaches to a component | Pick one per component |

### Notation Persistence
Keep notation throughout as an audit trail. When finalizing, compose new options by referencing prior components (e.g., "Option E = C1 + C2 + C3-A").

## Phases

Shaping moves through two phases:

```
Shaping → Slicing
```

| Phase | Purpose | Output |
|-------|---------|--------|
| **Shaping** | Explore the problem and solution space, select and detail an option | Shaping doc with R, options, coverage checks, See-Do tables, Breadboard |
| **Slicing** | Break down for implementation | Implementation steps with demo-able UI |

### Phase Transition

**Shaping → Slicing** happens when:
- An option is selected (passes coverage check, feels right)
- The option has been screen mapped (See-Do tables) and breadboarded (Actions & Flows)
- We need to plan implementation order

You can't slice without screen mapping and breadboarding (See-Do tables + Contexts/Actions/Flows).

---

## Coverage Check (Decision Matrix)

THE coverage check is the single table comparing all options against all requirements. Requirements are rows, options are columns. This is how we decide which option to pursue.

### Format

```markdown
## Coverage Check

| Req | Requirement | Status | A | B | C |
|-----|-------------|--------|---|---|---|
| R0 | Make items searchable from index page | Core goal | ✅ | ✅ | ✅ |
| R1 | State survives page refresh | Must-have | ✅ | ❌ | ✅ |
| R2 | Back button restores state | Must-have | ❌ | ✅ | ✅ |

**Notes:**
- A fails R2: [brief explanation]
- B fails R1: [brief explanation]
```

### Conventions
- **Always show full requirement text** — never abbreviate or summarize requirements in coverage checks
- **Coverage check is BINARY** — Use ✅ for pass, ❌ for fail. No other values.
- **Option columns contain only ✅ or ❌** — no inline commentary; explanations go in Notes section
- **Never use ⚠️ or other symbols in coverage check** — ⚠️ belongs only in the Parts table's open questions column
- Keep notes minimal — just explain failures

### Comparing Alternatives Within a Component

When comparing alternatives for a specific component (e.g., C3-A vs C3-B), use the same format but scoped to that component (replace "Coverage Check" with the component name):

```markdown
## C3: Component Name

| Req | Requirement | Status | C3-A | C3-B |
|-----|-------------|--------|------|------|
| R1 | State survives page refresh | Must-have | ✅ | ❌ |
| R2 | Back button restores state | Must-have | ✅ | ✅ |
```

### Missing Requirements
If an option passes all checks but still feels wrong, there's a missing requirement. Articulate the implicit constraint as a new R, then re-run the coverage check.

### Quick Coverage Check

A separate tool from the standard coverage check, used when working at a high level with chunked requirements and early-stage options where most approaches are still ⚠️. Use when explicitly requested.

The quick coverage check has two columns per option instead of one:

- **Addressed?** — Does some part of the option seem to speak to this requirement at a high level?
- **Answered?** — Can you trace the concrete how? Is the approach actually spelled out?

**Format:**

```markdown
## Quick Coverage Check: R × A

| Req | Requirement | Addressed? | Answered? |
|-----|-------------|:----------:|:---------:|
| R0 | Core goal description | ✅ | ❌ |
| R1 | Guided workflow | ✅ | ❌ |
| R2 | Agent boundary | ⚠️ | ❌ |
```

**Conventions:**
- Only show top-level requirements (R0, R1, R2...), not sub-requirements
- **No notes column** — keep the table narrow and scannable
- Use ✅ (yes), ⚠️ (partially), ❌ (no) for Addressed
- Use ✅ (yes) or ❌ (no) for Answered
- Follow the quick coverage check with a separate **Gaps** table listing specific missing parts and their related sub-requirements

## Possible Actions

These can happen in any order:

- **Populate R** - Gather requirements as they emerge
- **Sketch an option** - Propose a high-level approach (A, B, C...)
- **Detail (components)** - Break an option into components (B1, B2...)
- **Screen map (UI elements)** - Expand a selected option into See-Do tables and high-level behavior flows
- **Explore alternatives** - For a component, identify options (C3-A, C3-B...)
- **Check coverage** - Build a coverage check (decision matrix) playing options against R
- **Extract Rs** - When coverage checks reveal implicit requirements, add them to R as standalone items
- **Breadboard** - Map the system to understand where changes happen and translate the screen map into an Actions Reference and sequence diagram
- **Spike** - Investigate unknowns to identify concrete steps needed
- **Decide** - Pick alternatives, compose final solution
- **Slice** - Break a breadboarded option into implementation steps

## Communication

### Show Full Tables

When displaying R (requirements) or any S (options), always show every row — never summarize or abbreviate. The full table is the artifact; partial views lose information and break the collaborative process.

- Show all requirements, even if many
- Show all option components, including sub-parts (E1.1, E1.2...)
- Show all alternatives in coverage checks

### Why This Matters

Shaping is collaborative negotiation. The user needs to see the complete picture to:
- Spot missing requirements
- Notice inconsistencies
- Make informed decisions
- Track what's been decided

Summaries hide detail and shift control away from the user.

### Mark Changes with 🟡

When re-rendering a requirements table or option table after making changes, mark every changed or added line with a 🟡 so the user can instantly spot what's different. Place the 🟡 at the start of the changed cell content. This makes iterative refinement easy to follow — the user should never have to diff the table mentally.

## Spikes

A spike is an investigation task to learn how the existing system works and what concrete steps are needed to implement a component. Use spikes when there's uncertainty about mechanics or feasibility.

### File Management

**Always create spikes in their own file** (e.g., `spike.md` or `spike-[topic].md`). Spikes are standalone investigation documents that may be shared or worked on independently from the shaping doc.

### Purpose

- Learn how the existing system works in the relevant area
- Identify **what we would need to do** to achieve a result
- Enable informed decisions about whether to proceed
- Not about effort — effort is implicit in the steps themselves
- **Investigate before proposing** — discover what already exists; you may find the system already satisfies requirements

### Structure

```markdown
## [Component] Spike: [Title]

### Context
Why we need this investigation. What problem we're solving.

### Goal
What we're trying to learn or identify.

### Questions

| # | Question |
|---|----------|
| **X1-Q1** | Specific question about mechanics |
| **X1-Q2** | Another specific question |

### Acceptance
Spike is complete when all questions are answered and we can describe [the understanding we'll have].
```

### Acceptance Guidelines

Acceptance describes the **information/understanding** we'll have, not a conclusion or decision:

- ✅ "...we can describe how users set their language and where non-English titles appear"
- ✅ "...we can describe the steps to implement [component]"
- ❌ "...we can answer whether this is a blocker" (that's a decision, not information)
- ❌ "...we can decide if we should proceed" (decision comes after the spike)

The spike gathers information; decisions are made afterward based on that information.

### Question Guidelines

Good spike questions ask about mechanics:
- "Where is the [X] logic?"
- "What changes are needed to [achieve Y]?"
- "How do we [perform Z]?"
- "Are there constraints that affect [approach]?"

Avoid:
- Effort estimates ("How long will this take?")
- Vague questions ("Is this hard?")
- Yes/no questions that don't reveal mechanics

## Screen Mapping and Breadboarding

Use the `/screen-mapping` and `/breadboarding` skills to detail an option into concrete screens, actions, and system flows.

- **Screen Mapping (`/screen-mapping`)**: Translates a shaped option into concrete user interface requirements. Produces See-Do tables (what users see and can do) per screen.
- **Breadboarding (`/breadboarding`)**: Maps the UI to the underlying architecture. Produces a Context list, an Actions Reference, and a Sequence Diagram showing the control and data flow.

Invoke these when you need to:
- Map existing code to understand where changes land
- Translate a high-level option into concrete screens and actions
- Reveal independent parts that can be built separately

### See-Do Tables Are the UX Source of Truth

The See-Do tables (one per screen) define the user experience. The Breadboard (Actions & Flows) explains the logic and architecture behind those actions.

When receiving feedback on a design:
1. **First** — update the See-Do tables (add/remove See items and Do actions)
2. **Then** — update the Breadboard (Actions Reference and Sequence Diagrams) to match

Changes always flow from the UX definition (See-Do) to the architectural mapping (Breadboard).

### CURRENT as Reserved Option Name

Use **CURRENT** to describe the existing system. This provides a baseline for understanding where proposed changes fit.

## Option Components

### Open Questions (⚠️)

An approach can be described at a high level without being concretely understood. The **Flag** column tracks this:

| Part | Approach | Flag |
|------|----------|:----:|
| **F1** | Create widget (component, def, register) | |
| **F2** | Magic authentication handler | ⚠️ |

- **Empty** = approach is understood — we know concretely how to build it
- **⚠️** = open question — we've described WHAT but don't yet know HOW

**Why open questions fail the coverage check:**

1. **✅ is a claim of knowledge** — it means "we know how this option satisfies this requirement"
2. **Satisfaction requires an approach** — some part that concretely delivers the requirement
3. **A flag means we don't know how** — we've described what we want, not how to build it
4. **You can't claim what you don't know** — therefore it must be ❌

Coverage check is always binary — ✅ or ❌ only. There is no third state. An open question is a failure until resolved.

This distinguishes "we have a sketch" from "we actually know how to do this." Early options (A, B, C) often have many flagged parts — that's fine for exploration. But a selected option should have no flags (all ❌ resolved), or explicit spikes to resolve them.

### Parts Must Be Approaches

Option components describe what we BUILD or CHANGE — not intentions or constraints:

- ✅ "Route `childType === 'letter'` to `typesenseService.rawSearch()`" (concrete approach)
- ❌ "Types unchanged" (constraint — belongs in R)

### Avoid Tautologies Between R and S

**R** states the need/constraint (what outcome). **S** describes the approach (how to achieve it). If they say the same thing, the option component isn't adding information.

- ❌ R17: "Admins can bulk request members to sign" + C6.3: "Admin can bulk request members to sign"
- ✅ R17: "Admins can bring existing members into waiver tracking" + C6.3: "Bulk request UI with member filters, creates WaiverRequests in batch"

The requirement describes the capability needed. The option component describes the concrete approach that provides it. If you find yourself copying text from R into S, stop — the component should add specificity about *how*.

### Parts Should Be Implementation Steps

Avoid horizontal layers like "Data model" that group all tables together. Instead, co-locate data models with the features they support:

- ❌ **B4: Data model** — Waivers table, WaiverSignatures table, WaiverRequests table
- ✅ **B1: Signing handler** — includes WaiverSignatures table + handler logic
- ✅ **B5: Request tracking** — includes WaiverRequests table + tracking logic

Each part should be an implementation step containing the approach AND the data it needs.

### Extract Shared Logic

When the same logic appears in multiple parts, extract it as a standalone part that others reference:

- ❌ Duplicating "Signing handler: create WaiverSignature + set boolean" in B1 and B2
- ✅ Extract as **B1: Signing handler**, then B2 and B3 say "→ calls B1"

```markdown
| **B1** | **Signing handler** |
| B1.1 | WaiverSignatures table: memberId, waiverId, signedAt |
| B1.2 | Handler: create WaiverSignature + set member.waiverUpToDate = true |
| **B2** | **Self-serve signing** |
| B2 | Self-serve purchase: click to sign inline → calls B1 |
| **B3** | **POS signing via email** |
| B3.1 | POS purchase: send waiver email |
| B3.2 | Passwordless link to sign → calls B1 |
```

### Hierarchical Notation

Start with flat notation (E1, E2, E3...). Only introduce hierarchy (E1.1, E1.2...) when:

- There are too many parts to easily understand
- You're reaching a conclusion and want to show structure
- Grouping related approaches aids communication

| Notation | Meaning |
|----------|---------|
| E1 | Top-level component of Option E |
| E1.1, E1.2 | Sub-parts of E1 (add later if needed) |

Example of hierarchical grouping (used when option is mature):

| Part | Approach |
|------|----------|
| **E1** | **Swap data source** |
| E1.1 | Modify backend indexer |
| E1.2 | Route letters to new service |
| E1.3 | Route posts to new service |
| **E2** | **Add search input** |
| E2.1 | Add input with debounce |

## Detailing an Option

When an option is selected, you can expand it into concrete elements. This is called **detailing**.

### Notation

Use "Detail X" (not a new letter) to show this is a breakdown of Option X, not an alternative:

```markdown
## A: First approach
(option table)

## B: Second approach
(option table)

## Detail B: Concrete elements
(element tables + connections)
```

### What Detailing Produces

Use the `/screen-mapping` and `/breadboarding` skills to produce:
- **See-Do tables (`/screen-mapping`)** — Per screen: what users see (data, labels, status) and what they can do (buttons, inputs, links → destinations)
- **Breadboard (`/breadboarding`)** — Context List, Actions Reference, and Sequence Diagram tracing the logic across the full flow.

### Why "Detail X" Not "C"

Option letters (A, B, C...) are **mutually exclusive alternatives** — you pick one. Detailing is not an alternative; it's a deeper breakdown of the selected option. Using a new letter would incorrectly suggest it's a sibling option.

```
A, B, C = alternatives (pick one)
Detail B = expansion of B (not a choice)
```

## Additional References

- **Document roles, frontmatter, and file management**: See [DOCUMENTS.md](DOCUMENTS.md)
- **Worked shaping example**: See [EXAMPLES.md](EXAMPLES.md)
