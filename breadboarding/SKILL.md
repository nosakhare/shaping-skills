---
name: breadboarding
description: Turn a description of how something works (or should work) into a Context list, an Actions Reference, and a sequence diagram showing the Flow. Use to map existing systems or design new ones.
---

# Breadboarding (Plain English Guide)

Breadboarding maps out a system — either one that already exists or one you're designing — by capturing three things:

- **Contexts** — the distinct screens or boundaries the user moves between (a page, a modal, the backend)
- **Actions** — every button, input, display, function, and data store involved, each given a single ID
- **Flows** — how those Actions connect and trigger each other, shown as a sequence diagram

---

## What Is It Used For?

### 1. Understanding an Existing System

Use this when you're trying to figure out how something already works — for example, why a bug is happening, or how data moves through the system.

**You bring:**
- The code (one or more repos)
- A description of the workflow to trace, written from the perspective of someone trying to make something happen (e.g. "I click Submit — what happens next?")

**You get back:**
- A Context list
- An Actions Reference
- A sequence diagram showing the Flow

If the workflow spans a frontend and a backend, make one breadboard that covers both. Label Contexts clearly so it's obvious which system each one belongs to.

### 2. Designing Something New

Use this when you've sketched out a new feature as a list of parts and need to work out the exact details — what Actions are needed and how they Flow together.

**You bring:**
- A list of parts (from your design/shaping work)
- The goal or outcome those parts are meant to achieve
- The existing system (if the new parts need to plug into it)

**You get back:**
- Same three artefacts as above

### 3. When You Have Both

Often you'll have an existing system plus some new changes. Breadboard both together — show the existing Actions and the new ones in one Actions Reference, and trace the combined Flow in one sequence diagram.

### 4. Reading a Hand-Drawn Breadboard

Sometimes breadboards are sketched on a whiteboard. The same concepts apply — Contexts, Actions, Flows — but the layout uses visual stacking instead of tables.

| Visual Element | What It Means |
|---|---|
| Coloured block at the top of a stack | A Context |
| Blocks stacked underneath | Actions belonging to that Context |
| Code blocks floating between stacks | System Actions (functions, etc.) |
| Block at the top-left of a Context | A loader — what data the Context needs to render |
| Solid arrows | Control Flow → (what triggers what) |
| Dashed arrows | Data Output ← (where results flow back) |
| Indented blocks in a different colour | Conditional branches (if/else logic) |
| `_ContextName` in a stack | A reference to another Context defined elsewhere |
| `?` or `~` prefix, or dashed border | Speculative — not confirmed yet |
| Large box around multiple stacks | A system or responsibility boundary |
| Freeform text | Notes, open questions, or context |

**Converting to the standard format:** Map each stack to its Context, list Actions top to bottom in the Actions Reference, and turn arrows into sequence diagram messages labelled with Action IDs.

---

## Core Concepts

### Contexts

A **Context** is "where you are" in the interface — a bounded situation with a specific set of Actions available. When you're in a Context, you can only interact with what's there. To do something else, you have to leave.

A Context is about the user's experience, not technical details like URLs or components.

#### The Blocking Test

The easiest way to tell if something is a new Context: **can you still interact with what's behind it?**

| Answer | Meaning |
|---|---|
| No — you're blocked | You're in a different Context |
| Yes — you can still click around | Same Context, just a local change |

#### Examples

| UI Element | Blocking? | New Context? | Why |
|---|---|---|---|
| Modal dialog | Yes | Yes | You can't click anything behind it |
| Confirmation popover | Yes | Yes | You must respond before moving on |
| Edit mode (whole screen changes) | Yes | Yes | Everything on screen is different |
| Checkbox that reveals extra fields | No | No | The rest of the screen is unchanged |
| Dropdown menu | No | No | You can click away |
| Tooltip | No | No | Just informational, doesn't block anything |

#### Local Change vs. Navigation

Ask yourself: did *everything* change, or just a small part while the rest stayed the same?

| Type | What Happens | How to Handle It |
|---|---|---|
| Local change | Only part of the UI changes | Same Context — model it as a conditional |
| Navigation | Whole screen changes, or something blocking appears | Different Context |

#### Mode-Based Contexts

