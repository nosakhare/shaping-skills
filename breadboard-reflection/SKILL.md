---
name: breadboard-reflection
description: Reflects on an existing breadboard by syncing it to the implementation, then identifying and fixing design problems. Use when reviewing or repairing a breadboard built from real code.
---

# Breadboard Reflection

Reflect on a breadboard by syncing it to the implementation, then finding and fixing design problems. Works on existing breadboards built with the `/breadboarding` skill.

---

## What a Breadboard Is

A breadboard should be a legible explanation of how the system produces its effects. When you look at it, you should be able to account for every behavior — not just see that an effect happens, but understand *how* it happens through the wiring.

When someone's mental model of how the system behaves doesn't match what the breadboard shows, either their model is wrong or the breadboard is incomplete. A problem is that gap — you expect to see work being done (data transformed, decisions made, state managed) and the breadboard doesn't show it. Each question that probes the gap ("what generates this data?", "where is this defined?", "who calls this?") is testing the mental model against the artifact.

The breadboard is complete when it can explain every behavior without hidden steps.

---

## Core Checklist

Reflection is a two-phase loop: **SEE, then REFLECT.** Always in this order.

### Phase 1: SEE — Sync breadboard to the implementation

The code is ground truth. The breadboard may have drifted or been built from a conceptual shape that doesn't match what was actually implemented. Before looking for design problems, make the breadboard accurate.

1. **Read the implementation code.** Find the relevant source files. Don't rely on the breadboard's current description of what the code does.
2. **Inspect the seams.** Walk the code using the checklist in "Reading Seams from the Implementation" below — module boundaries, module-level definitions, function signatures, full call chains, decorators, state co-access.
3. **Update the breadboard to match.** Add missing Actions, Contexts, and data stores. Fix wrong sequence diagram wiring. Remove stale Actions. The goal is: the breadboard now accurately reflects the code's actual structure — even if that structure has problems.

After Phase 1, the breadboard shows what IS, not what should be.

### Phase 2: REFLECT — Find and fix design problems

Phase 1 is preparation. Phase 2 is the point. Do not skip it.

Now that the breadboard is accurate, inspect it for design problems. The code's names might be wrong. The split of responsibilities might not be ideal. The wiring might reveal unnecessary coupling or missing abstractions. Walk through each of these concrete checks:

1. **Trace user stories through the wiring.** Does the path tell a coherent story? Can you explain every behavior without hidden steps?
2. **Run the naming test on every System Action.** For each System Action: who is the caller, what is the step-level effect, can you name it with one plain verb? Flag any that resist naming. (See "The Naming Test" in Phase 2 details below.)
3. **Check for hidden data stores.** For every module-level constant, config object, or template that an Action reads: is it in the Actions Reference? If code reads it to produce effects, it's a store — even if it's static. Ask: "what does this action need to know in order to do its job?" If the answer references something not in the breadboard, it's missing.
4. **Question the Contexts.** For each Context: does everything in it share a single responsibility? Do state and System Actions within it co-access each other and stay isolated from other Contexts? If a cluster of System Actions, state, and UI Action elements all serve one job (e.g., "turn natural language into structured commands"), they might deserve their own Context — even if the code doesn't separate them yet.
5. **Check the problems table.** Unexplained behavior, incoherent Sequence Diagram wiring, naming resistance, etc.
6. **Fix problems.** Split, merge, rename, or rewire behavior steps. Update the breadboard.
7. **Optionally update the code.** If the breadboard reveals a better design, refactor the implementation to match.

The loop: SEE what the code actually does → REFLECT on whether that design is right → fix the breadboard → optionally fix the code → repeat.

---

## Phase 1: SEE — Read Seams from the Implementation

The code is ground truth. Read it before judging the breadboard.

### What to Look For

**1. Module boundaries are seams.** Each file or module is a boundary the code has already chosen. Check what crosses it — that's a public interface, and the breadboard should show it. If a module exists (e.g., `llm.py` separate from `app.py`), the breadboard shouldn't reach through it to grab internals. The module's public function is the behavior step; what it calls internally is behind the boundary.

**2. Module-level definitions are data stores.** Constants, configurations, and templates at the top of a module — `TOOLS`, `SYSTEM_PROMPT`, `DEFAULTS` — are static state that shapes behavior. If code reads them to produce effects, they belong in the breadboard as stores. They're easy to miss because they don't change at runtime, but they define what the system can do.

**3. Function signatures are contracts.** The types tell you what data flows across boundaries. When a function's return type differs from what its downstream dependency returns (e.g., `send_command` returns `list[dict]` but `ollama.chat` returns a Response object), there's a transformation happening. That transformation is work the breadboard should account for. Name it.

**4. Trace the full call chain, not just endpoints.** Don't jump from the UI event to the external service to the executor. Walk every function in the chain. Each one exists for a reason — orchestration, state management, data transformation, error handling. The ones that do real work (not just forwarding) are behavior steps. Skipping intermediate functions because they look like "glue" hides the explanation.

**5. Decorators and patterns signal architectural roles.** `@work(thread=True)` means background worker. `try/except` wrappers mean error boundary. `async` means concurrency management. These aren't just implementation details — they tell you a function has a specific role in the system's architecture. An event handler that delegates to a `@work` function is two distinct things: a trigger and an orchestrator.

**6. State that co-accesses suggests Contexts.** Which functions and state are used together but don't touch other parts of the system? If `loading`, `TOOLS`, and `SYSTEM_PROMPT` are all accessed by the command input flow and never by the table display, they cluster into a candidate Context. Context boundaries emerge from co-access patterns, not just from UI layout.

### The Underlying Principle

