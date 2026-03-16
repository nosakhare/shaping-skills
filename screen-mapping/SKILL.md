---
name: screen-mapping
description: Maps a user journey screen by screen. Defines exactly what users see and what they can do before code or system architecture is planned. Use as the bridge between shaped pitches and technical breadboarding.
---

# Screen Mapping (See-Do)

Screen Mapping translates a shaped pitch (or an existing workflow) into concrete user interface requirements. It maps out exactly what the user sees on each screen and what actions they can take, without getting bogged down in database schemas, API endpoints, or precise system architecture.

The output is a set of **See-Do tables**, one per screen in the user journey.

---

## Where It Fits in the Process

1. **Shaping:** Defines the problem and abstract solution (fat-marker sketches).
2. **Screen Mapping (This Skill):** Details the exact user interface, navigation paths, and high-level behavioral logic (See-Do tables).
3. **Breadboarding:** Turns the Screen Map into rigorous system architecture (Action IDs, Contexts, Sequence Diagrams, Data flows).

---

## What It Produces

### Two Layers

**Layer 1: See-Do tables (always)**

For every screen in the user's journey, you create a two-column table:
- **See** — Everything the user can see: labels, data, status indicators, content.
- **Do** — Everything the user can act on: buttons, inputs, links, gestures.

Every "Do" item must point to where it goes: either another screen, or a named behavior flow.

**Layer 2: Behavior flows (when needed)**

When a "Do" action involves complex logic (e.g., submitting a form, calculating a result, fetching data), describe the high-level steps in a plain-text behavior flow. This explains the *UX logic*, not the pure technical implementation.

---

## Core Concepts

### Screens

A screen is wherever the user currently is — what they can see and do right now. While on a screen, they have a specific set of things available. 

**The Blocking Test:** Can you interact with what's behind the new element?
| Answer | What it means |
|--------|---------------|
| **No** | You're on a different screen (e.g., a modal, a confirmation dialog, a full-page edit mode). |
| **Yes** | You are on the same screen (e.g., a dropdown menu, a tooltip, an expanding section). |

Give each screen a clear, descriptive name. That name becomes its heading in your See-Do document.

### The See-Do Format

Each screen gets a table with two columns.

**See**
What the user can see. Static content: labels, data, images, status indicators, headings, lists.
*Rule of Thumb: If a See item displays dynamic data (not just a static label), note what that data is so engineers know it must be fetched.*

**Do**
What the user can act on. Interactive elements: buttons, links, inputs, toggles, gestures. 
Each Do item *must* explicitly state where the action leads:

| Where the action goes | How to write it |
|----------------------|-----------------|
| Another screen | Tap "Send" → **PIN Entry Screen** |
| A behavior flow | Type query → `search letters` |
| Same screen (local change) | Toggle filter → updates list |

### Behavior Flows (High-Level Logic)

A behavior flow explains the steps behind a complex "Do" action. When someone types in a search box and results appear, what happens between the typing and the results appearing?

**Example: `search letters`**
1. Wait for the user to stop typing (debounce), requiring at least 3 characters.
2. Send the query to the search backend.
3. Show a loading state while waiting.
4. When results return, display them in the results list.
5. If no results, show the empty state message.

*Note: In Screen Mapping, behavior flows are written in plain English for product and design alignment. In the next phase (Breadboarding), engineers will translate these into exact API calls, data stores, and sequence diagrams.*

---

## Example Output

### Workflow: Transferring Money

**Transfer Screen**

| See | Do |
|-----|-----|
| Available balance | Enter amount → **Amount Confirmation** |
| Recipient name and account number | Change recipient → **Recipient Search** |
| Bank name | Add note (optional) |
| Transfer fee (if any) | Tap "Send" → **PIN Entry** |
| Recent recipients list | Select recent recipient → fills in details |

**PIN Entry**

| See | Do |
|-----|-----|
| Transfer summary (amount, recipient) | Enter PIN digits |
| "Enter your PIN" prompt | Tap "Confirm" → `process transfer` |
| PIN input (masked) | Tap "Cancel" → **Transfer Screen** |

**Success Screen**

| See | Do |
|-----|-----|
| Success icon | Share receipt → **Share Sheet** |
| Amount sent | Tap "Done" → **Account Dashboard** |
| Recipient details | Make another transfer → **Transfer Screen** |
| Transaction reference number | |

**Behavior Flow: `process transfer`**
1. Send the entered PIN, amount, and recipient details for validation.
2. If the PIN is incorrect or balance is insufficient, return an error and stay on **PIN Entry**.
3. If successful, execute the transfer, generate a transaction reference number, and navigate to the **Success Screen**.

---

## Verification Checklist

Before handing the Screen Map off for technical Breadboarding, check the following:

- **No Dead Ends:** Does every "Do" action explicitly state where it goes (another screen or a behavior flow)?
- **Data Accountability:** For every "See" item that isn't static text (e.g., "Available balance", "Recent recipients"), is it clear that data needs to be populated? 
- **Screen Boundaries:** Did you properly use the Blocking Test? (e.g., A blocking modal is its own screen, not just a "See" item on the parent screen).
- **Stakeholder Alignment:** Can a product manager or designer read these tables and confidently say, "Yes, this is exactly how the feature should behave"?