If a "mode" (like Edit Mode) transforms the entire screen, treat it as a separate Context:

```
C1: CMS Page (Read Mode)
C2: CMS Page (Edit Mode)
```

The flag that switches between modes is just a navigation mechanism — don't list it as data inside either Context.

#### Three Questions for Any Button or Control

1. Where did I come from to see this?
2. Where am I right now?
3. Where do I end up if I use it?

If the answer to #3 is "everything changes" or "I can't interact with what's behind until I respond," you're navigating to a new Context.

#### Naming Contexts

| Pattern | When to Use |
|---|---|
| `C#: Page Name` | A standard page or route |
| `C#: Page Name (Mode)` | A mode-based version of a page |
| `C#: Modal Name` | A modal dialog |
| `C#: Backend` | The API or database layer |

When spanning multiple systems: `C1: Checkout Page (frontend)`, `C4: Payment API (backend)`.

#### Sub-Contexts

A **sub-context** is a defined section within a Context — useful when a Context has multiple distinct widgets or areas. Use hierarchical IDs: `C2.1`, `C2.2`, etc.

When zooming in on one sub-context, add a placeholder to show there's more on the page:
```
[... other page content ...]
```

### Actions

**Actions** are the individual pieces you can act on. Every Action gets a single ID in the format `A1`, `A2`, `A3`, …

There are two kinds:

- **Action (UI):** Anything the user sees or interacts with — buttons, inputs, text displays, spinners, scroll areas
- **Action (System):** Anything in the code that can be triggered or observed — functions, subscriptions, data stores, framework hooks

Both kinds live in the same Actions Reference. The type is noted in the definition.

### Flows

A Flow describes how Actions connect. There are two directions:

- **Control Flow →** — what an Action triggers or calls (a function call, a write to a data store, navigation to a new Context)
- **Data Output ←** — where an Action's result goes (a return value flowing back to its caller, a data store being read)

Flows are shown in a sequence diagram, not in table columns. Solid arrows represent Control Flow →. Dashed arrows represent Data Output ←.

---

## The Three Artefacts

Every breadboard produces exactly these three things.

### 1. Context List

A simple numbered list of every Context in the workflow.

| # | Context | Description |
|---|---|---|
| C1 | Search Page | Main search interface |
| C2 | Detail Page | Individual result view |
| C3 | Backend | Search service and data layer |

### 2. Actions Reference

A single bullet list where every Action — UI or System — is defined once. Each entry follows this format:

> **A#** – name: short description of what it does.

The type (UI or System) is noted in parentheses where it helps clarity.

**Example:**

- **A1** – search input (UI): text field where the user types a query; triggers A2 on each keystroke
- **A2** – `activeQuery.next()` (System): pushes the new query value into the observable stream; triggers A3
- **A3** – `activeQuery` subscription (System): observes the stream with a 90ms debounce; triggers A4 when value is ≥ 3 chars
- **A4** – `performSearch()` (System): sets loading state, calls the search service; triggers A5, A6, A7
- **A5** – `searchOneCategory()` (System): builds the Typesense filter and calls A8; returns results to A4
- **A6** – `loading` store (System): holds the boolean loading state; feeds A9
- **A7** – `results` store (System): holds the array of search results; feeds A10
- **A8** – `rawSearch()` (System): queries Typesense; returns `{found, hits}` to A5
- **A9** – loading spinner (UI): renders while A6 is true
- **A10** – results list (UI): renders each hit from A7
- **A11** – result row (UI): click navigates to C2

### 3. Sequence Diagram

A Mermaid sequence diagram with one lifeline per Context. Arrows between lifelines are labelled with Action IDs. Solid arrows show Control Flow →. Dashed arrows show Data Output ←.

```mermaid
sequenceDiagram
    participant C1 as C1: Search Page
    participant C3 as C3: Backend

    C1->>C1: A1 search input (type)
    C1->>C1: A2 activeQuery.next()
    C1->>C1: A3 subscription fires
    C1->>C3: A4 performSearch() → A5 searchOneCategory()
    C3-->>C1: A8 rawSearch() results
    C1->>C1: A6 loading store update
    C1->>C1: A7 results store update
    C1-->>C1: A9 loading spinner renders
    C1-->>C1: A10 results list renders
```

