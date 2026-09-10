# Nelson♦Σ Symbol Legend

Shared vocabulary for `SKILL.md` and every file under `references/`. Symbols replace recurring phrases; rules, thresholds, tool names, paths, and template labels stay literal.

## Domains

| Symbol | Domain | Index |
|---|---|---|
| Ω | workflow step | Ω₁–Ω₈ |
| Μ | execution mode | Μ₁–Μ₄ |
| Σ | action station (risk tier) | Σ₀–Σ₃ |
| Ε | Estimate question | Ε₁–Ε₇ |
| Ρ | role | ADM CPT RCN RM + crew |
| Φ | standing order (anti-pattern) | Φ₁–Φ₆ |
| Δ | damage-control procedure | Δ₁–Δ₄ |
| Τ | template | sections of `references/templates.md` |

## Ω Steps

| Ω | Step | Native surface |
|---|---|---|
| Ω₁ ⚓ | Issue Sailing Orders | conversation; `/goal` when the mission runs unwatched |
| Ω₂ 🔭 | Conduct The Estimate | plan mode; `Explore` agents |
| Ω₃ 🗺️ | Draft Battle Plan | plan mode |
| Ω₄ 🚢 | Form the Squadron | plan mode (design only, nothing spawns) |
| Ω₅ 🫡 | Get Permission to Sail | `ExitPlanMode`, or `AskUserQuestion` outside plan mode |
| Ω₆ 📊 | Run Quarterdeck Rhythm | `Agent`, `SendMessage`, `ListAgents`, task list, `quarterdeck-report.md` |
| Ω₇ 🎯 | Set Action Stations | evidence gate; plan mode for Σ₂+ captains |
| Ω₈ 📜 | Stand Down And Log Action | `captains-log.md`, `.nelson/memory.md` |

## Μ Execution Modes

| Μ | Mode | Surface |
|---|---|---|
| Μ₁ | `single-session` | ADM works tasks in order; no agents |
| Μ₂ | `subagents` | named `Agent` dispatches; CPTs return results; no peer messaging |
| Μ₃ | `agent-team` | named `Agent` dispatches + `SendMessage` between CPTs + shared task list where enabled |
| Μ₄ | `workflow` | one `Workflow` script run; user opt-in required; Σ₂+ work as separate approved runs |

## Σ Action Stations

| Σ | Name | Trigger (first yes wins) |
|---|---|---|
| Σ₃ 🔴 | Trafalgar | irreversible, regulated, severe-incident |
| Σ₂ 🟠 | Action | security, privacy, data integrity, high blast radius |
| Σ₁ 🟡 | Caution | user-visible, reliability or cost, coupled to other work |
| Σ₀ 🟢 | Patrol | none of the above |

Controls are cumulative: Σ₁ ⊇ Σ₀, Σ₂ ⊇ Σ₁, Σ₃ ⊇ Σ₂. Definitions: `references/action-stations.md`.

## Ρ Roles

| Abbr | Role | Notes |
|---|---|---|
| ADM | admiral | the session running this skill; coordinates, never implements |
| CPT | captain | commands one ship, one task; coordinates crew or implements when 0 crew |
| RCN | red-cell navigator | adversarial review only; never implementation |
| RM | royal marine | short-lived sub-agent a CPT deploys for one sortie; max 2 per ship |
| XO | Executive Officer | integration across sub-tasks |
| PWO | Principal Warfare Officer | core implementation |
| NO 🔒 | Navigating Officer | codebase research, read-only (`Explore`) |
| MEO | Marine Engineering Officer | testing and validation |
| WEO | Weapon Engineering Officer | config, infra, integration |
| LOGO | Logistics Officer | docs and dependencies |
| COX 🔒 | Coxswain | standards review, read-only (`Explore`) |

Sizing, ship names, marines, models: `references/squadron.md`.

## Operators

| Symbol | Meaning |
|---|---|
| → | then, results in |
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
  memory.md                       cross-mission patterns: adopt, avoid, recurring Φ; appended at Ω₈, read at Ω₁
  missions/{YYYY-MM-DD_HHMM}-{slug}/
    battle-plan.md                sailing orders, Standing Order Check, task briefs, formation (Ω₅; owners updated as they change)
    estimate.md                   the Estimate, one H2 per question (~)
    quarterdeck-report.md         latest checkpoint; history rotates to quarterdeck-report-N.md
    captains-log.md               Ω₈; its presence marks the mission complete
```

`.nelson/` is git-ignored by default; un-ignore `memory.md` to share learned patterns with a team.
