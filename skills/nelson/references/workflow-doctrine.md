# Workflow Doctrine

Dynamic workflows are a fleet asset: broad, repeatable orchestration, still governed by sailing orders, Σ controls, cost discipline, and explicit verification.

## What a workflow is

A JavaScript script owning orchestration: fan-out to many agents, intermediate results in script variables, aggregation, one review or migration pattern repeated across a large scope. Starts `export const meta = {...}`; body uses `agent()`, `parallel()`, `pipeline()`, `phase()` under top-level `await`. One-shot (written and run inline) ∨ saved as `.claude/workflows/<name>.js` (project) ∨ `~/.claude/workflows/<name>.js` (personal), re-run as `/<name>`; runs watched, paused, resumed from the `/workflows` view.

Nelson v1 ✗ compile ∨ invoke scripts: it produces the doctrine, battle-plan charter, gates, and verification contract; the bridge below yields a runnable skeleton.

`ultracode` is not a Μ: a Claude Code `xhigh` effort/automation setting that may let Claude choose dynamic workflows. Nelson keeps the charter, approval gate, Σ tiering, and acceptance criteria.

## Ultracode readiness

∀ hold before ultracode on a Nelson mission: sailing orders state outcome, metric, deadline, forbidden actions, budget · file ownership ∨ target scope bounded for broad fan-out · Station 2/3 (Σ₂|Σ₃) gates written as stage boundaries, ¬ hoped-for mid-run prompts · verification contract covers accepted, rejected, uncertain outputs · fallback mode named. Any missing → plan first; Μ₅ ∨ a conventional Μ₃ rather than let ultracode choose blindly.

## Execution modes

- Μ₄ `workflow`: one approved autonomous run (large fan-out, repeatable review, broad migration, audit, cross-checked research).
- Μ₅ `hybrid-workflow`: Nelson-gated sequence of separate stages (human review between stages, staged risk controls, Σ₂|Σ₃ approvals).

Σ₂|Σ₃ → prefer Μ₅. No arbitrary mid-run human input: end the run, present results, launch the next only after approval.

## Suitability checks

Fits: codebase-wide audits across many files, packages, services · large migrations, one transformation repeated across independent targets · cross-checked research ∨ review, several agents inspecting the same question independently · repeatable verification sweeps with clear acceptance criteria · broad issue triage, findings collected, ranked, de-duplicated.

Prefer Μ₃ ∨ Μ₂ ∨ Μ₁: tightly coupled work in the same files · frequent human steering · rich peer-to-peer negotiation among a few CPTs · expected cost exceeds value ∨ scope unbounded · acceptance criteria vague enough to amplify ambiguity.

## Sounding the Channel

Probe one small representative slice before full scope (one package, a handful of files, one migration pattern, one research slice). Report: slice ∧ why it represents the wider channel · agents completed / total · elapsed ∧ approximate token burn · accepted, rejected, uncertain findings · verification evidence for accepted output · charter changes needed before broad execution. ⛔ full run only after ADM reviews the probe; Μ₅ also ⏸ explicit user approval of the next stage.

## Workflow charter fields

Μ₄|Μ₅ → compact Workflow Charter in `battle-plan.md`, same data in `battle-plan.json` where useful:

- `execution_primitive`: `workflow` ∨ `hybrid-workflow`
- `workflow_suitability`: why a workflow is appropriate
- `workflow_phases`: planned stages, including probes and full runs
- `human_gates`: approvals required before or after stages
- `verification_contract`: how findings or edits become accepted
- `cost_guardrail`: budget limits, scope limits, stop triggers
- `fallback_mode`: usually `agent-team`, ∨ `single-session` for tightly coupled recovery

## From charter to a runnable script

Fields map one-to-one onto a Workflow script: a starting skeleton ADM hands over after approval so a power user runs the approved plan instead of re-deriving it, ¬ something Nelson executes. `workflow_phases` → a `phase('...')` group, ∨ a `pipeline`/`parallel` stage per phase · `verification_contract` → a verify stage that adversarially re-checks ∀ finding and drops those failing the contract · `cost_guardrail` → a Sounding-the-Channel probe first, plus a budget/agent-count guard before the full run · `human_gates` → Μ₅: a script boundary where the run ends; the next stage is a separate approved run (no mid-run gate) · `fallback_mode` → the mode to drop to on low probe signal.

