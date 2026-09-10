# Τ Battle Plan

`{mission-dir}/battle-plan.md` = prose authority (commander's intent, per-task briefs). `{mission-dir}/battle-plan.json` = execution-data authority (owners, dependencies, Σ tiers, file ownership). Keep aligned: edit one → mirror in the other.

∀ CPT brief opens with the commander's intent from the Estimate (§2): one paragraph, verbatim, so every ship sails under a shared purpose.

```text
Commander's intent:
[One paragraph from the Estimate §2 — prepended to every captain's brief.]

Workflow suitability:
[Non-workflow modes: one line on why workflow was not selected.]

Workflow Charter: [include only when mode is workflow or hybrid-workflow]
- Execution primitive: [workflow | hybrid-workflow]
- Suitability: [why scripted orchestration is appropriate]
- Phases: [probe/full run/stage names and purposes]
- Human gates: [approval required before/after stages]
- Verification contract: [how findings or edits become accepted]
- Cost guardrail: [probe, scope cap, token/time limit, stop trigger]
- Fallback mode: [agent-team | single-session]

Task ID:
- Name:
- Owner: [assigned at Ω₄]
- Ship (if crewed): [assigned at Ω₄]
- Crew manifest (if crewed):
- Deliverable:
- Dependencies:
- Station tier (0-3):
- File ownership (if code):
- Modification targets (if extending): [specific functions, env vars, config to modify — not replace. Omit for greenfield tasks.]
- Acceptance criteria (inherited from effect):
  - [Criterion 1 — captain chooses verification method: test | type-check | lint | review | visual]
  - [Criterion 2 — ...]
- Validation required:
- Rollback note required: yes/no
- admiralty-action-required: yes/no
  - action: [one sentence — what the human must do]
  - timing: before this task starts | after this task completes
  - blocks: [task name or "stand-down"]
```

- **Modification targets:** task extends existing code → name the specific functions, variables, config to modify in place. Flows from Ε₁ Reconnaissance ∧ Ε₄ Terrain: where those identified existing code, the plan preserves that specificity. Omit for greenfield tasks.
- **Acceptance criteria:** ∀ task inherits its parent effect's criteria (Estimate §3). CPT chooses the verification method per criterion (test, type-check, lint, review, visual). Quarterdeck records ∀ outcome (`pass` / `fail` / `not-verified`) via `nelson-data.py estimate-outcome`.
- **JSON schema:** the battle-plan `task` object accepts optional `acceptance_criteria: list[str]` (programmatic aggregation of verification outcomes) ∧ optional `modification_targets: list[str]` (functions, env vars, config to modify in place).
- **Workflow advisory fields:** the top-level battle-plan object ~ `execution_primitive`, `workflow_suitability`, `workflow_phases`, `human_gates`, `verification_contract`, `cost_guardrail`, `fallback_mode`, `workflow`. Advisory in v1: they document the charter and gates, ¬ generate runnable `.claude/workflows/*.js`.
- **`admiralty-action-required`:** `yes` when a step cannot be completed by an agent (human must interact with an external system, provide credentials or URLs, or act where only a human can). Fill consciously ∀ task: blank = a claim that no human action is needed. `yes` → ADM surfaces it in the Admiralty Action List before agents launch; CPT invokes the `awaiting-admiralty` standing order when the step is reached.
- **`blocks:`** names the task that cannot proceed until the human acts. The Admiralty Action List displays it as `unblocks:` (same task name, inverted label).
