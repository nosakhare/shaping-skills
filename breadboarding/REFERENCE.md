# Breadboarding Reference

## Legend

| Prefix | Type | Definition |
|---|---|---|
| P# | Place | A bounded context of interaction — where you are and what you can do |
| U# | UI | What the user sees or interacts with |
| N# | Code | Functions and handlers you can call or observe |
| S# | Store | State that persists and is read and written |

## Element Types

| Element | ID Pattern | What It Is | What Qualifies |
|---|---|---|---|
| **Place** | P1, P2, P3… | A bounded interaction context | Passes the blocking test |
| **Sub-place** | P2.1, P2.2… | A defined section within a Place | Groups related UI within a larger Place |
| **Place Reference** | _PlaceName | A UI element pointing to a detached Place | Complex nested Place defined separately |
| **UI** | U1, U2, U3… | What the user sees or interacts with | Buttons, inputs, displays, scroll regions |
| **Code** | N1, N2, N3… | Functions and handlers you can call or observe | Functions, subscriptions, handlers, framework hooks |
| **Store** | S1, S2, S3… | State that persists and is read and written | Observables, arrays, booleans, external state (URL, localStorage) |
| **Chunk** | — | A collapsed subsystem | One flow in, one flow out, many internals |
| **Placeholder** | — | Out-of-scope content marker | Shows context without detailing |

## Verification Checklist

| Check | Question | If the Answer Is No… |
|---|---|---|
| Every U# that shows data | Does it have an incoming Feeds ← arrow in the sequence diagram? | Add the source N# or S# |
| Every N# | Does it appear in at least one arrow in the sequence diagram? | Investigate — may be dead code or missing flow |
| Every S# | Does something feed from it (a U# reading it)? | Investigate — may be unused |
| Navigation mechanisms | Is this N# just the "how" of getting somewhere? | Remove it; draw the arrow directly to the Place |
| N# with side effects | Does it affect external state (URL, storage, clipboard)? | Add a S# entry for the external state |
| State diagram | Does every user-facing Place appear as a state node? | Add it; check it's reachable from the entry point |
| State diagram terminal nodes | Is every Place with no outgoing transitions intentional? | Add back navigation or note it as a deliberate dead end |
