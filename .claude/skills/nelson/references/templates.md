# Τ Templates

## Τ.define (Ω₁ ⚓ output)
outcome: | metric: | deadline:
constraints: [token_budget, reliability_floor, compliance, forbidden_actions]
scope: [in, out] | stop_criteria: [] | handoff_artifacts: []

## Τ.plan (Ω₃ ⚔️ output)
task_id: | name: | owner: | unit: | crew_manifest:
deliverable: | deps: [] | station(Σ₀₋₃): | file_ownership: [] | validation:

## Τ.manifest (LEAD crew plan)
unit: | lead: | task:
crew: [{role: subtask}] | sequence: [{step, deps}] | token_budget:

## Τ.checkpoint (Ω₄ 📊 output)
time: | progress: {pending:[], active:[], done:[]}
blockers: [{item, owner, action, eta}]
budget: {spent, remaining} | risks: [{risk, mitigation}]
decision: continue|rescope|stop + rationale

## Τ.review (REV output)
target: | assumption_tested: | failure_mode: | blast_radius:
checks: [{check, result}]
recommendation: approve|revise|block + required_changes

## Τ.close (Ω₆ 📜 output)
planned_vs_achieved: [planned, achieved, metric_result]
artifacts: [{item, location}] | decisions: [{what, why}]
evidence: [] | risks: [{risk, owner, next}]
followups: [{item, owner, due}]
patterns: {worked:[], avoid:[]}
