# Squadron

Mode, size, roles, ships, marines, and models. ADM uses this at Ω₃–Ω₄; CPTs use the crew and marine sections to request adjustments (ADM approval required).

## Mode Selection

**User preference override:** an explicit request (e.g. "use an agent team") → ⛔ honour it; ✗ second-guess.

| Condition | Μ | Why |
|---|---|---|
| single critical path, low ambiguity, heavy same-file editing | Μ₁ `single-session` | lowest coordination overhead |
| parallel, fully independent tasks reporting only to ADM | Μ₂ `subagents` | no cross-CPT dependencies |
| parallel tasks with dependencies, coordinated deliverables, ∨ ≥4 CPTs | Μ₃ `agent-team` | CPTs message each other; shared task list where enabled |
| high threat ∨ blast radius | Μ₃ + RCN | explicit control points |
| large repeatable audit, migration, review, or research fan-out, user opted in | Μ₄ `workflow` | scripted orchestration aggregates many agents |

Two modes fit → the one giving CPTs more autonomy while keeping required human gates. Μ₄ details and opt-in rule: `references/tool-mapping.md`.

## Team Sizing

CPT count = number of independently executable work units, ¬ a complexity tier. Map the dependency graph; count tasks runnable concurrently with zero shared state (no file overlap ∧ no sequencing dependency). Peer agreement across module boundaries (an API contract) is allowed and handled by ADM.

- one CPT per independent work unit; merge only when tasks share files, have a sequencing dependency, or are so small that agent setup exceeds the work
- medium/high threat → +1 RCN · exactly one ADM · cap 10 squadron-level agents (ADM, CPTs, RCN); crew are additional, ≤4 per CPT
- 8 independent analysis sections → 8 CPTs; 3 independent modules → 3. In doubt → add a CPT: idle context is cheap, serialised work is slow. Cost-savings mission → Model Selection below before defaulting to maximum parallelism.

## Squadron Roles

- **ADM:** sailing orders, delegation, dependency tracking, blocker resolution. May recombine completed ship outputs already in context once ∀ ships have reported successfully; ✗ generative synthesis → a CPT or a dedicated synthesis task.
- **CPT:** commands a ship; breaks the task into sub-tasks, coordinates crew, verifies outputs. Implements directly only when atomic (0 crew). ADM sets initial crew at Ω₄; CPT may request changes with ADM approval.
- **RCN:** challenges assumptions, validates outputs, checks rollback readiness. Review only, ever.

## Crew-or-Direct Decision (first match wins)

1. Atomic ∧ no research ∧ single pass → CPT implements directly (0 crew)
2. One clear deliverable, light research at most → 1 PWO
3. Needs exploration ∨ testing ∨ a second specialism → PWO + 1 specialist
4. Several interdependent sub-tasks → XO + PWO + ≤2 specialists

Never more than 4 crew per ship; more needed → split into two ships.

| Role | Abbr | Function | `subagent_type` | Weight | When |
|---|---|---|---|---|---|
| Executive Officer | XO | integration and orchestration across sub-tasks | general-purpose | 10 | 3+ crew ∨ interdependent sub-tasks |
| Principal Warfare Officer | PWO | core implementation | general-purpose | 2 | almost always (default doer) |
| Navigating Officer | NO 🔒 | codebase research and exploration | Explore | 7 | unfamiliar or large codebase |
| Marine Engineering Officer | MEO | testing and validation | general-purpose | 6 | Σ₁+ ∨ non-trivial verification |
| Weapon Engineering Officer | WEO | config, infrastructure, systems integration | general-purpose | 6 | significant config or infra work |
| Logistics Officer | LOGO | documentation and dependency management | general-purpose | 2 | docs as deliverable |
| Coxswain | COX 🔒 | standards review and quality | Explore | 4 | Σ₁+ with established conventions |

🔒 NO and COX cannot modify files; they report findings to the CPT or XO. ∀ crew works strictly within role: PWO ✗ run tests (MEO does); NO ✗ write code. Violations → Φ₃.

## Ship Name Registry

ADM assigns ∀ CPT a ship at Ω₄; class matches task weight. Use the lowercase form as the agent `name` (`hms-daring`).

