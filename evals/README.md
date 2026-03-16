# Skill Evals

This directory contains lightweight evaluation fixtures for the main skills in this repo.

Each eval is a JSON scenario with:

- `skills`: the skill or skills that should be enabled
- `query`: the task prompt
- `files`: optional local files or artifacts needed for the task
- `expected_behavior`: the rubric to score against

Suggested manual process:

1. Run each eval against Claude Haiku, Claude Sonnet, and Claude Opus.
2. Check whether the skill triggers when expected.
3. Compare the output against `expected_behavior`.
4. Tighten `SKILL.md` or reference files only when the eval reveals a real failure mode.
