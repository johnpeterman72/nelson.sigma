# Squadron Composition Reference

Choose Μ and team size at Ω₄.

## Mode Selection

**User preference override:** user explicitly requests a mode (e.g. "use agent teams") → ⛔ honour it. User preference beats the matrix below; ✗ second-guess or override.

Evaluate all five; pick the best fit. Two apply → prefer the one giving CPTs more autonomy while preserving required human gates.

- Μ₁ `single-session`: sequential ∨ tightly coupled ∨ mostly the same files
- Μ₂ `subagents`: parallel ∧ ∀ CPT task fully independent; no shared coordination surface needed
- Μ₃ `agent-team`: parallel ∧ CPTs benefit from a shared task list ∨ peer messaging ∨ coordinated deliverables. Also ≥4 CPTs ∨ user requests it
- Μ₄ `workflow`: one approved dynamic workflow run with large fan-out ∨ repeatable review or migration logic ∨ codebase-wide audit ∨ cross-checked research. The workflow = one fleet asset, ¬ ordinary CPTs
- Μ₅ `hybrid-workflow`: Nelson-gated sequence of workflow stages; a probe ∨ Σ₂/Σ₃ sign-off ∨ human approval is required between runs

## Decision Matrix

| Condition | Preferred Mode | Why |
|---|---|---|
| single critical path, low ambiguity | `single-session` | lowest coordination overhead |
| parallel, fully independent tasks | `subagents` | no cross-CPT dependencies |
| parallel implementation with dependencies | `agent-team` | teammate-to-teammate coordination |
| ≥4 parallel CPTs | `agent-team` | shared task list simplifies coordination at scale |
| high threat ∨ high blast radius | `agent-team` + RCN | explicit control points |
| large repeatable audit, migration, or research fan-out | `workflow` | dynamic workflow scripts orchestrate many agents and aggregate results |
| workflow-suitable mission with stage gates ∨ Σ₂/Σ₃ approvals | `hybrid-workflow` | human approval between separate workflow runs |
| user explicitly requests a mode | as requested | user preference overrides the matrix |

Before Μ₄|Μ₅ → 📖 `workflow-doctrine.md`; record workflow suitability, probe, verification contract, cost guardrail, and fallback mode in the battle plan. Nelson v1 produces a workflow charter; it does not generate or launch `.claude/workflows/*.js`.

## Team Sizing

CPT count = number of independently executable work units, ¬ a complexity tier. Map the dependency graph; count tasks that can run concurrently with zero shared state; that count is the target.

**Zero shared state** = no file-ownership overlap ∧ no sequencing dependency (task B does not need task A's output). Peer coordination across module boundaries (e.g. agreeing an API contract) is permitted and handled by ADM.

- 1 CPT per independent work unit
- merge tasks onto one CPT only when they share files ∨ have a sequencing dependency ∨ are so small that agent setup cost clearly exceeds the work
- medium/high threat → +1 RCN
- exactly one ADM
- squadron cap: 10 squadron-level agents (ADM, CPTs, RCN). Crew are additional, ≤4 per CPT, per `references/crew-roles.md`

8 independent analysis sections → 8 CPTs; 3 independent modules → 3. In doubt → add a CPT: idle context is cheap, serialised work is slow. Cost-optimised mission (token-budget priority in sailing orders) → 📖 `references/model-selection.md` before defaulting to maximum parallelism.

## Role Guide

- ADM: sailing orders, delegation, dependency tracking, blocker resolution. May perform read-only recombination of completed ship outputs once ∀ ships have reported successfully; ✗ generative synthesis directly → assign a CPT or a dedicated synthesis task
- CPT: commands a ship; breaks the task into sub-tasks, coordinates crew, verifies outputs. Implements directly only when atomic (0 crew). ADM sets initial crew at Ω₄; CPT may request mid-task adjustments with ADM approval. Crew roles XO PWO NO MEO WEO LOGO COX and crewing rules: `references/crew-roles.md`
- RCN: challenges assumptions, validates outputs, checks rollback readiness

## Anti-Patterns

Full list: Standing Orders table in SKILL.md.

## Worktree Isolation

File-ownership boundaries hard to draw ∨ several CPTs must modify overlapping files → `isolation: "worktree"` on `Agent`: ∀ CPT gets an isolated copy of the repository via a git worktree. A stronger alternative to file ownership per `standing-orders/split-keel.md`. Use when:

- several CPTs need to edit the same files
- merge-conflict risk is high ∧ split-keel cannot resolve it
- tasks are large enough to justify the merge cost

**Trade-off:** prevents conflicts during execution but requires merging afterwards. ADM coordinates the merge.
