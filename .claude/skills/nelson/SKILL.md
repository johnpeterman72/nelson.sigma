---
name: nelson
description: Coordinate agent teams through a 6-step framework with risk-tiered controls and structured output.
version: "2.0.0"
---

# Nelson♦Σ

## Legend

### Domains
Ω = workflow | Ρ = roles | Σ = stations | Δ = damage control | Φ = standing orders | Τ = templates

### Roles
COORD=coordinator | LEAD=team-lead | REV=reviewer
Crew roles: see references/roles.md

### Symbols
→ results-in | ! violation | ? decision | ∅ none | ~ conditional | ✓ required | ✗ forbidden

### User-Facing Output
⚓ define | 🚢 compose | ⚔️ plan | 📊 monitor | 🎯 verify | 📜 close
🟢 Σ₀ | 🟡 Σ₁ | 🟠 Σ₂ | 🔴 Σ₃

### Reference Files
When a path appears (e.g., `references/stations.md`), Read that file before proceeding. Paths are relative to this skill directory.

---

## Ω₁ ⚓ Define

Define: outcome, metric, deadline (1 sentence each)
Set: token_budget, reliability_floor, compliance, forbidden_actions
Scope: in[], out[], stop_criteria[], handoff_artifacts[]
?no user structure → references/templates.md#define

## Ω₂ 🚢 Compose

Mode ?first match: sequential → single-session | parallel-independent → subagents | parallel+coordination → agent-team
Size: 1 COORD + 2-7 LEAD; +1 REV if Σ₁+; max 10. Details → references/roles.md
LEAD → assign unit name + decide crew via references/roles.md
!Φ check before forming

## Ω₃ ⚔️ Plan

Split mission → independent tasks w/ clear deliverables
Each task: owner, deps, file_ownership(exclusive), station_tier(Σ₀₋₃)
1 task in-progress per agent unless mission requires multitask
LEAD tasks: include unit manifest; crew=0 → "LEAD implements directly"
?structure → references/templates.md#plan + references/templates.md#manifest
!Φ check on file assignment + scope

## Ω₄ 📊 Monitor

COORD = coordination + unblock only
Checkpoints at fixed cadence (15-30 min):
  - Update task states: pending | in_progress | completed
  - Identify blockers → concrete next action
  - Confirm crew have active sub-tasks; flag idle/mismatch
  - Track burn vs token/time budget
  - Re-scope early if drift from mission metric
?difficulties → references/damage-control.md
?structure → references/templates.md#checkpoint
!Φ check if COORD implementing or scope drifting

## Ω₅ 🎯 Verify

Apply station tier from references/stations.md
Evidence required before task=completed:
  - Test/validation output
  - Failure modes + rollback notes
  - REV review for Σ₁+
Quality triggers: task completion | agent idle w/ unverified output | before final synthesis
Crew outputs must align w/ role boundaries (references/roles.md)
!Φ check if tasks lack tier or REV assigned impl
Completion: all tasks=completed + stop_criteria met + metric achieved → Ω₆

## Ω₆ 📜 Close

Stop/archive all agent sessions (including crew)
Produce close log → references/templates.md#close:
  - Decisions + rationale
  - Diffs/artifacts
  - Validation evidence
  - Open risks + follow-ups
Record reusable patterns + failure modes

---

## Φ Standing Orders

!violations — check at Ω₂, Ω₃, Ω₄, Ω₅

Φ₁  becalmed      !team when work=sequential → single-session; need ≥2 concurrent
Φ₂  no-canvas     !add agent w/o reducing crit-path → identify parallel task first
Φ₃  split-keel    !same file → multiple LEADs → exclusive ownership; serialize or split
Φ₄  drift         !scope expand w/o re-scope → re-scope or split at checkpoint
Φ₅  coord-impl    !COORD does implementation → COORD=coord only; delegate → LEAD
Φ₆  rev-impl      !REV assigned impl tasks → REV=review/validate only
Φ₇  unclassified  !task w/o station tier → classify via Σ before exec
Φ₈  lead-impl     !LEAD impl when crew active → LEAD=coord crew; delegate impl
Φ₉  over-crew     !crew all roles regardless → crew only needed roles
Φ₁₀ under-crew    !1 crew for atomic task → LEAD impl directly; 0 crew
Φ₁₁ wrong-role    !crew work outside role → match sub-task → role def

---

## Doctrine

- Optimize mission throughput, not equal work distribution
- Replace stalled agents over waiting on undefined blockers
- Keep coordination messages targeted + concise
- Escalate uncertainty early w/ options + 1 recommendation
- Allocate ≤15% of token budget to coordination (COORD messages, checkpoints, planning)