**Line conventions:**

| Arrow | Mermaid Syntax | Meaning |
|---|---|---|
| Solid `->>`  | `A ->> B: label` | Control Flow → (triggers, calls, writes) |
| Dashed `-->>`| `A -->> B: label` | Data Output ← (return values, store reads) |

---

## Step-by-Step Procedures

### Mapping an Existing System

**Step 1: Identify Contexts.**
Walk through the user journey and list every distinct Context — every screen, modal, or system boundary the user crosses.

**Step 2: Trace through the code.**
Starting from the entry point (a route, an API endpoint), follow the code to find every component touched by that flow.

**Step 3: List Actions once in the Actions Reference.**
For each component, identify every button, input, display, function, subscription, and data store involved. Give each one an A-ID and write a short definition. Use real names — if you write "DATABASE," stop and find the actual method (`userRepo.save()`).

**Step 4: Note Control Flow → and Data Output ← in each definition.**
In the definition for each Action, describe what it triggers (Control Flow →) and where its output goes (Data Output ←). This is prose in the Actions Reference, not columns in a table.

**Step 5: Draw a sequence diagram to show Flows.**
Place each Context as a lifeline. Draw solid arrows for Control Flow → and dashed arrows for Data Output ←, labelled with Action IDs. Trace the full journey from the first user interaction to the final visible result.

**Step 6: Check against the code.**
Read the code again. Confirm every Action exists and the sequence diagram matches reality.

---

### Designing Something New

**Step 1: Identify Contexts.**
For each part in your design, decide which Context it lives in — an existing one being modified, or a new one being created.

**Step 2: List Actions once in the Actions Reference.**
For each part, identify the UI Actions the user will see and the System Actions that implement it. Give each an A-ID and write a short definition.

**Step 3: Make sure every UI Action has a System Action supporting it.**
For each UI Action that displays data, check: which System Action provides that data? If none exists, add it.

**Step 4: Draw a sequence diagram to show Flows.**
Trace the intended behaviour from start to finish. Use solid arrows for Control Flow → and dashed arrows for Data Output ←.

**Step 5: Connect to the existing system (if needed).**
Add the existing Actions the new ones must connect to in the Actions Reference. Show those connections in the sequence diagram.

**Step 6: Check for completeness.**
- Every UI Action that shows data should have a System Action feeding it
- Every System Action should appear in at least one arrow in the sequence diagram
- Functions should have outgoing Control Flow → arrows
- Queries should have incoming Data Output ← arrows
- Data stores should have at least one read arrow pointing out of them

**Step 7: Treat everything the user sees as a UI Action.**
Emails, notifications, and any other visible output are UI Actions and need a System Action flowing to them.

---

## Key Rules

### Always check the Actions Reference — don't rely on memory

When tracing a flow backwards, scan the definitions for all Actions that mention your target in their Control Flow →. Don't follow what you think you remember.

### Every name must be real (when mapping existing code)

Never invent abstractions. Every Action name must point to something real in the codebase.

### Not everything is an Action

An Action is something you can act on that has meaningful identity in the system. Some things look like Actions but are actually just implementation details:

| Type | Example | Why It's Not an Action |
|---|---|---|
| Visual containers | `modal-frame wrapper` | You can't act on a wrapper — it's just a Context boundary |
| Internal transforms | `letterDataTransform()` | An implementation detail of the caller |
| Navigation mechanisms | `modalService.open()` | Just the "how" of getting to a Context — draw the arrow directly to the Context |

When reviewing your Actions Reference, ask for each System Action: "Is this actually something I can act on, or is it just describing *how* something happens?" If it's just the "how," remove it and draw the sequence arrow directly to the destination.

```
❌ A8 → A22 → C3     (A22 is modalService.open — just a mechanism)
✅ A8 → C3           (action navigates to context)

❌ A6 → A20 → S2     (A20 is a data transform — internal to A6)
✅ A6 → S2           (callback writes to store)
```

### Two flows to trace: Navigation and Data

| Flow | What It Tracks | Arrow Type |
|---|---|---|
| **Control Flow →** | How the user moves between Contexts; what triggers what | Solid `->>` |
| **Data Output ←** | How results and state flow back to what the user sees | Dashed `-->>` |