A breadboard designed from a conceptual shape ("user types, LLM processes, app executes") will be a flat pipeline. The code is more specific — it has already decided where the seams are through module splits, function extractions, and static configuration. When a breadboard exists alongside code, read the code's seams and check that the breadboard accounts for them. Not every function needs a node, but every module boundary, every data transformation, and every piece of static configuration that shapes behavior should show up somewhere.

### Updating the Breadboard

After inspecting the code, update the breadboard to match what IS:
- Add missing System Actions for functions the breadboard skipped
- Add missing stores for constants and configuration the breadboard ignored
- Fix sequence diagram wiring to match actual call chains
- Add or restructure Contexts based on state co-access

Do NOT fix design problems yet. The goal of Phase 1 is an accurate picture, even if the design has issues.

---

## Phase 2: REFLECT — Find and Fix Design Problems

Now the breadboard is accurate. The code's names might be wrong, the split of responsibilities might not be ideal, the wiring might reveal unnecessary coupling. This is where you judge the design.

### Trace User Stories Through the Wiring

Take a user story from the requirements. Trace it through the See-Do tables and behavior flows. Ask: does the path make sense? Does it produce the expected result?

Example: "User types a search query → results filter in real time → clicks a result → comes back and search is preserved."

Trace: Search input (A1) → `activeQuery` stream (A2) → `performSearch` (A4) → results store (A7) → results list renders (A10) → Click row (A11) → **Letter Detail Context** (C2) → Back button (A17) → `initializeState` (A20) → results restored (A10).

At each step, ask: does this logically lead to the next? Is anything missing?

### What Problems Look Like

| Problem | What you notice |
|---------|-----------------|
| **Unexplained behavior** | You know the system does something (transforms data, makes decisions) but the breadboard doesn't show how — the explanation is missing |
| **Incoherent wiring** | A node writes to S1 AND triggers the thing that writes to S1 — redundant or contradictory |
| **Missing path** | The user story requires an effect, but no wiring path produces it |
| **Diagram-only Actions** | Actions in the sequence diagram that aren't defined in the Actions Reference |
| **Naming resistance** | You can't name a System Action with one plain verb (see Naming Test below) |
| **Stale elements** | The breadboard shows something that no longer exists in the code — should have been caught in Phase 1 |
| **Wrong causality** | The wiring shows A calls B, but the code shows C calls B — should have been caught in Phase 1 |

The first five are design problems — the code works but the design could be better. The last two are accuracy problems that Phase 1 should have caught; if you find them here, go back to Phase 1.

### Fixing Problems

### The Naming Test

The main tool for finding and fixing boundary problems in behavior flows.

For each System Action:

1. **Who calls this?** Who triggers this action?
2. **What does this action do on its own?** Not the downstream chain — just this action's direct effect.
3. **Name it with one verb.** Describe the effect with a single, plain English verb.

| Signal | Meaning |
|--------|---------|
| One verb covers it | The step boundary is right |
| Need "or" to connect two verbs | Probably two steps bundled together |
| Name doesn't feel natural | The boundary is wrong |
| Name matches a downstream effect, not this step | You're naming the chain, not the step |

### Step-Level vs Chain-Level

Name what THIS step does, not the whole cascade.

**Chain-level** (wrong): A step that calls validate, find, extract, and insert is named "add locale" — but it doesn't add anything itself. Adding is what the whole chain does.

**Step-level** (right): This step's own effect is dispatching work to sub-steps → "handle place locale."

How to check:

1. List everything the step calls downstream
2. Remove all of that — what's left?
3. Name what's left

If what's left is just sequencing and branching, it's a handler. Name it as such.

### Name From the Caller's Perspective

Names should reflect what the caller achieves by using this step:

| Perspective | Question | Example |
|-------------|----------|---------|
| **Caller** | "What do I get by calling this?" | "handle place_locale request" |
| **Step** | "What does this function do itself?" | "dispatch to validate, resolve, insert" |
| **Effect** | "What changes after this runs?" | "locale is extracted from its position" |

### External Tools vs Internal Handlers

A tool exposed to an external caller (like an LLM) should be named for the effect the caller wants: `place_locale` — the caller wants to place a locale.

The internal handler should be named for its own role: `handle_place_locale` — it handles the dispatch, delegating work to sub-steps.

### Naming Resistance as a Signal

A function `resolve_locale` either pops an existing locale from a list OR creates a new one:

- "Take" fits the pop path but "take into existence" isn't natural English
- "Create" fits the new path but not the pop
- Need "or" → split into two steps: `extract_locale` (pop) and `create_locale` (new)

The inability to find one natural verb was the signal that two distinct operations were forced into one step.

### Splitting Steps

When the naming test reveals a bundled step:

1. **Split in the code first.** Extract distinct operations into separate functions. Even one-liners count if they represent a distinct effect.
2. **Then update the Actions Reference.** Add the new System Actions.
3. **Then update any sequence diagrams.**

Never split only in a diagram (adding unnamed actions). If it's not a named function in the code and a System Action in the diagram, it's not real.

### Fixing Connections

When the Sequence Diagram doesn't match the code:

1. Read the code to understand the actual path.
2. Update the Sequence Diagram to match.
3. Re-trace the user story to confirm it now makes sense.

---

## Verification

After any changes:

1. **Re-trace user stories.** Does each story produce its expected result through the Actions Reference and Sequence Diagrams?
2. **Describe the flow in plain language.** Check every claim against the Sequence Diagram. If your description says "step 3 calls the API" but the sequence diagram doesn't show that, something was missed.
3. **Check consistency:**
   - Every UI Action that shows data has an incoming Data Output arrow
   - Every System Action appears in at least one Sequence Diagram arrow
   - Every data store Action has a read arrow pointing out of it
   - Every arrow in the Sequence Diagram corresponds to something real in the code
