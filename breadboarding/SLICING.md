# Breadboarding Slicing

## Chunking

Sometimes a section of the breadboard is very complex internally but has a simple interface: one thing triggers it, one thing comes out. You can **chunk** it — represent it as a single labelled box in the main sequence diagram and show the internals in a separate diagram.

Use chunking when a section has:

- One entry point (one incoming Triggers arrow)
- One exit point (one outgoing Feeds arrow)
- A lot of internal complexity between them

In the main diagram, replace the subsystem with a single participant labelled `CHUNK: name`. In a separate diagram, expand the chunk's internals with its own lifelines and arrows.

## Slicing a Breadboard

Once you have a complete breadboard, slice it — group the UI, Code, and Stores into vertical implementation slices that can be built and demoed one at a time.

**Input:** The UI, Code, and Stores tables plus the design requirements

**Output:** The same tables, with each element assigned to a slice (`V1`-`V9`, max 9 slices)

### What Is a Vertical Slice?

A vertical slice is a group of elements that together do something you can actually demo. It cuts through all layers (UI, logic, data) to deliver a working increment.

The opposite is a horizontal slice — working on one layer only, such as "set up all the data models," with nothing clickable to show for it. Horizontal slices are not allowed.

A slice must have:

- An entry point (a UI interaction or trigger)
- A visible output (something renders or an effect occurs)
- Meaningful progress toward the goal

### Slice Size

- **Too small:** Only 1-2 UI elements, nothing meaningful to demo; merge with a related slice
- **Too big:** 15+ elements or multiple unrelated journeys; split it
- **Right size:** A coherent journey with a clear "watch me do this" demo

Aim for 9 slices or fewer.

### How to Slice

1. **Find the smallest demo-able increment.** Ask: "What's the least I can build that shows the core thing working?" Usually this is the core data fetch plus basic rendering. This becomes `V1`.
2. **Add capabilities as additional slices.** Each slice should demonstrate one mechanism working, such as search, pagination, or URL state.
3. **Assign elements to slices.** Go through every UI element, Code element, and Store and mark which slice it first enters the flow in.
4. **Write a demo statement for each slice.** Each slice needs a concrete, one-sentence demo such as "Type 'dharma', results filter live."
5. **Draw a per-slice sequence diagram.** Show the complete sequence diagram in every slice diagram, but style elements by scope:

| Category | Style | Meaning |
|---|---|---|
| This slice | Highlighted (e.g. green note) | Being built now |
| Already built | Normal | Done in a previous slice |
| Future | Greyed out | Not yet built |

### Slice Summary Format

| Slice | Mechanism | Elements in Flow | Demo |
|---|---|---|---|
| V1 Widget with real data | F1, F4, F6 | performSearch(), rawSearch(), loading, Results list | "Widget shows letters from API" |
| V2 Search works | F3 | Search input, activeQuery.next(), activeQuery subscription | "Type to filter results" |
| V3 Infinite scroll | F5 | Scroll, appendNextPage(), sendMessage() | "Scroll down, more load" |
| V4 URL state | F2 | Back button, URL ?q=, initializeState(), navigate() | "Refresh preserves search" |