When reviewing a sequence diagram, trace both: can you follow the user's journey from Context to Context? And for every UI Action that shows data, can you trace where that data comes from?

### Every UI Action that shows data needs a source

```
❌ A10: results list — no incoming Data Output arrow
✅ A7 (results store) -->> A10 (store feeds the display)
✅ A5 -->> A10 (query result feeds the display)
```

If a display has no data source, either the source is missing or the display isn't real.

### Every System Action must appear in the sequence diagram

- Functions → should have at least one outgoing Control Flow → arrow
- Queries → should have at least one incoming Data Output ← arrow
- Data stores → should have at least one read arrow pointing out

### Side effects need their own Action entry

If a System Action has side effects outside the system boundary (browser URL, localStorage, external API, analytics), add a separate Action entry for that external state and draw an arrow to it:

```
❌ A41: updateUrl() — no outgoing arrow
✅ A41: updateUrl() → A42 (Browser URL store)
```

Common external state to model as Actions:
- Browser URL (query params, hash fragments)
- `localStorage` / `sessionStorage`
- Clipboard
- Browser History

### Keep Control Flow and Data Output distinct

Solid arrows show what triggers what. Dashed arrows show where output goes. Don't mix them up in the sequence diagram.

### Show navigation inline

Draw navigation arrows directly from the Action that causes navigation to the destination Context lifeline. Don't route everything through a central Router entry.

### Put data stores in the Context where their data is consumed

A data store belongs in the Context where its data is *used* to make something happen — not where it's written. Trace who reads it; that determines which Context it belongs to.

### The backend is a Context too

The database and API resolvers aren't floating infrastructure — they're a Context with their own Actions. List them in the Actions Reference and give them a lifeline in the sequence diagram.

---

## Full Reference

### Element Types

| Element | ID Pattern | What It Is | What Qualifies |
|---|---|---|---|
| **Context** | C1, C2, C3… | A bounded interaction context | Passes the blocking test |
| **Sub-context** | C2.1, C2.2… | A defined section within a Context | Groups related Actions |
| **Context Reference** | _ContextName | A UI Action pointing to a detached Context | Complex nested Context defined separately |
| **Action (UI)** | A1, A2, A3… | Something the user sees or interacts with | Buttons, inputs, displays, scroll regions |
| **Action (System)** | A1, A2, A3… | Something in code you can act on | Functions, subscriptions, handlers, data stores |
| **Chunk** | — | A collapsed subsystem | One Flow in, one Flow out, many internals |
| **Placeholder** | — | Out-of-scope content marker | Shows context without detailing |

Note: UI and System Actions share the same A-ID sequence — there is only one Actions Reference.

### Verification Checklist

| Check | Question | If the Answer Is No… |
|---|---|---|
| Every UI Action that shows data | Does it have an incoming Data Output ← arrow in the diagram? | Add the source Action |
| Every System Action | Does it appear in at least one arrow in the sequence diagram? | Investigate — may be dead code or missing flow |
| Every data store Action | Does something read from it? | Investigate — may be unused |
| Navigation mechanisms | Is this Action just the "how" of getting somewhere? | Remove it; draw the arrow directly to the Context |
| System Actions with side effects | Does it affect external state (URL, storage, clipboard)? | Add an Action entry for the external state |

---

## Chunking (Collapsing Complex Subsystems)

Sometimes a section of the breadboard is very complex internally but has a simple interface: one thing goes in, one thing comes out. You can **chunk** it — represent it as a single labelled box in the main sequence diagram and show the internals in a separate diagram.

Use chunking when a section has:
- One entry point (one incoming arrow)
- One exit point (one outgoing arrow)
- A lot of internal complexity between them

In the main diagram, replace the subsystem with a single participant labelled `CHUNK: name`. In a separate diagram, expand the chunk's internals with its own lifelines and arrows.

---

## Slicing a Breadboard

Once you have a complete breadboard, **slice** it — group the Actions into vertical implementation slices that can be built and demoed one at a time.

**Input:** The Actions Reference + the design requirements

**Output:** The same Actions Reference, with each Action assigned to a slice (V1–V9, max 9 slices)

