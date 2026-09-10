# Nelson♦Σ Symbol Legend

Shared vocabulary for `SKILL.md` and every file under `references/`. Symbols replace recurring phrases; rules, thresholds, tool names, paths, and template labels stay literal.

## Arrays

Structure is written as arrays, not prose or tables.

| Form | Meaning |
|---|---|
| `x = [a, b, c]` | ordered sequence: steps in order, fields in order |
| `{a, b}` | unordered set |
| `{f: v, g: w}` | record with named fields |
| `a\|b\|c` | enum, pick one |
| `g[{f, h}]` | repeated group: zero or more records |
| `f~` | optional field or step |
| `r(a, b, c)` | tuple; positional meaning is given once above it |
| `Τ_x` | template x; written to disk as `Field: value` lines in array order, groups as bullets |
| `𝔾ₙ` | gate n; ⛔ until every element holds |

## Domains

`Ω` step Ω₁–Ω₈ · `Μ` mode Μ₁–Μ₄ · `Σ` action station Σ₀–Σ₃ · `Ε` Estimate question Ε₁–Ε₇ · `Ρ` role · `Φ` standing order Φ₁–Φ₆ · `Δ` damage control Δ₁–Δ₄ · `Τ` template

## Ω Steps

```
Ω = [
  Ω₁ ⚓ Sailing Orders     (conversation; /goal when unwatched),
  Ω₂ 🔭 The Estimate       (plan mode; Explore agents),
  Ω₃ 🗺️ Battle Plan        (plan mode),
  Ω₄ 🚢 Form the Squadron  (plan mode, design only),
  Ω₅ 🫡 Permission to Sail (ExitPlanMode | AskUserQuestion),
  Ω₆ 📊 Quarterdeck Rhythm (Agent, SendMessage, ListAgents, task list, quarterdeck-report.md),
  Ω₇ 🎯 Action Stations    (evidence gate; plan mode for Σ₂+ captains),
  Ω₈ 📜 Stand Down         (captains-log.md, .nelson/memory.md)
]
```

## Μ Execution Modes

```
Μ = [
  Μ₁ single-session (ADM works tasks in order; no agents),
  Μ₂ subagents      (named Agent dispatches; CPTs return results; no peer messaging),
  Μ₃ agent-team     (named dispatches + SendMessage between CPTs + shared task list where enabled),
  Μ₄ workflow       (one Workflow run; user opt-in required; Σ₂+ as separate approved runs)
]
```

## Σ Action Stations

```
Σ = [Σ₀ 🟢 Patrol, Σ₁ 🟡 Caution, Σ₂ 🟠 Action, Σ₃ 🔴 Trafalgar]   controls cumulative: Σ₃ ⊇ Σ₂ ⊇ Σ₁ ⊇ Σ₀
classify(first yes wins) = [irreversible ∨ regulated → Σ₃, security ∨ privacy ∨ data integrity ∨ high blast → Σ₂, user-visible ∨ reliability ∨ coupled → Σ₁, else → Σ₀]
```

Definitions: `references/action-stations.md`.

## Ρ Roles

```
squadron = [ADM admiral (this session; coordinates, never implements),
            CPT captain (one ship, one task; coordinates crew or implements when 0 crew),
            RCN red-cell navigator (adversarial review only),
            RM royal marine (short-lived sub-agent a CPT deploys for one sortie; ≤2 per ship)]
crew     = [XO Executive Officer, PWO Principal Warfare Officer, NO🔒 Navigating Officer,
            MEO Marine Engineering Officer, WEO Weapon Engineering Officer, LOGO Logistics Officer, COX🔒 Coxswain]
🔒 = read-only (Explore)
```

Sizing, ships, marines, models: `references/squadron.md`.

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
