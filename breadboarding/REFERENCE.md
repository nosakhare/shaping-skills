# Breadboarding Reference

## Element Types

| Element | ID Pattern | What It Is | What Qualifies |
|---|---|---|---|
| **Context** | C1, C2, C3… | A bounded interaction context | Passes the blocking test |
| **Sub-context** | C2.1, C2.2… | A defined section within a Context | Groups related Actions |
| **Context Reference** | _ContextName | A UI Action pointing to a detached Context | Complex nested Context defined separately |
| **Action (UI)** | A1, A2, A3… | Something the user sees or interacts with | Buttons, inputs, displays, scroll regions |
| **Action (System)** | A1, A2, A3… | Something in code you can act on | Functions, subscriptions, handlers, data stores |
| **Chunk** | — | A collapsed subsystem | One flow in, one flow out, many internals |
| **Placeholder** | — | Out-of-scope content marker | Shows context without detailing |

Note: UI and System Actions share the same A-ID sequence — there is only one Actions Reference.

## Verification Checklist

| Check | Question | If the Answer Is No… |
|---|---|---|
| Every UI Action that shows data | Does it have an incoming Data Output ← arrow in the diagram? | Add the source Action |
| Every System Action | Does it appear in at least one arrow in the sequence diagram? | Investigate — may be dead code or missing flow |
| Every data store Action | Does something read from it? | Investigate — may be unused |
| Navigation mechanisms | Is this Action just the "how" of getting somewhere? | Remove it; draw the arrow directly to the Context |
| System Actions with side effects | Does it affect external state (URL, storage, clipboard)? | Add an Action entry for the external state |