### What Is a Vertical Slice?

A vertical slice is a group of Actions that together do something you can actually demo. It cuts through all layers (UI, logic, data) to deliver a working increment.

The opposite is a horizontal slice — working on one layer only (e.g. "set up all the data models") with nothing clickable to show for it. Horizontal slices are not allowed.

**A slice must have:**
- An entry point (a UI interaction or trigger)
- A visible output (something renders, an effect occurs)
- Meaningful progress toward the goal

### Slice Size

- **Too small:** Only 1–2 UI Actions, nothing meaningful to demo → merge with a related slice
- **Too big:** 15+ Actions or multiple unrelated journeys → split it
- **Right size:** A coherent journey with a clear "watch me do this" demo

Aim for 9 slices or fewer.

### How to Slice

**Step 1: Find the smallest demo-able increment.**
Ask: "What's the least I can build that shows the core thing working?" Usually this is the core data fetch + basic rendering. This becomes V1.

**Step 2: Add capabilities as additional slices.**
Each slice should demonstrate one mechanism working — search, pagination, URL state, etc.

**Step 3: Assign Actions to slices.**
Go through every Action in the Actions Reference and mark which slice it first enters the Flow in.

**Step 4: Write a demo statement for each slice.**
Each slice needs a concrete, one-sentence demo: "Type 'dharma', results filter live."

**Step 5: Draw a per-slice sequence diagram.**
Show the complete sequence diagram in every slice diagram, but style Actions by scope:

| Category | Style | Meaning |
|---|---|---|
| This slice | Highlighted (e.g. green note) | Being built now |
| Already built | Normal | Done in a previous slice |
| Future | Greyed out | Not yet built |

### Slice Summary Format

| # | Slice | Mechanism | Actions in Flow | Demo |
|---|---|---|---|---|
| V1 | Widget with real data | F1, F4, F6 | A3–A11 | "Widget shows letters from API" |
| V2 | Search works | F3 | A1, A2, A3 | "Type to filter results" |
| V3 | Infinite scroll | F5 | A12, A13, A14 | "Scroll down, more load" |
| V4 | URL state | F2 | A15, A16, A17 | "Refresh preserves search" |

---

## Examples

### Example A: Mapping an Existing System

**Goal:** Understand how `admin_organisation_countries` gets modified and read across three entry points: a manual edit in SSO Admin, a checkbox toggle, and a scheduled batch job.

#### Context List

| # | Context | Description |
|---|---|---|
| C1 | SSO Admin — User Change Page | Where an admin edits a user's role and country assignments |
| C2 | sso-dwbn-theme | Background processing layer that reacts to user changes |
| C3 | DWConnect — Center Page | Where country admin assignments are displayed |
| C4 | Batch Trigger | Scheduled `manage.py dwbn_cleanup` job |

#### Actions Reference

- **A1** – `role_profiles` checkboxes (UI): renders the user's current role profiles
- **A2** – "Country Admin" checkbox (UI): click toggles the Country Admin role; leads to A7
- **A3** – `admin_countries` filter (UI): renders the country assignment widget (superuser only, shown by A9)
- **A4** – Available countries list (UI): renders countries the user could be assigned
- **A5** – Selected countries list (UI): renders countries already assigned; fed by S2
- **A6** – Add → / Remove ← (UI): click modifies the selection in A5; leads to A7
- **A7** – Save button (UI): click triggers A10
- **A8** – `get_administrable_user_countries()` (System): returns the list of assignable countries; feeds A4
- **A9** – `get_fieldsets()` (System): conditionally shows A3 for superusers
- **A10** – `save_form()` (System): triggers A11 and A12
- **A11** – Form M2M save (System): Django's built-in M2M handler; writes to S2
- **A12** – `_update_user_m2m()` (System): updates S1; fires signal A13
- **A13** – `user_m2m_field_updated` signal (System): received by A14
- **A14** – `dwbn_user_m2m_field_updated()` (System): receives the signal; triggers A15
- **A15** – `dwbn_user_m2m_field_updated_task()` (System): async task; triggers A16
- **A16** – Country Admin added AND zero admin countries? (System): conditional; if yes, triggers A18
- **A17** – `manage.py dwbn_cleanup` (System): batch job; triggers A19
- **A18** – Get home center's country (System): triggers A20
- **A19** – `admin_changes()` (System): loops over Country Admins; for each missing home center country, triggers A18
- **A20** – `admin_organisation_countries.add()` (System): writes to S2
- **A21** – `findCenterAdmins()` (System): reads S2; feeds A22
- **A22** – "Country admins" section (UI): renders the list of country admins in DWConnect
- **S1** – `role_profiles` (Data Store): M2M — which role profiles a user has
- **S2** – `admin_organisation_countries` (Data Store): M2M — which countries a user administers
- **S3** – `organisations` (Data Store): user's home centre(s)

