# Nelson♦Σ

[![Version](https://img.shields.io/github/v/release/johnpeterman72/nelson.sigma)](https://github.com/johnpeterman72/nelson.sigma/releases)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-skill-blueviolet)](https://docs.anthropic.com/en/docs/claude-code)

**If what works well for people works well for agents, there are few finer examples of organisational tradition than the Royal Navy to base the rules on. Nelson♦Σ does that for Claude Code, in one skill, with no code.**

Nelson♦Σ is a zero-dependency edition of [Nelson](https://github.com/harrymunro/nelson) by Harry Munro. It keeps the eight-step doctrine, the risk tiers, the anti-pattern guards, and the recovery playbook. It drops the Python scripts and hooks, because Claude Code now provides what they did: a task list, plan mode, worktree isolation, background agents, standing goals, and memory. The skill text is written in the symbolic notation of [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma).

<!-- markdownlint-disable-next-line MD036 -->
*8 steps · 4 execution modes · 4 risk tiers · 6 standing orders · 4 damage-control procedures · 9 files · 0 scripts*

<p align="center">
  <img src="docs/images/1024px-Young_Nelson-min.jpg" alt="Captain Horatio Nelson" width="500">
  <br>
  <em>Captain Horatio Nelson — John Francis Rigaud, 1781. Image: Wikimedia Commons</em>
</p>

## Contents

- [Quick Start](#quick-start)
- [What it does](#what-it-does)
- [How it works](#how-it-works)
- [Why no scripts](#why-no-scripts)
- [Symbolic compression](#symbolic-compression)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [File structure](#file-structure)
- [Mission artifacts](#mission-artifacts)
- [Credits](#credits)
- [Disclaimer](#disclaimer)
- [License](#license)

## Quick Start

```
/plugin marketplace add johnpeterman72/nelson.sigma
/plugin install nelson@nelson-sigma-marketplace
```

Describe the mission and mention Nelson:

```
Use Nelson to migrate the payment module from Stripe v2 to v3
```

Nelson writes sailing orders, scouts the codebase, drafts a battle plan, presents it once for approval, forms a squadron of named captains, runs quarterdeck checkpoints, and writes a captain's log when it stands down.

## What it does

An eight-step operational framework, Ω₁ to Ω₈:

1. **Sailing Orders** ⚓ — outcome, success metric, constraints, stop criteria
2. **The Estimate** 🔭 — the 7 Question Maritime Tactical Estimate, in plan mode: reconnaissance, intent, effects, terrain, forces, coordination, control
3. **Battle Plan** 🗺️ — effects become tasks with owners, dependencies, file ownership, acceptance criteria, and a written Standing Order Check
4. **Form the Squadron** 🚢 — execution mode, captain count, ship names, crew, models
5. **Get Permission to Sail** 🫡 — one approval gate before anything spawns
6. **Quarterdeck Rhythm** 📊 — checkpoints for progress, blockers, budget, and anti-patterns, written to disk
7. **Action Stations** 🎯 — every task classified by risk; evidence before completion
8. **Stand Down** 📜 — captain's log with decisions, evidence, follow-ups, and lessons for the next mission

## How it works

### Execution modes

| Μ | Mode | When | Runs on |
|---|---|---|---|
| Μ₁ | `single-session` | sequential, low complexity, same-file editing | the session itself |
| Μ₂ | `subagents` | parallel, independent tasks reporting only to the admiral | named background `Agent` dispatches |
| Μ₃ | `agent-team` | parallel tasks that coordinate with each other, or four or more captains | named agents plus `SendMessage`, shared task list when enabled |
| Μ₄ | `workflow` | large fan-out audits, migrations, or cross-checked research, when you ask for one | the `Workflow` tool, with a probe first |

### Chain of command

```
                          ┌───────────┐
                          │  Admiral  │
                          └─────┬─────┘
                  ┌─────────────┼─────────────┐
                  ▼             ▼             ▼
           ┌───────────┐ ┌───────────┐ ┌───────────┐
           │  Captain   │ │  Captain   │ │ Red-Cell  │
           │ HMS Daring │ │ HMS Kent   │ │ Navigator │
           └─────┬─────┘ └─────┬─────┘ └───────────┘
            ┌────┼────┐   ┌────┼────┐
            ▼    ▼    ▼   ▼    ▼    ▼
           XO  PWO  MEO  PWO  NO  COX
```

The admiral coordinates and never implements. Each captain commands a named ship, one task, and crews only the roles the task needs: XO integration, PWO implementation, NO research, MEO testing, WEO config, LOGO docs, COX standards review. NO and COX are read-only. A captain may deploy up to two Royal Marines, short-lived sub-agents, for single sorties. A red-cell navigator joins for medium and high risk work and only ever reviews.

### Action stations

| Σ | Name | When | Controls |
|---|---|---|---|
| Σ₀ 🟢 | Patrol | low blast radius, easy rollback | validation evidence, rollback step |
| Σ₁ 🟡 | Caution | user-visible, coupled, moderate impact | independent review, negative test, rollback note |
| Σ₂ 🟠 | Action | security, privacy, data integrity | red-cell review, failure-mode checklist, admiral go/no-go; captain spawned in plan mode |
| Σ₃ 🔴 | Trafalgar | irreversible, regulated, safety-sensitive | minimal scope, human confirmation, two-step verification, contingency plan |

### Standing orders

Six anti-pattern guards, answered in writing before the plan is final and scanned at every checkpoint: Right-Sized Squadron, Admiral at the Helm, Pressed Crew, Split Keel, Drifting Anchorage, Paid Off. Upstream's seventeen orders express these six rules; the consolidation loses none of them.

### Damage control

Four procedures for when things go wrong: Man Overboard (stuck or unreachable agent), Session Resumption (interruption or compaction), Context Exhaustion (admiral or ship running out of room), Recovery and Abort (rollback, escalation, scuttle and re-form).

### Templates

Nine output shapes in one file: sailing orders, battle plan, crew briefing, marine deployment brief, quarterdeck report, relief brief, red-cell review, captain's log, memory entry.

## Why no scripts

Upstream Nelson v2.4 carries about 9,000 lines of Python. Each module now has a native equivalent:

| Upstream | Nelson♦Σ uses |
|---|---|
| mission JSON files and event log | the task list, `battle-plan.md`, `quarterdeck-report.md` |
| phase engine and tool gate | the Ω sequence and plan mode |
| conflict scan and radar, file-ownership checks | `isolation: "worktree"` on the Agent tool |
| hull-integrity monitoring, damage reports, turnover packets | compaction at safe points; a relief brief and a fresh dispatch |
| circuit breakers | the checkpoint budget line and idle notifications |
| goal composer | a `/goal` condition shape in the tool mapping |
| cross-mission pattern mining | `.nelson/memory.md` and the session memory directory |
| enforcement hooks | the admiral's gates, the red-cell navigator, and the human at Σ₃ |

What is genuinely lost: deterministic enforcement (a hook can reject a task marked complete without evidence; a rule can only insist) and quantitative cross-mission analytics. If you want either, the last script-bearing edition is tag [`v2.4.0-sigma`](https://github.com/johnpeterman72/nelson.sigma/releases/tag/v2.4.0-sigma), and upstream carries the full machinery.

## Symbolic compression

Every rule, gate, threshold, and template survives; the prose around them is cut and recurring phrases become symbols: Ω₁–Ω₈ for the steps, Μ₁–Μ₄ for modes, Σ₀–Σ₃ for risk tiers, Ε₁–Ε₇ for the Estimate questions, ADM CPT RCN RM for roles, Φ and Δ for orders and procedures, ⛔ 📖 💾 ⏸ for gates. The vocabulary is in [`skills/nelson/references/sigma-legend.md`](skills/nelson/references/sigma-legend.md).

Measured with a byte-pair tokenizer (cl100k as a proxy for Claude's):

| File set | Upstream v2.4.0 | Nelson♦Σ | Saving |
|---|---|---|---|
| `SKILL.md` (loads every mission) | 9,804 | 3,549 | 64% |
| `references/` (loaded on demand) | 40,854 | 12,079 | 70% |
| whole skill | 50,658 | 15,628 | 69% |

An honest note: the glyphs themselves are not where the saving comes from. Greek letters and math operators cost about as many tokens as the words they replace. The saving comes from cutting explanatory prose, consolidating seventeen files into six, dropping the 10,000-token CLI reference the scripts needed, and abbreviating the roles and gates. Judge by tokens, never by kilobytes: symbols are multi-byte.

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed and authenticated. That is all.
- **Optional:** the [agent teams](https://code.claude.com/docs/en/agent-teams) feature adds a shared task list for the `agent-team` mode. Plugin installs ship a `settings.json` that enables it; for manual installs add `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` to your settings. Nelson works without it.
- **Optional:** run inside [tmux](https://github.com/tmux/tmux/wiki) to watch every ship in its own pane.

## Installation

### Plugin install (recommended)

```
/plugin marketplace add johnpeterman72/nelson.sigma
/plugin install nelson@nelson-sigma-marketplace
```

<details>
<summary>Prompt-based install</summary>

```
Install skills from https://github.com/johnpeterman72/nelson.sigma
```

Claude clones the repo, copies the skill into your project's `.claude/skills/`, and cleans up. Ask for `~/.claude/skills/` to install globally.

</details>

<details>
<summary>Manual install</summary>

```bash
git clone https://github.com/johnpeterman72/nelson.sigma.git /tmp/nelson-sigma
mkdir -p .claude/skills
cp -r /tmp/nelson-sigma/skills/nelson .claude/skills/nelson
rm -rf /tmp/nelson-sigma
```

Nothing else is needed; there are no scripts to wire up.

</details>

<details>
<summary>Verify</summary>

Ask Claude Code `What skills are available?` and look for `nelson`, or say "Use Nelson to..." followed by a task.

</details>

## Usage

Nelson loads when your request matches its description; no slash command is required.

```
Use Nelson to migrate the payment processing module from Stripe v2 to v3
```

Force a mode:

```
Use an agent team with Nelson to refactor the authentication system across
the API layer, frontend, and test suite
```

Provide full sailing orders:

```
Use Nelson to deliver this:

Sailing orders:
- Outcome: All API endpoints return consistent error responses
- Success metric: Zero test failures, all error responses match the schema
- Deadline: This session

Constraints:
- Token/time budget: Stay under 50k tokens
- Forbidden actions: Do not modify the database schema

Scope:
- In scope: src/api/ and tests/api/
- Out of scope: Frontend error handling
```

For long unattended runs, Nelson offers a `/goal` composed from the sailing orders so the session cannot stop before the captain's log is written. `/nelson` also works directly.

## File structure

```
.claude-plugin/           # Plugin and marketplace manifests
settings.json             # Enables agent teams (optional feature)
skills/nelson/
├── SKILL.md              # Ω₁–Ω₈, gates, standing-order and damage-control index
└── references/
    ├── sigma-legend.md     # Symbol vocabulary and mission directory layout
    ├── squadron.md         # Modes, sizing, roles, crew, ships, marines, models
    ├── action-stations.md  # Risk tiers and controls
    ├── tool-mapping.md     # Native tool per operation; workflows; standing goal
    ├── standing-orders.md  # Φ₁–Φ₆
    ├── damage-control.md   # Δ₁–Δ₄
    ├── templates.md        # Nine output shapes
    └── the-estimate.md     # 7 Question Maritime Tactical Estimate
agents/nelson.md          # Agent definition bound to the skill
scripts/check-references.sh
demos/battleships/        # Example application built with Nelson
```

## Mission artifacts

Each mission writes three or four markdown files under a timestamped directory, and the skill appends lessons to a memory file the next mission reads first.

```
.nelson/
├── memory.md                       # adopt / avoid / recurring standing orders
└── missions/{YYYY-MM-DD_HHMM}-{slug}/
    ├── battle-plan.md              # sailing orders, Standing Order Check, task briefs, formation
    ├── estimate.md                 # when The Estimate was conducted
    ├── quarterdeck-report.md       # latest checkpoint; history rotates to -N.md
    └── captains-log.md             # written at stand-down; marks the mission complete
```

`.nelson/` is git-ignored by default. Un-ignore `memory.md` to share learned patterns with a team.

## Credits

Nelson♦Σ is a fork of [Nelson](https://github.com/harrymunro/nelson) by [Harry Munro](https://github.com/harrymunro); the doctrine, templates, and names are his. The symbolic notation follows [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma).

## Disclaimer

This project is not associated with, endorsed by, or affiliated with the British Royal Navy or the UK Ministry of Defence. All Royal Navy terminology and references are used purely as a creative framework for organising software development tasks.

## License

MIT — see [LICENSE](LICENSE) for details.
