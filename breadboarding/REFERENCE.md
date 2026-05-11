# Breadboarding Reference

## Legend

| Type | Definition |
|---|---|
| Place | A bounded context of interaction — where you are and what you can do |
| UI | What the user sees or interacts with |
| Code | Functions and handlers you can call or observe |
| Store | State that persists and is read and written |

## Element Types

| Element | What It Is | What Qualifies |
|---|---|---|
| **Place** | A bounded interaction context | Passes the blocking test |
| **Sub-place** | A defined section within a Place | Groups related UI within a larger Place; named descriptively |
| **Place Reference** | A UI element pointing to a detached Place | Complex nested Place defined separately |
| **UI** | What the user sees or interacts with | Buttons, inputs, displays, scroll regions |
| **Code** | Functions and handlers you can call or observe | Functions, subscriptions, handlers, framework hooks |
| **Store** | State that persists and is read and written | Observables, arrays, booleans, external state (URL, localStorage) |
| **Chunk** | A collapsed subsystem | One flow in, one flow out, many internals |
| **Placeholder** | Out-of-scope content marker | Shows context without detailing |

## Verification Checklist

| Check | Question | If the Answer Is No… |
|---|---|---|
| Every UI element that shows data | Does it have an incoming Feeds ← arrow in the sequence diagram? | Add the source Code element or Store |
| Every Code element | Does it appear in at least one arrow in the sequence diagram? | Investigate — may be dead code or missing flow |
| Every Store | Does something feed from it (a UI element reading it)? | Investigate — may be unused |
| Navigation mechanisms | Is this Code element just the "how" of getting somewhere? | Remove it; draw the arrow directly to the Place |
| Code elements with side effects | Does it affect external state (URL, storage, clipboard)? | Add a Store entry for the external state |
| State diagram | Does every user-facing Place appear as a state node? | Add it; check it's reachable from the entry point |
| State diagram terminal nodes | Is every Place with no outgoing transitions intentional? | Add back navigation or note it as a deliberate dead end |