#### Sequence Diagram

```mermaid
sequenceDiagram
    participant C1 as C1: SSO Admin
    participant C2 as C2: sso-dwbn-theme
    participant C3 as C3: DWConnect
    participant C4 as C4: Batch Trigger

    Note over C1: Manual edit path
    C1->>C1: A2 checkbox click / A6 Add/Remove
    C1->>C1: A7 Save button
    C1->>C1: A10 save_form()
    C1->>C1: A11 Form M2M save → S2
    C1->>C1: A12 _update_user_m2m() → S1
    C1->>C2: A13 user_m2m_field_updated signal

    C2->>C2: A14 receive signal
    C2->>C2: A15 async task
    C2->>C2: A16 conditional check
    C2->>C2: A18 get home center country (S3)
    C2->>C2: A20 admin_organisation_countries.add() → S2

    Note over C4: Batch path
    C4->>C2: A17 manage.py dwbn_cleanup
    C2->>C2: A19 admin_changes() loop
    C2->>C2: A18 get home center country (S3)
    C2->>C2: A20 admin_organisation_countries.add() → S2

    C2-->>C3: S2 updated
    C3->>C3: A21 findCenterAdmins() reads S2
    C3-->>C3: A22 "Country admins" section renders
```

---

### Example B: Designing from Shaped Parts

#### Part 1: What Comes In from the Design Phase

**Requirements**

| ID | Requirement |
|---|---|
| R0 | Make content searchable from the index page |
| R2 | Navigate back to pagination state when returning from detail |
| R3 | Navigate back to search state when returning from detail |
| R4 | Search/pagination state survives page refresh |
| R5 | Browser back button restores previous search/pagination state |
| R9 | Search should debounce input (not fire on every keystroke) |
| R10 | Search should require minimum 3 characters |
| R11 | Loading and empty states should provide user feedback |

**Existing Patterns to Reuse**

| Part | Mechanism |
|---|---|
| S-CUR1 | URL state & initialisation |
| S-CUR2 | Search input (debounce, min 3 chars) |
| S-CUR3 | Data fetching |
| S-CUR4 | Pagination (scroll-to-bottom, append pages) |
| S-CUR5 | Rendering (loading, empty, results list) |

**New Parts**

| Part | Mechanism | Adapts |
|---|---|---|
| F1 | Create widget (component, definition, register) | — |
| F2 | URL state & initialisation (read `?q=`, restore on load) | S-CUR1 |
| F3 | Search input (debounce, min 3 chars, triggers search) | S-CUR2 |
| F4 | Data fetching (`rawSearch()` with filter) | S-CUR3 |
| F5 | Pagination (scroll-to-bottom, append pages, re-arm) | S-CUR4 |
| F6 | Rendering (loading, empty, results list, rows) | S-CUR5 |

#### Part 2: The Breadboard

##### Context List

| # | Context | Description |
|---|---|---|
| C1 | Letters Index Page | The page containing the letter-browser widget |
| C2 | Letter Detail Page | Individual letter view |
| C3 | Full Search Page | Full-page search results |
| C4 | Browser | URL state and back button |
| C5 | Services | typesense.service and intercom.service |

##### Actions Reference

