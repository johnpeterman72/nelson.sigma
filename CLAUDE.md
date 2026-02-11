# Nelson♦Σ

Nelson is a token-efficient Claude Code skill for coordinating agent teams. It uses symbolic notation (Greek letters, arrays, compressed shorthand) to encode a 6-step operational framework: Define, Compose, Plan, Monitor, Verify, Close.

## Project structure

```
.claude/skills/nelson/
  SKILL.md              — Legend + Ω₁₋₆ workflow + Φ standing orders + doctrine
  references/
    roles.md            — Ρ roles, mode selection, crew tree, unit names
    stations.md         — Σ₀₋₃ risk tiers + controls
    templates.md        — Τ output templates (define, plan, manifest, checkpoint, review, close)
    damage-control.md   — Δ recovery procedures
  agents/
    openai.yaml         — Agent interface definition
demos/                  — Example applications
```

## No build system

This is a documentation-driven skill with zero runtime dependencies. There is no package manager, no build step, and no test suite.

## Testing changes

Install the skill locally and run a mission to verify. Either tell Claude Code "Install skills from https://github.com/johnpeterman72/nelson.sigma" or copy the skill directory manually:

```bash
mkdir -p <target-project>/.claude/skills
cp -r .claude/skills/nelson <target-project>/.claude/skills/nelson
```

Then invoke `/nelson` in Claude Code.

## Code style

- Token-efficient: symbolic notation, arrays, compressed shorthand
- Emojis for user-facing output; symbols for AI-internal logic
- Markdown for all documentation; YAML for agent interfaces
- The battleships demo (`demos/battleships/index.html`) uses vanilla HTML/CSS/JS with no dependencies

## Git workflow

- Branch from `main`
- Commit messages: imperative mood, concise summary line
- Open a PR for review

## Environment

`CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` must be set to enable the `agent-team` execution mode (configured in `.claude/settings.local.json`).
