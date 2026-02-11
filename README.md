# Nelson♦Σ

<p align="center">
  <img src="1024px-Young_Nelson-min.jpg" alt="Captain Horatio Nelson" width="500">
  <br>
  <em>Captain Horatio Nelson — John Francis Rigaud, 1781. Image: Wikimedia Commons</em>
</p>

A token-efficient Claude Code skill for coordinating agent teams. Nelson♦Σ uses symbolic notation — Greek letter domains, compressed arrays, and shorthand operators — to encode a full agent coordination framework at a fraction of the token cost of [the original Nelson](https://github.com/harrymunro/nelson).

## What it does

Nelson♦Σ gives Claude a six-step operational framework (Ω₁–Ω₆) for tackling complex missions:

1. **⚓ Define** — Outcome, metric, deadline, constraints, scope, stop criteria
2. **🚢 Compose** — Execution mode (single-session, subagents, agent-team), team size, unit assignment
3. **⚔️ Plan** — Task breakdown with owners, deps, file ownership, station tiers
4. **📊 Monitor** — Checkpoints for progress, blockers, budget tracking
5. **🎯 Verify** — Risk-tiered controls (Σ₀–Σ₃), verification evidence, review gates
6. **📜 Close** — Archive sessions, produce structured close log

### Symbolic compression

Inspired by [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma), the skill encodes its entire instruction set using:

- **Greek letter domains** — `Ω` workflow, `Ρ` roles, `Σ` stations, `Δ` damage control, `Φ` standing orders, `Τ` templates
- **Subscript indexing** — `Ω₁`–`Ω₆`, `Σ₀`–`Σ₃`, `Φ₁`–`Φ₁₁`
- **Symbolic operators** — `→` results-in, `!` violation, `?` decision, `~` conditional
- **Emoji for user-facing output** — `🟢 🟡 🟠 🔴` station tiers, `⚓ 🚢 ⚔️ 📊 🎯 📜` workflow steps
- **Array notation** — Templates as field arrays, standing orders as single-line rules

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI installed and authenticated
- **Recommended:** Enable [agent teams](https://code.claude.com/docs/en/agent-teams) for team coordination. Nelson works without it (single-session or subagent modes), but agent teams unlock the `agent-team` execution mode. Enable in your [settings.json](https://code.claude.com/docs/en/settings):

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

- **For split-pane visibility:** Run Claude Code inside [tmux](https://github.com/tmux/tmux/wiki). Agent teams auto-detect tmux and give every teammate a dedicated split pane.

## Installation

### Prompt-based (recommended)

Open Claude Code and say:

```
Install skills from https://github.com/johnpeterman72/nelson.sigma
```

Claude will clone the repo, copy the skill into your project's `.claude/skills/` directory, and clean up. To install globally, ask Claude to install to `~/.claude/skills/` instead.

### Manual

```bash
# Project-level (recommended for teams)
git clone https://github.com/johnpeterman72/nelson.sigma.git /tmp/nelson-sigma
mkdir -p .claude/skills
cp -r /tmp/nelson-sigma/.claude/skills/nelson .claude/skills/nelson
rm -rf /tmp/nelson-sigma

# Or user-level (personal, all projects)
cp -r /tmp/nelson-sigma/.claude/skills/nelson ~/.claude/skills/nelson
```

Then commit `.claude/skills/nelson/` to version control so your team can use it.

### Verify installation

```
What skills are available?
```

You should see `nelson` listed. Invoke directly with `/nelson`.

## Usage

### Let Claude invoke it automatically

Claude reads the skill description and loads it when your request matches:

```
I need to refactor the authentication system. The work spans the API layer,
the frontend, and the test suite. Use nelson to coordinate this.
```

### Invoke it directly

```
/nelson Migrate the payment processing module from Stripe v2 to v3
```

### Provide structured input

For maximum control, provide your own mission definition:

```
/nelson

Define:
- Outcome: All API endpoints return consistent error responses
- Metric: Zero test failures, all error responses match the schema
- Deadline: This session

Constraints:
- Token budget: Stay under 50k tokens
- Forbidden: Do not modify the database schema

Scope:
- In: src/api/ and tests/api/
- Out: Frontend error handling
```

## How it works

<p align="center">
  <img src="HMP_RNM_1973_76-001.jpg" alt="HMS Victory anchored off the Isle of Wight" width="700">
  <br>
  <em>HMS Victory anchored off the Isle of Wight — John Wilson Carmichael (1799–1868), National Museum of the Royal Navy, Portsmouth</em>
</p>

### Execution modes

| Mode | When | How |
|------|------|-----|
| `single-session` | Sequential, low complexity, same-file editing | Single session, tasks in order |
| `subagents` | Parallel, workers report to coordinator only | Independent [subagents](https://code.claude.com/docs/en/sub-agents) |
| `agent-team` | Parallel, workers coordinate with each other | [Agent team](https://code.claude.com/docs/en/agent-teams) with peer communication |

### Team hierarchy

Nelson uses a three-tier hierarchy: COORD (coordinator) delegates to LEADs (team leads), each LEAD commands a named unit with specialist crew.

```
                        ┌─────────┐
                        │  COORD  │
                        └────┬────┘
                ┌────────────┼────────────┐
                ▼            ▼            ▼
         ┌───────────┐ ┌──────────┐ ┌─────────┐
         │   LEAD    │ │   LEAD   │ │   REV   │
         │  Daring   │ │   Kent   │ │ reviewer│
         └─────┬─────┘ └────┬─────┘ └─────────┘
          ┌────┼────┐  ┌────┼────┐
          ▼    ▼    ▼  ▼    ▼    ▼
         XO  PWO  MEO PWO  NO  COX
```

**Team level:**

- **COORD** — Coordinates the mission, delegates, resolves blockers, final synthesis. Always exactly one.
- **LEAD** — Commands a named unit. Breaks task into sub-tasks, crews roles, verifies outputs. Implements directly only for atomic tasks. Typically 2–7 per mission.
- **REV** — Challenges assumptions, validates outputs, checks rollback. Added at Σ₁+.

**Unit level (crew per LEAD, 0–4 members):**

| Role | Abbr | Function | subagent_type |
|------|------|----------|---------------|
| Exec Officer | XO | Integration & orchestration | general-purpose |
| Principal Warfare | PWO | Core implementation | general-purpose |
| Navigator | NO | Codebase research (read-only) | Explore |
| Marine Engineer | MEO | Testing & validation | general-purpose |
| Weapon Engineer | WEO | Config, infra, integration | general-purpose |
| Logistics | LOGO | Docs & dependency mgmt | general-purpose |
| Coxswain | COX | Standards review (read-only) | Explore |

NO and COX are read-only — they report findings but never modify files.

Units are named from Royal Navy warships, matched to task weight: frigates (general), destroyers (high-risk), patrol vessels (small), flagships (critical-path), submarines (research).

### Station tiers (Σ₀–Σ₃)

Every task is classified by risk before execution. Controls are cumulative:

| Tier | When | Controls |
|------|------|----------|
| 🟢 Σ₀ | Low blast radius, easy rollback | Validate + rollback note |
| 🟡 Σ₁ | User-visible, moderate impact | + non-author review + negative test |
| 🟠 Σ₂ | Security/compliance/data integrity | + REV review + failure checklist + COORD go/no-go |
| 🔴 Σ₃ | Irreversible, regulated, safety-sensitive | + human confirm + 2-step verify + contingency plan |

### Standing orders (Φ₁–Φ₁₁)

Anti-patterns checked at every workflow step:

| ID | Rule |
|----|------|
| Φ₁ | Don't team when work is sequential |
| Φ₂ | Don't add agents without reducing critical path |
| Φ₃ | Don't assign same file to multiple LEADs |
| Φ₄ | Don't expand scope without re-scoping |
| Φ₅ | COORD must not implement |
| Φ₆ | REV must not be assigned implementation |
| Φ₇ | Every task needs a station tier |
| Φ₈ | LEAD must not implement when crew active |
| Φ₉ | Don't crew every role regardless of need |
| Φ₁₀ | Don't spawn 1 crew for an atomic task |
| Φ₁₁ | Don't assign crew work outside their role |

### Templates (Τ)

Seven output templates encoded as field arrays — define, plan, manifest, checkpoint, rescope, review, close. The AI fills these structured formats at each workflow step.

## Skill file structure

```
.claude/skills/nelson/
├── SKILL.md                    # Legend + Ω₁₋₆ workflow + Φ standing orders + doctrine
└── references/
    ├── roles.md                # Ρ roles, mode selection, crew tree, unit names
    ├── stations.md             # Σ₀₋₃ risk tiers + cumulative controls
    ├── templates.md            # Τ output templates as field arrays
    └── damage-control.md       # Δ recovery procedures
```

5 files total. The original Nelson used 30 files — this version consolidates everything through symbolic compression.

## Customisation

### Modify templates

Edit `references/templates.md` to adjust output fields. Templates are field arrays — add, remove, or rename fields as needed.

### Adjust risk tiers

Edit `references/stations.md` to change controls per station tier. Controls are cumulative (`Σ₁ = Σ₀ + ...`), so changes cascade upward.

### Change team sizing

Edit `references/roles.md` to adjust mode selection rules, team sizes, or the crew decision tree.

## Compatibility notes

- **Subagents** are a stable Claude Code feature and work out of the box.
- **Agent teams** are experimental and disabled by default. See [Prerequisites](#prerequisites) for setup. Without agent teams, Nelson falls back to `single-session` or `subagents` mode. Full details: [Agent teams documentation](https://code.claude.com/docs/en/agent-teams).

## Credits

Nelson♦Σ is a token-optimised fork of [Nelson](https://github.com/harrymunro/nelson) by [Harry Munro](https://github.com/harrymunro). Symbolic compression approach inspired by [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma).

## Disclaimer

This project is not associated with, endorsed by, or affiliated with the British Royal Navy or the UK Ministry of Defence. All Royal Navy terminology and references are used purely as a creative framework for organising software development tasks.

## License

MIT — see [LICENSE](LICENSE) for details.
