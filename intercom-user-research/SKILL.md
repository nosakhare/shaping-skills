---
name: intercom-user-research
description: Reviews Intercom conversation transcripts to build an evidence base around a customer problem. Use when the user wants recurring pain points, customer language, segments, and patterns from support conversations without jumping straight to roadmap framing.
---

# Intercom User Research

Review Intercom conversation transcripts to understand what customers are struggling with, how often it appears in the sample, how painful it seems, and what the evidence does and does not support.

This skill is for building a reusable research artifact from support conversations. It is not for deciding roadmap priority or writing a framing document.

## Use This Skill When

- The user wants to understand a customer problem from Intercom conversations
- The team needs direct customer language and representative evidence
- The team wants to cluster recurring issues, workarounds, and confusion patterns
- The team needs to separate product problems from policy, ops, or support-tooling problems

## Do Not Use This Skill For

- Choosing which problem to solve now
- Writing a frame with `Problem` and `Outcome`
- Making business-priority arguments like "why now" or "why this over alternatives"
- Proving causality beyond what the conversations actually show

If the team wants to decide whether this problem should become a project, hand the research output to `framing-doc`.

## Inputs

Before pulling transcripts, get clear on:

1. **Research question** — What customer problem are we investigating?
2. **Customer job** — What is the customer trying to get done?
3. **Target segment** — Which users, account types, journey stages, or geographies matter first?
4. **Date range** — What time window should the pull cover?
5. **Scope filters** — Inbox, team, tags, contact reasons, or other useful metadata
6. **Sample size** — Start focused; 30 to 50 relevant conversations is often enough for a first pass

Keep the question narrow. The goal is to investigate one problem area well, not to summarize the whole queue.

## Search Vocabulary

Before searching, write down the different ways this problem may appear:

- Customer language
- Agent language
- Internal product language
- Old labels or contact reasons
- Common misspellings and shorthand

Run both:

- A **keyword pull** using the words customers actually use
- A **structured pull** using metadata like tags, inboxes, or contact reasons

This helps catch both labeled and unlabeled evidence.

## Evidence Standard

Good evidence is:

- **Attributed** to real conversations or customer segments
- **Quantified** when possible within the reviewed sample
- **Honest about uncertainty**

Good:

- "18 of 50 reviewed conversations mention delayed card delivery after address confirmation"
- "Most conversations about payout visibility in this sample came from new business accounts in their first 30 days"
- "Agents repeatedly explain the same manual workaround"

Weak:

- "Lots of users complained"
- "Customers hate this"
- "This is why deals are lost" when the transcripts only show the issue came up

## Workflow

### 1. Define the pull

Before using Intercom MCP, decide:

- Research question
- Date range
- Segment or inbox
- Keywords
- First-pass sample size

### 2. Find candidate conversations

Use the Intercom MCP to search or list conversations matching the scope.

Useful filters include:

- Keywords from the conversation body
- Contact reason or category
- Date range
- Inbox
- Tag
- Conversation state
- Customer type or segment
- Teammate assignment when relevant

### 3. Pull the full transcript

For each selected conversation, retrieve the full conversation trail, not just a summary.

Capture:

- Conversation ID or URL
- Created date
- Safe customer identifiers when relevant
- Tags, contact reason, and metadata
- Full message trail from first customer message to final resolution or handoff
- Internal notes only when they help explain the customer problem or ops response

### 4. Log the raw evidence

For each reviewed conversation, record:

| Field | What to capture |
|---|---|
| Conversation | ID or URL |
| Date | When it happened |
| Segment | Customer type, product, or journey stage |
| Theme | Main problem shown in the transcript |
| Evidence | Direct quote or short excerpt |
| Interpretation | What this transcript suggests |
| Outcome | Resolved, escalated, abandoned, workaround, still blocked |

### 5. Read for patterns, not just volume

As you review, pay attention to:

- Exact customer wording
- Triggers that started the conversation
- What the customer was trying to do before things went wrong
- Whether the issue is about understanding, trust, speed, access, or failure
- Whether the agent had enough tooling or information to help
- Whether the conversation ended with clarity, escalation, workaround, or frustration

Do not confuse ticket count with severity. A lower-volume issue may still matter if the pain is high or the affected segment is important.

### 6. Cluster findings into themes

After reading the sample, group conversations into recurring themes.

Look for:

- Repeated triggers
- Repeated customer wording
- Repeated agent explanations or workarounds
- Hidden repeats under different labels
- Points where the customer loses trust
- Places where the product, help content, policy, or ops flow is insufficient

Also separate:

- Product problems
- Policy problems
- Operational problems
- Agent tooling gaps

### 7. Write the synthesis

The final synthesis should answer:

- What customers are struggling with
- How often it appears in the reviewed sample
- Which segments are most affected
- What customers expected instead
- What evidence suggests product vs policy vs ops causes
- What remains unclear and needs more validation

## Output

Write a reusable research note, usually in the project's `research/` folder.

Recommended files:

- `research/intercom-evidence.md` — reviewed conversations and evidence log
- `research/intercom-patterns.md` — themes, repeated patterns, and anti-patterns
- `research/sample-transcripts.md` — representative excerpts

A good run should leave behind:

- A clear research question
- A documented pull method
- A list of reviewed conversations
- A set of direct quotes
- A theme breakdown
- A short synthesis with limits and open questions

## Handoff

When the team wants to decide whether this problem should become a project, hand the research output to `framing-doc`.

This skill produces the evidence base. `framing-doc` turns that evidence into a decision-oriented frame.

## Things to Avoid

- Do not use only one keyword
- Do not rely only on tags or contact reasons
- Do not rely only on AI summaries when the raw transcript matters
- Do not overclaim causality from a small sample
- Do not ignore agent friction; it often reveals product or ops gaps
- Do not drift into roadmap prioritization or solution shaping

## Prompt Template

Use this when running the skill:

> Review Intercom conversation transcripts to investigate this customer problem: **[insert problem]**. Define the search vocabulary, pull a focused set of relevant conversations using Intercom MCP, read the full transcripts, extract direct quotes, identify repeated patterns, separate product issues from policy or ops issues, and write the findings into `research/intercom-evidence.md`. Be precise, evidence-led, and explicit about what the conversations do and do not prove.