A review-shaped charter maps to the canonical find-then-verify pipeline:

```javascript
export const meta = {
  name: 'audit-<scope>',            // from the charter's workflow name
  description: '<workflow_suitability>',
  phases: [{ title: 'Probe' }, { title: 'Review' }, { title: 'Verify' }],
}
// Sounding the Channel — cost_guardrail: prove signal on one slice first.
const probe = await agent(`Review ${args.slice} for <finding type>.`, { phase: 'Probe', schema: FINDINGS })
if (!probe.findings.length) return { stopped: 'probe found no signal; fall back to <fallback_mode>' }

// Full run — one reviewer per target, each finding verified as its review lands.
const results = await pipeline(
  args.targets,                                    // bounded per cost_guardrail
  t => agent(`Review ${t} for <finding type>.`, { phase: 'Review', schema: FINDINGS }),
  review => parallel(review.findings.map(f => () =>  // verification_contract
    agent(`Adversarially verify: ${f.summary}. Default to rejected if uncertain.`, { phase: 'Verify', schema: VERDICT })
      .then(v => ({ ...f, verdict: v })))),
)
return { confirmed: results.flat().filter(Boolean).filter(f => f.verdict?.real) }
```

Σ₂ outputs still need red-cell review, Σ₃ outputs explicit human confirmation, so a Station 2/3 audit runs as Μ₅: end the run at ∀ `human_gate`, resume only after approval.

## Verification contract

Completion ≠ acceptance; the battle plan must say what counts as verified. Common contracts: independent reviewer confirmation for findings above a risk threshold · tests, lint, type checks, ∨ targeted manual review for generated edits · red-cell review for Σ₂+ outputs · rejected ∧ uncertain findings surfaced separately, ¬ hidden in the summary · sample-based audit of accepted findings before final synthesis · rollback notes for any edit-bearing result. Σ still applies: Σ₂ adversarial review; Σ₃ explicit human confirmation ∧ contingency planning.

## Cost controls

Workflows can spend far more tokens than ordinary delegation (many agents, state held in script). Apply: Sounding the Channel before broad execution · cap target scope per phase · cap agent count per wave where possible · stop after repeated agent failures, ¬ retry blindly · approximate token burn required in telemetry · narrow ∨ fall back on low signal, high duplicate findings, ∨ excessive cost.

## Telemetry

∀ checkpoint ∨ stage boundary, from what Claude Code exposes ∨ manual entry from the `/workflows` view: workflow name ∧ phase · agents completed / total · failed agents · elapsed time · token burn ∨ best available estimate · accepted, rejected, uncertain findings · current status · next gate.

Events (`references/structured-data.md`): `workflow_run_started`, `workflow_probe_completed`, `workflow_run_completed`, `workflow_run_stopped`. Nelson v1 event data is deliberately loose; acceptable fields: `workflow_name`, `phase`, `status`, `agents_total`, `agents_completed`, `tokens_used`, `elapsed_minutes`, `summary`, `next_gate`.

## Standing goal and workflows

Goal = mission-level completion barrier; workflow = orchestration primitive inside the mission. Different levels, no interference, but coordinate: ✗ set a `/goal` inside a run, set it once at Ω₁ (`references/goal-alignment.md`). The evaluator does not see inside a run, and a run has no mid-run human gate → the goal cannot police workflow internals; it enforces that results are reviewed ∧ accepted in the transcript before the session may stop. Μ₅: word the goal so it is met only once ∀ planned stage has completed ∧ its results are accepted in the conversation, so the session cannot stand down between stages while a human gate is pending.

## Damage-control mapping

- Low signal after probe → stop, narrow the charter, ∨ fall back to `agent-team`.
- Budget burn too high → halt at the next stage boundary, reduce scope, record the cost finding.
- Agent failures cluster on one target → isolate it for a human-reviewed ∨ `single-session` task.
- Contradictory findings → independent reviewer confirmation before acceptance.
- Station 2/3 gate needed → stop the run; resume only as a separate approved `hybrid-workflow` stage.
- Workflow runner ∨ tool allowlist problem → ✗ improvise around prompts; surface the blocked call, revise the charter ∨ allowlist.
