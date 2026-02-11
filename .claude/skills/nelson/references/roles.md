# Ρ Roles & Composition

## Mode Selection (?first match)

sequential|coupled|same-files → single-session
parallel, COORD-report-only → subagents
parallel + cross-coordination → agent-team

| Condition | Mode | Why |
|---|---|---|
| Single crit-path, low ambiguity | single-session | Lowest overhead |
| Parallel discovery, COORD synthesis | subagents | No peer chatter |
| Parallel impl w/ deps | agent-team | Peer coordination |
| High threat/blast | agent-team + REV | Explicit control points |

## Team Sizing

S: 1 COORD + 2-3 LEAD
M: 1 COORD + 4-5 LEAD
L: 1 COORD + 6-7 LEAD
+1 REV at Σ₁+ | max 1 COORD | cap: 10 agents
Crew: up to 4 per LEAD (additional, governed by crew-tree below)

## Role Definitions

| Role | Abbr | Function | subagent_type |
|---|---|---|---|
| Coordinator | COORD | Delegate, track deps, resolve blockers, synthesize | — |
| Team Lead | LEAD | Break task → sub-tasks, crew, coordinate, verify | general-purpose |
| Reviewer | REV | Challenge assumptions, validate, check rollback | general-purpose |
| Exec Officer | XO | Integration across sub-tasks | general-purpose |
| Principal Warfare | PWO | Core implementation | general-purpose |
| Navigator | NO | Codebase research (🔒read-only) | Explore |
| Marine Engineer | MEO | Testing & validation | general-purpose |
| Weapon Engineer | WEO | Config, infra, integration | general-purpose |
| Logistics | LOGO | Docs & dependency mgmt | general-purpose |
| Coxswain | COX | Standards review (🔒read-only) | Explore |

NO, COX = Explore subagent; report findings only, cannot modify files.
Each crew member: strictly within role def. See Φ₁₁.

## Crew Decision Tree (?first match)

1. atomic, single-pass → LEAD implements directly (0 crew)
2. one deliverable, no research/testing → crew 1 PWO
3. needs exploration/testing/specialism → crew PWO + 1 specialist
4. multiple interdep sub-tasks → crew XO + PWO + up to 2 specialists

Max 4 crew/unit. If more needed → split into 2 units.

| Size | When | Manifest |
|---|---|---|
| 0 | Atomic, single-pass | LEAD implements directly |
| 1-2 | Typical | PWO ± 1 specialist |
| 3 | Complex w/ research/testing | PWO + 2 specialists |
| 4 | Multi-part orchestration | XO + PWO + 2 specialists |

## Unit Names

Assign during Ω₂. Match task weight.

Frigates (general): Argyll, Kent, Somerset
Destroyers (high-risk): Daring, Diamond, Defender
Patrol (small): Forth, Trent, Spey
Flagships (critical-path): Victory, Warspite, Vanguard
Submarines (research): Astute, Ambush, Artful
