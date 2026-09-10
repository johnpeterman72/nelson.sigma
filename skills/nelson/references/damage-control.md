# Damage Control

Four procedures.

```
escalation = crew → CPT → ADM → Admiralty (the human); the agent pauses → reports {issue, options, one recommendation} → the next level decides within its authority ∨ passes it up with a summary
authority = {crew: own sub-task, CPT: own task, ADM: re-assign ∧ replace ∧ re-time ∧ raise Σ ∧ descope within the orders, Admiralty: scope changes ∧ budget extensions ∧ abort ∧ confirmation of ∀ irreversible action}
```

## Δ₁ Man Overboard

Agent unresponsive, looping, producing nothing useful, unreachable, or the message bus is down.

```
Δ₁ = [record {agent, task, last progress, partial outputs on disk},
      SendMessage shutdown_request; 3 unacknowledged → abandon the request, note it, continue,
      spawn a replacement of the same role with Τ_relief (references/templates.md): task, dependencies, partial outputs, blockers, file ownership → resumes from the last verified point, ¬ scratch,
      update the battle plan ∧ task list]
comms_failure (several ships unreachable ∨ SendMessage errors ∨ task list stale) =
  [record unreachable ships ∧ tasks, recover their disk output, redistribute to reachable ships ∨ fresh dispatches,
   team surface down → continue in Μ₂ with replacements briefed from recovered context, cannot continue → Δ₄,
   ⛔ ADM ✗ implement the lost work (Φ₂)]
prevention = [≥4 ships → intermediate outputs to disk, long tasks → "write a checkpoint to disk" in the brief, ∀ checkpoint → ListAgents]
crew_variant = CPT runs the same steps at ship level; same role fails twice → escalate to ADM with summary ∧ recommendation
manual_cleanup (last resort: graceful shutdown failed 3× ∧ team cannot be deleted) = [remove ~/.claude/teams/{team-name} ∧ ~/.claude/tasks/{team-name} (paths vary by build), verify with ListAgents, log affected agents in the captain's log, re-issue their tasks from the last verified point]
```

## Δ₂ Session Resumption

New session, interruption (context limit, crash, timeout), or compaction mid-mission.

```
Δ₂ = [locate: newest .nelson/missions/ directory without captains-log.md; several candidates → ⏸ user picks → {mission-dir},
      📖 quarterdeck-report.md (fallback: highest quarterdeck-report-N.md) → battle-plan.md → estimate.md~,
      ListAgents → ships still on station; reconcile with the task list where available,
      ∀ in_progress task: verify partial outputs on disk against the deliverable; discard anything unconfirmed,
      re-issue sailing orders: original outcome, scope less completed work; re-form the squadron at minimum size, briefed from recovered context,
      ? /goal recorded in battle-plan.md ∧ none active (bare /goal) → re-issue,
      resume the quarterdeck rhythm at the next checkpoint]
safe_compaction = [after Ω₁, Ω₂, Ω₃, Ω₅ spawning, ∀ Ω₆ checkpoint]; unsafe = between Ω₅ approval and the spawn turn; ∀ compaction summary MUST name {mission-dir} ∧ the coordination role
new_mission = mkdir -p {mission-dir}; previous missions persist untouched; rotated reports (quarterdeck-report-N.md) are history, ✗ delete
```

## Δ₃ Context Exhaustion

```
ADM = [context is managed by the harness; the on-disk quarterdeck report is the recovery point,
       approaching compaction → checkpoint now → 💾 the report → /compact at a safe point (Δ₂); ✗ let compaction land between approval and spawn]
ship (degraded output: repetition ∨ missed instructions ∨ shallow reasoning; a long-running task; a ship asking for relief) =
  [signal the ship to pause ∧ 💾 outputs,
   ship writes Τ_relief to {mission-dir}/relief-{ship}-{HHMM}.md,
   spawn the replacement (class fits the remaining work; the brief path is the first line of its prompt),
   reassign the task, shutdown_request to the relieved ship, update the battle plan,
   chained reliefs carry one line per predecessor; a third relief on one task → ADM re-scopes (too large ∨ ill-defined for one ship)]
crew_overrun (crew burning tokens ∨ time out of proportion) =
  [CPT pauses ∀ crew → reviews each against its sub-task → {scope creep → descope, blocked → resolve ∨ reassign, role mismatch → correct role ∨ CPT does it} → resume with a revised budget,
   cannot recover → ADM with a recommendation: extend ∨ descope ∨ split to a second ship]
```

## Δ₄ Recovery and Abort

```
partial_rollback (completed task found faulty, others sound) =
  [identify the faulty task ∧ its dependents, faulty → in_progress ∧ dependents → pending,
   revert code via version control; archive other artifacts as reverted,
   re-assign to the original owner ∨ a replacement, re-execute from the original definition with the failure mode as a constraint,
   verified → resume dependents]
escalation_triggers = [ambiguous requirement ∨ criteria → CPT asks ADM,
                       agents disagree → ADM decides; unsure → ⏸ Admiralty,
                       scope creep → ADM re-scopes ∨ ⏸ approval,
                       unexpected out-of-scope dependency → pause dependents, ⏸ Admiralty,
                       Σ₂+ risk found mid-task → raise Σ, apply its controls,
                       budget near limit with critical work left → ⏸ extend ∨ descope ∨ abort,
                       planned human-action step → Awaiting Admiralty (Φ₆), ¬ a failure]
scuttle_and_reform (mission cannot succeed: budget exhausted with critical tasks pending ∨ outcome unachievable ∨ risk unacceptable) =
  [halt ∀ work; ∀ agent saves partial outputs ∧ last state,
   💾 an abort log in Τ_log form: {reason, tasks done ∧ outputs, tasks abandoned ∧ partial state, conditions before retry},
   task list: done → completed; incomplete → described as abandoned,
   shutdown_request to ∀ agent,
   ⏸ present the abort log ∧ one recommendation: retry with new constraints ∨ descope ∨ abandon; ? /goal active → state the reason in chat]
```