- **A1** – search input (UI): user types a query; triggers A2
- **A2** – `activeQuery.next()` (System): pushes query into the observable stream; triggers A3; feeds A14 (compact link)
- **A3** – `activeQuery` subscription (System): observes stream with 90ms debounce, min 3 chars; triggers A4
- **A4** – `performSearch()` (System): sets loading state, calls search service; triggers A5, A8, A9, A10
- **A5** – `rawSearch()` (System): queries Typesense with filter from A17; returns `{found, hits}` to A4 and A12
- **A6** – `parentId` config (System): filter value fed into A5
- **A7** – `compact` config (System): controls pagination subscription (A15), truncation logic (A16), and search filter (A5)
- **A8** – `loading` store (System): written by A4; feeds A10
- **A9** – `detailResult` store (System): written by A4; feeds A10 and A16
- **A10** – `detectChanges()` (System): triggers re-render; triggers A11, A12, A13, A14 display
- **A11** – loading spinner (UI): renders while A8 is true
- **A12** – no results message (UI): renders when A9 is empty
- **A13** – result count (UI): renders count from A9
- **A14** – results list (UI): renders rows A15a–A15d from A9
- **A15a** – row click (UI): click navigates to C2
- **A15b** – date (UI): renders letter date
- **A15c** – subject (UI): renders letter subject
- **A15d** – teaser (UI): renders letter teaser
- **A16** – scroll (UI): scroll to bottom triggers A18
- **A17** – back button (UI): click reads A19
- **A18** – intercom scroll subject (System): observes scroll; triggers A12 (appendNextPage)
- **A19** – URL `?q=` (System): reads query param; triggers A20
- **A20** – `initializeState()` (System): restores query and triggers A2 and A4 on load
- **A21** – `appendNextPage()` (System): increments page, calls A5, updates A9, triggers A10, calls A22 and A23
- **A22** – `sendMessage()` (System): re-arms A18 for next scroll
- **A23** – `navigate()` (System): updates A19 (browser URL)
- **A24** – if `!compact` subscribe (System): conditionally arms A18 based on A7
- **A25** – if truncated show link (System): conditionally shows A26 based on A9 and A7
- **A26** – "See all X results" link (UI): click navigates to C3 with `?q=` from A2; shown when A25 fires; fed by A18 (fullPageRoute config)

##### Sequence Diagram

```mermaid
sequenceDiagram
    participant C1 as C1: Letters Index
    participant C4 as C4: Browser
    participant C5 as C5: Services
    participant C2 as C2: Letter Detail
    participant C3 as C3: Full Search Page

    Note over C4,C1: Page load — URL state restore (F2)
    C4->>C1: A19 URL ?q= read
    C1->>C1: A20 initializeState()
    C1->>C1: A2 activeQuery.next()
    C1->>C1: A3 subscription fires

    Note over C1,C5: Search (F3, F4)
    C1->>C5: A4 performSearch() → A5 rawSearch()
    C5-->>C1: results returned
    C1->>C1: A8 loading store update
    C1->>C1: A9 detailResult store update
    C1->>C1: A10 detectChanges()
    C1-->>C1: A11 spinner / A12 no results / A13 count / A14 list render

    Note over C1: Row interaction
    C1->>C2: A15a row click → navigate

    Note over C1,C5: Pagination (F5)
    C1->>C1: A16 scroll to bottom
    C1->>C5: A18 scroll subject → A21 appendNextPage()
    C5->>C5: A5 rawSearch() next page
    C5-->>C1: new hits returned
    C1->>C1: A9 store updated (hits appended)
    C1->>C1: A10 detectChanges()
    C1->>C4: A23 navigate() updates URL
    C1->>C5: A22 sendMessage() re-arms scroll

    Note over C1: Compact mode (V5)
    C1->>C1: A25 truncation check
    C1-->>C1: A26 "See all" link renders
    C1->>C3: A26 click → navigate with ?q=
```

##### Slicing

| # | Slice | Mechanism | Actions in Flow | Demo |
|---|---|---|---|---|
| V1 | Widget with real data | F1, F4, F6 | A4–A15d | "Widget shows real letters from API" |
| V2 | Search works | F3 | A1, A2, A3 | "Type 'dharma', results filter live" |
| V3 | Infinite scroll | F5 | A16, A18, A21, A22 | "Scroll down, more results load" |
| V4 | URL state | F2 | A17, A19, A20, A23 | "Refresh preserves the search query" |
| V5 | Compact mode | — | A24, A25, A26 | "Shows 'See all X results' link" |
