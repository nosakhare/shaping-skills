# Breadboarding Slicing

## Chunking

Sometimes a section of the breadboard is very complex internally but has a simple interface: one thing goes in, one thing comes out. You can **chunk** it — represent it as a single labelled box in the main sequence diagram and show the internals in a separate diagram.

Use chunking when a section has:

- One entry point (one incoming arrow)
- One exit point (one outgoing arrow)
- A lot of internal complexity between them

In the main diagram, replace the subsystem with a single participant labelled `CHUNK: name`. In a separate diagram, expand the chunk's internals with its own lifelines and arrows.

## Slicing a Breadboard

Once you have a complete breadboard, slice it — group the Actions into vertical implementation slices that can be built and demoed one at a time.

**Input:** The Actions Reference plus the design requirements

**Output:** The same Actions Reference, with each Action assigned to a slice (`V1`-`V9`, max 9 slices)

### What Is a Vertical Slice?

A vertical slice is a group of Actions that together do something you can actually demo. It cuts through all layers (UI, logic, data) to deliver a working increment.

The opposite is a horizontal slice — working on one layer only, such as "set up all the data models," with nothing clickable to show for it. Horizontal slices are not allowed.

A slice must have:

- An entry point (a UI interaction or trigger)
- A visible output (something renders or an effect occurs)
- Meaningful progress toward the goal

### Slice Size

- **Too small:** Only 1-2 UI Actions, nothing meaningful to demo; merge with a related slice
- **Too big:** 15+ Actions or multiple unrelated journeys; split it
- **Right size:** A coherent journey with a clear "watch me do this" demo

Aim for 9 slices or fewer.

### How to Slice

1. **Find the smallest demo-able increment.** Ask: "What's the least I can build that shows the core thing working?" Usually this is the core data fetch plus basic rendering. This becomes `V1`.
2. **Add capabilities as additional slices.** Each slice should demonstrate one mechanism working, such as search, pagination, or URL state.
3. **Assign Actions to slices.** Go through every Action in the Actions Reference and mark which slice it first enters the flow in.
4. **Write a demo statement for each slice.** Each slice needs a concrete, one-sentence demo such as "Type 'dharma', results filter live."
5. **Draw a per-slice sequence diagram.** Show the complete sequence diagram in every slice diagram, but style Actions by scope:

| Category | Style | Meaning |
|---|---|---|
| This slice | Highlighted (e.g. green note) | Being built now |
| Already built | Normal | Done in a previous slice |
| Future | Greyed out | Not yet built |

### Slice Summary Format

| # | Slice | Mechanism | Actions in Flow | Demo |
|---|---|---|---|---|
| V1 | Widget with real data | F1, F4, F6 | A3-A11 | "Widget shows letters from API" |
| V2 | Search works | F3 | A1, A2, A3 | "Type to filter results" |
| V3 | Infinite scroll | F5 | A12, A13, A14 | "Scroll down, more load" |
| V4 | URL state | F2 | A15, A16, A17 | "Refresh preserves search" |
