# Breadboard Analysis

Find problems in a breadboard and fix them. Works on breadboards built with the breadboarding skill.

---

## Finding Problems

### Entry Point: Trace User Stories Through the Flow

Take a user story from the requirements. Trace it through the See-Do tables and behavior flows. Ask: does the path make sense? Does it produce the expected result?

Example: "User types a search query → results filter in real time → clicks a result → comes back and search is preserved."

Trace: Search input (Do) → `search` flow (steps 1-6) → results appear in See → Click row (Do) → **Letter Detail** → Back (Do) → `restore state` flow → results restored in See.

At each step, ask: does this logically lead to the next? Is anything missing?

### What Problems Look Like

| Problem | What you notice |
|---------|----------------|
| **Missing path** | A user story requires something to happen, but no Do action or behavior flow produces it |
| **Disconnected See items** | A See item shows data, but nothing in the behavior flows produces that data |
| **Dead-end Do actions** | A Do action doesn't point to a screen or behavior flow |
| **Naming resistance** | You can't name a behavior step with one plain verb (see Naming Test below) |
| **Stale elements** | The breadboard shows something that no longer exists in the code |
| **Wrong causality** | The behavior flow says A calls B, but the code shows C calls B |
| **Missing logic** | The code has paths or functions not represented in the behavior flows |

The first three are visible from the breadboard alone. The last four require comparing to the code.

---

## Fixing Problems

### The Naming Test

The main tool for finding and fixing boundary problems in behavior flows.

For each behavior step:

1. **Who calls this?** Who triggers this step?
2. **What does this step do on its own?** Not the downstream chain — just this step's direct effect.
3. **Name it with one verb.** Describe the effect with a single, plain English verb.

| Signal | Meaning |
|--------|---------|
| One verb covers it | The step boundary is right |
| Need "or" to connect two verbs | Probably two steps bundled together |
| Name doesn't feel natural | The boundary is wrong |
| Name matches a downstream effect, not this step | You're naming the chain, not the step |

#### Step-Level vs Chain-Level

Name what THIS step does, not the whole cascade.

**Chain-level** (wrong): A step that calls validate, find, extract, and insert is named "add locale" — but it doesn't add anything itself. Adding is what the whole chain does.

**Step-level** (right): This step's own effect is dispatching work to sub-steps → "handle place locale."

How to check:

1. List everything the step calls downstream
2. Remove all of that — what's left?
3. Name what's left

If what's left is just sequencing and branching, it's a handler. Name it as such.

#### Name From the Caller's Perspective

Names should reflect what the caller achieves by using this step:

| Perspective | Question | Example |
|-------------|----------|---------|
| **Caller** | "What do I get by calling this?" | "handle place_locale request" |
| **Step** | "What does this function do itself?" | "dispatch to validate, resolve, insert" |
| **Effect** | "What changes after this runs?" | "locale is extracted from its position" |

#### External Tools vs Internal Handlers

A tool exposed to an external caller (like an LLM) should be named for the effect the caller wants: `place_locale` — the caller wants to place a locale.

The internal handler should be named for its own role: `handle_place_locale` — it handles the dispatch, delegating work to sub-steps.

#### Naming Resistance as a Signal

A function `resolve_locale` either pops an existing locale from a list OR creates a new one:

- "Take" fits the pop path but "take into existence" isn't natural English
- "Create" fits the new path but not the pop
- Need "or" → split into two steps: `extract_locale` (pop) and `create_locale` (new)

The inability to find one natural verb was the signal that two distinct operations were forced into one step.

### Splitting Steps

When the naming test reveals a bundled step:

1. **Split in the code first.** Extract distinct operations into separate functions. Even one-liners count if they represent a distinct effect.
2. **Then update the behavior flow.** Add the new steps.
3. **Then update any diagrams.**

Never split only in a diagram (adding unnamed sub-nodes). If it's not a named function in the code and a step in the behavior flow, it's not real.

### Fixing Connections

When the behavior flow doesn't match the code:

1. Read the code to understand the actual path.
2. Update the behavior flow to match.
3. Re-trace the user story to confirm it now makes sense.

---

## Verification

After any changes:

1. **Re-trace user stories.** Does each story produce its expected result through the See-Do tables and behavior flows?
2. **Describe the flow in plain language.** Check every claim against the breadboard. If your description says "step 3 calls the API" but the behavior flow doesn't show that, something was missed.
3. **Check consistency:**
   - Every Do action points somewhere (screen or behavior flow)
   - Every See item that shows data has a traceable source
   - Every behavior step connects to something (calls, writes, or produces)
   - Every step in a behavior flow corresponds to something real in the code
