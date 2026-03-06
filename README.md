# Shaping Skills

[Claude Code](https://claude.com/claude-code) skills for shaping and breadboarding — the methodology from [Shape Up](https://basecamp.com/shapeup) adapted for working with an LLM.

**Case study:** [Shaping 0-1 with Claude Code](https://x.com/rjs/status/2020184079350563263) walks through the full process of building a project from scratch using these skills. The source for that project is at [rjs/tick](https://github.com/rjs/tick).

## Skills

### Document skills — for collaborative work

These turn transcripts of real conversations into structured shaping documents. They're useful on real production projects where you're working with other people and want to capture what was said in a format you can act on.

**These are extremely GIGO (garbage in, garbage out).** They don't evaluate whether the material makes sense or is reasonable. They format and distill — that's it. When your inputs are good conversations with good thinking, they save a ton of time. When your inputs are bad, you get a nicely formatted bad document.

**`/framing-doc`** — Turn conversation transcripts into a framing document that captures the problem worth solving and why it was chosen over alternatives.

**`/kickoff-doc`** — Turn a shaped project kickoff transcript into a reference document for the builder, capturing what was shaped and agreed.

### Solo skills — more experimental

These are for working with Claude directly on shaping and design. They're more experimental and less battle-tested than the document skills.

**`/shaping`** — Iterate on both the problem (requirements) and solution (options) before committing to implementation. Separates what you need from how you might build it, with coverage checks to see what's solved and what isn't.

**`/breadboarding`** — Map a system screen by screen — what users see, what they can do, and how it works underneath. Uses See-Do tables for the interface, behavior flows for the logic, and an optional PlantUML journey diagram for the full picture across actors. Good for slicing into vertical scopes.

**`/breadboard-reflection`** — Reflect on an existing breadboard by syncing it to the implementation, then finding and fixing design problems. Two-phase: first make the breadboard accurate (code is ground truth), then judge the design.

## Install

```bash
# Clone the repo, then symlink each skill into your Claude Code skills directory
git clone https://github.com/rjs/shaping-skills.git ~/.local/share/shaping-skills
ln -s ~/.local/share/shaping-skills/framing-doc ~/.claude/skills/framing-doc
ln -s ~/.local/share/shaping-skills/kickoff-doc ~/.claude/skills/kickoff-doc
ln -s ~/.local/share/shaping-skills/breadboarding ~/.claude/skills/breadboarding
ln -s ~/.local/share/shaping-skills/breadboard-reflection ~/.claude/skills/breadboard-reflection
ln -s ~/.local/share/shaping-skills/shaping ~/.claude/skills/shaping
```

Each skill must be a direct child of `~/.claude/skills/` so Claude Code can discover it. Symlinks keep them updatable with `git pull`.

## Codex

To use these as Codex skills, symlink them into `~/.codex/skills/`:

```bash
mkdir -p ~/.codex/skills
ln -s "$(pwd)/shaping" ~/.codex/skills/shaping
ln -s "$(pwd)/breadboarding" ~/.codex/skills/breadboarding
```

## Cursor

To use these skills as **global Cursor Agent Skills** (available in all projects), put them under `~/.cursor/skills/`. Copy or symlink from the repo root:

```bash
mkdir -p ~/.cursor/skills

# Option A: copy (Cursor has its own copy; re-copy after upstream changes)
cp -r shaping ~/.cursor/skills/shaping
cp -r breadboarding ~/.cursor/skills/breadboarding
cp breadboarding/skill.md ~/.cursor/skills/breadboarding/SKILL.md   # Cursor expects SKILL.md

# Option B: symlink (single source of truth; git pull updates Cursor)
ln -sf "$(pwd)/shaping" ~/.cursor/skills/shaping
ln -s "$(pwd)/breadboarding" ~/.cursor/skills/breadboarding
cp breadboarding/skill.md ~/.cursor/skills/breadboarding/SKILL.md   # Cursor expects SKILL.md
```

The ripple-check hook below is Claude-specific and is not used by Cursor.

## Hook: Ripple Check

The repo includes a hook that reminds Claude to check for ripple effects when editing shaping documents. When Claude writes or edits a `.md` file with `shaping: true` in its frontmatter, the hook prompts a checklist — update element tables, coverage checks, work streams, etc.

### Setup

1. Symlink the hook script:

```bash
mkdir -p ~/.claude/hooks
ln -s ~/.local/share/shaping-skills/hooks/shaping-ripple.sh ~/.claude/hooks/shaping-ripple.sh
```

2. Add the hook to your `~/.claude/settings.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/hooks/shaping-ripple.sh",
            "timeout": 5
          }
        ]
      }
    ]
  }
}
```

This fires after every `Write` or `Edit` tool call. It only activates for shaping documents (those with `shaping: true` frontmatter) — all other files pass through silently.

---

This README was written by [Claude Code](https://claude.com/claude-code).
