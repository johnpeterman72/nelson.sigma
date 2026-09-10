# Nelson♦Σ Symbol Legend

Shared vocabulary for `SKILL.md` and every file under `references/`. Symbols replace recurring phrases; rules, thresholds, paths, and commands stay literal.

## Domains

| Symbol | Domain | Index |
|---|---|---|
| Ω | workflow step | Ω₁–Ω₈ |
| Π | engine phase (`nelson-phase.py`) | SAILING_ORDERS → ESTIMATE → BATTLE_PLAN → FORMATION → PERMISSION → UNDERWAY → STAND_DOWN |
| Μ | execution mode | Μ₁–Μ₅ |
| Σ | action station (risk tier) | Σ₀–Σ₃ |
| Η | hull integrity (context remaining) | Η🟢 Η🟡 Η🔴 Η⚫ |
| Ε | Estimate question | Ε₁–Ε₇ |
| Ρ | role | ADM CPT RCN RM + crew |
| Φ | standing order (anti-pattern) | Φ₁–Φ₁₇ |
| Δ | damage-control procedure | Δ₁–Δ₁₁ |
| Τ | admiralty template | one file per template |

## Ω Steps and Π Phases

| Ω | Step | Π phase while active |
|---|---|---|
| Ω₁ ⚓ | Issue Sailing Orders | SAILING_ORDERS |
| Ω₂ 🔭 | Conduct The Estimate | ESTIMATE |
| Ω₃ 🗺️ | Draft Battle Plan | BATTLE_PLAN |
| Ω₄ 🚢 | Form the Squadron | FORMATION |
| Ω₅ 🫡 | Get Permission to Sail | PERMISSION |
| Ω₆ 📊 | Run Quarterdeck Rhythm | UNDERWAY |
| Ω₇ 🎯 | Set Action Stations | UNDERWAY |
| Ω₈ 📜 | Stand Down And Log Action | STAND_DOWN |

`NP advance` moves Π forward one phase; each transition has an exit validator (see `references/structured-data.md`).

## Μ Execution Modes

| Μ | Mode | Coordination surface |
|---|---|---|
| Μ₁ | `single-session` | ADM works tasks in order; `TaskCreate/Update/List/Get` |
| Μ₂ | `subagents` | `Agent(subagent_type)`; CPTs report via return value only |
| Μ₃ | `agent-team` | `TeamCreate` → `Agent(team_name, name)`; `TaskList` + `SendMessage` |
| Μ₄ | `workflow` | one approved dynamic-workflow run, treated as a fleet asset |
| Μ₅ | `hybrid-workflow` | workflow stages with a human gate between each run |

## Σ Action Stations

| Σ | Name | Trigger (first yes wins) |
|---|---|---|
| Σ₃ 🔴 | Trafalgar | irreversible, regulated, severe-incident |
| Σ₂ 🟠 | Action | security, privacy, data integrity, high blast radius |
| Σ₁ 🟡 | Caution | user-visible, reliability/cost, coupled to other work |
| Σ₀ 🟢 | Patrol | none of the above |

Controls are cumulative: Σ₁ ⊇ Σ₀, Σ₂ ⊇ Σ₁, Σ₃ ⊇ Σ₂. Full definitions: `references/action-stations.md`.

## Η Hull Integrity

| Η | Status | Context remaining | Action |
|---|---|---|---|
| Η🟢 | Green | ≥ 75 % | continue |
| Η🟡 | Amber | 60–74 % | prepare turnover brief |
| Η🔴 | Red | 40–59 % | request relief |
| Η⚫ | Critical | < 40 % | emergency turnover, relief now |

Thresholds and procedure: `references/damage-control/hull-integrity.md`.

## Ρ Roles

| Abbr | Role | Notes |
|---|---|---|
| ADM | admiral | the session running this skill; coordinates, never implements |
| CPT | captain | commands one ship (one task); coordinates crew or implements when 0 crew |
| RCN | red-cell navigator | adversarial review only; never implementation |
| RM | royal marine | short-lived sub-agent deployed by a CPT; max 2 per ship |
| XO | Executive Officer | integration across sub-tasks |
| PWO | Principal Warfare Officer | core implementation |
| NO 🔒 | Navigating Officer | codebase research, read-only (`Explore`) |
| MEO | Marine Engineering Officer | testing and validation |
| WEO | Weapon Engineering Officer | config, infra, integration |
| LOGO | Logistics Officer | docs and dependencies |
| COX 🔒 | Coxswain | standards review, read-only (`Explore`) |

Crew sizing and ship names: `references/crew-roles.md`. Marines: `references/royal-marines.md`.

## Script Aliases

Expand before running; shell state does not persist between tool calls.

| Alias | Command |
|---|---|
| ND | `python3 .claude/skills/nelson/scripts/nelson-data.py` |
| NP | `python3 .claude/skills/nelson/scripts/nelson-phase.py` |
| NCS | `python3 .claude/skills/nelson/scripts/nelson_conflict_scan.py` |

Plugin install path: `${CLAUDE_PLUGIN_ROOT}/skills/nelson/scripts/`. Global install: `~/.claude/skills/nelson/scripts/`. `{mission-dir}` is the path printed by `ND init`.

## Operators

| Symbol | Meaning |
|---|---|
| → | then, results in |
| ⟶ | phase transition |
| ∧ ∨ ¬ | and, or, not |
| ∀ ∃ ∄ | for each, exists, none |
| ? | condition ("if") |
| : | else branch |
| ~ | optional or conditional |
| ! | anti-pattern, violation |
| ⛔ | hard gate: MUST NOT proceed until satisfied |
| ⏸ | stop and wait for the human |
| 📖 | MUST read the named file before proceeding |
| 💾 | MUST write to disk (chat output alone does not satisfy) |
| 🔒 | read-only |
| ✓ ✗ | required, forbidden |
| ≤ ≥ | at most, at least |
| `[x]` | placeholder to fill |

## Mission Directory

```
.nelson/
  .active-{SESSION_ID}              session marker
  missions/{YYYY-MM-DD_HHMMSS}_{SESSION_ID}/
    sailing-orders.json  battle-plan.json  mission-log.json  fleet-status.json
    estimate.md  battle-plan.md  quarterdeck-report.md  captains-log.md
    damage-reports/{ship}.json    turnover-briefs/
  memory/patterns.json  standing-order-stats.json
```

Schemas and event types: `references/structured-data.md`.
