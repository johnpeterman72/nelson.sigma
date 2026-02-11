---
name: nelson
description: Coordinate agent teams through a 6-step framework with risk-tiered controls and structured output.
---

# Nelson♦Σ

## Legend

### Domains
Ω = workflow | Ρ = roles | Σ = stations | Δ = damage control | Φ = standing orders | Τ = templates

### Roles
COORD=coordinator | LEAD=team-lead | REV=reviewer
XO=integration | PWO=implementation | NO=research(read-only) | MEO=testing | WEO=config/infra | LOGO=docs/deps | COX=standards(read-only)

### Symbols
→ results-in | ! violation | ? decision | ∅ none | ~ conditional | ✓ required | ✗ forbidden

### User-Facing Output
⚓ define | 🚢 compose | ⚔️ plan | 📊 monitor | 🎯 verify | 📜 close
🟢 Σ₀ | 🟡 Σ₁ | 🟠 Σ₂ | 🔴 Σ₃

---

## Ω₁ ⚓ Define

Define: outcome, metric, deadline (1 sentence each)
Set: token_budget, reliability_floor, compliance, forbidden_actions
Scope: in[], out[], stop_criteria[], handoff_artifacts[]
?no user structure → refs/templates.md#define

## Ω₂ 🚢 Compose

Mode ?first match:
  sequential|coupled|same-files → single-session
  parallel, COORD-report-only → subagents
  parallel + cross-coordination → agent-team
Size: default 1 COORD + 3-6 LEAD; +1 REV if Σ₁+; max 10 team agents
LEAD → assign unit name from refs/roles.md#units matching task weight
LEAD → decide crew via refs/roles.md#crew-tree
!Φ check before forming

## Ω₃ ⚔️ Plan

Split mission → independent tasks w/ clear deliverables
Each task: owner, deps, file_ownership(exclusive), station_tier(Σ₀₋₃)
1 task in-progress per agent unless mission requires multitask
LEAD tasks: include unit manifest; crew=0 → "LEAD implements directly"
?structure → refs/templates.md#plan + refs/templates.md#manifest
!Φ check on file assignment + scope

## Ω₄ 📊 Monitor

COORD = coordination + unblock only
Checkpoints at fixed cadence (15-30 min):
  - Update task states: pending | in_progress | completed
  - Identify blockers → concrete next action
  - Confirm crew have active sub-tasks; flag idle/mismatch
  - Track burn vs token/time budget
  - Re-scope early if drift from mission metric
?difficulties → refs/damage-control.md
?structure → refs/templates.md#checkpoint
!Φ check if COORD implementing or scope drifting

## Ω₅ 🎯 Verify

Apply station tier from refs/stations.md
Evidence required before task=completed:
  - Test/validation output
  - Failure modes + rollback notes
  - REV review for Σ₁+
Quality triggers: task completion | agent idle w/ unverified output | before final synthesis
Crew outputs must align w/ role boundaries (refs/roles.md)
!Φ check if tasks lack tier or REV assigned impl

## Ω₆ 📜 Close

Stop/archive all agent sessions (including crew)
Produce close log → refs/templates.md#close:
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