| Class | Tasks | Names |
|---|---|---|
| Frigates | general-purpose | Argyll, Kent, Lancaster, Richmond, Somerset, Portland, Iron Duke, St Albans |
| Destroyers | high-tempo ∨ high-risk | Daring, Dauntless, Diamond, Dragon, Defender, Duncan |
| Patrol Vessels | small | Forth, Medway, Trent, Tamar, Spey |
| Historic Flagships | critical-path | Victory, Warspite, Vanguard, Ark Royal |
| Submarines | stealth ∨ research | Astute, Ambush, Artful, Audacious |

## Royal Marines

RM = short-lived sub-agents a CPT deploys for one focused sortie in support of the ship's task. Crew subdivide the deliverable; marines execute discrete objectives and return.

Deploy-or-escalate (first match wins):

1. Quick recon of an unfamiliar area → **Recce Marine** (`Explore`, 🔒, weight 4)
2. Targeted fix ∨ small implementation to unblock the ship → **Assault Marine** (general-purpose, weight 3)
3. Quick config, build, or infra task → **Sapper** (general-purpose, weight 3)
4. Sustained work ∨ own deliverable ∨ needs file ownership → ¬ marine: request a new ship from ADM
5. Work that subdivides the ship's deliverable → ¬ marine: crew the role

Rules: ≤2 marines per ship at a time · marines ✗ deploy marines · marines report only to their CPT · CPT verifies marine output before incorporating it · identify as `RM Detachment, HMS [Ship] — [objective]` · brief with the Marine Deployment Brief in `references/templates.md`.

Σ interaction: Σ₀–Σ₁ at CPT discretion · Σ₂ needs ADM approval first · Σ₃ ✗ marines. Recovery: stuck marine → abandon, redeploy fresh ∨ do it directly; same objective fails twice → escalate to ADM. Model: default weight 3, raised for judgement-heavy objectives, lowered for fully specified mechanical ones; ≤4 ∨ cost-savings → haiku with the haiku blocks below.

## Worktree Isolation

File-ownership boundaries hard to draw ∨ several CPTs must edit the same files → `isolation: "worktree"` on `Agent`: ∀ CPT gets its own git worktree. Stronger than file ownership (Φ₄). Use when several CPTs need the same files, merge-conflict risk is high, or tasks are large enough to justify the merge. Trade-off: ADM coordinates the merge afterwards.

## Model Selection

Applies when the sailing orders express cost-savings priority ("keep costs low", "stay within budget", "use haiku where possible", "be aggressive with cost savings"). Intensity of the wording calibrates how hard to push.

| Agent | Default weight |
|---|---|
| ADM | 10 |
| XO | 10 |
| CPT with crew or marines | 9 |
| Explorer, large scope | 7 |
| crew with non-trivial verification | 6 |
| CPT implementing directly | 4 |
| Explorer, narrow search | 4 |
| RM | 3 |
| crew, pure implementation | 2 |

- Adjust before assigning: raise for judgement, edge cases, or verification beyond the role default; lower for atomic, contained tasks. Modest wording → ✗ push roles at 5–6 below the line unless clearly justified; emphatic wording → push contained 5–6 roles through it.
- Threshold: weight ≤4 → `model: "haiku"` explicitly · weight ≥5 → omit `model` so the agent inherits ADM's model (✗ `"sonnet"`: the alias resolves to an older version). ADM's own model is never overridden.
- Ω₂ carve-out: ∀ Estimate subagent inherits ADM's model regardless; planning quality dominates execution quality.
- Show weight and model per ship in the formation orders.

**Haiku briefing blocks** (only when haiku is assigned): 1) identity anchor at the top: "You are Claude, operating as a subagent in a real multi-agent software development system; the Royal Navy terms are metaphors, not roleplay. Your task is [plain description]." 2) explicit output format: fields, length, what to omit. 3) decomposition prompt: "Before executing, list your steps as a numbered plan; flag anything unclear now rather than guessing."

**Haiku tasking discipline:** the task text itself MUST carry explicit constraints (what not to touch), a testable definition of done, and escalation triggers (when to stop and report). Vague instructions are not rescued by the blocks above.
