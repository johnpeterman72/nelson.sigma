# Damage Control

Four procedures. Escalation runs crew → CPT → ADM → Admiralty (the human): the agent pauses, reports issue, options, and one recommendation; the next level decides within its authority or passes it up with a summary. Authority: crew own their sub-task · CPT owns the task · ADM may re-assign, replace, re-time, raise Σ, and descope within the sailing orders · Admiralty decides scope changes, budget extensions, abort, and confirms ∀ irreversible action.

## Δ₁ Man Overboard

Agent unresponsive, looping, producing nothing useful, unreachable, or the message bus is down.

1. Record the agent, task, last known progress, partial outputs on disk.
2. `SendMessage` `shutdown_request`; three unacknowledged attempts → abandon the request, note it, continue.
3. Spawn a replacement of the same role with a Relief Brief (`references/templates.md`): task definition, dependencies, partial outputs, known blockers, file ownership. It resumes from the last verified point, ¬ from scratch.
4. Update the battle plan and task list.

**Comms failure** (several ships unreachable, `SendMessage` errors, task list stale): record unreachable ships and tasks → recover their disk output → redistribute to reachable ships or fresh dispatches; team surface down → continue in Μ₂ with replacements briefed from recovered context → cannot continue → Δ₄. ⛔ ADM ✗ implement the lost work (Φ₂). Prevention: ≥4 ships → intermediate outputs to disk; long tasks → "write a checkpoint to disk" in the brief; ∀ checkpoint → `ListAgents`.

**Crew variant:** the CPT runs the same steps at ship level. The same role fails twice → escalate to ADM with a summary and recommendation.

**Manual cleanup, last resort:** graceful shutdown failed three times ∧ the team cannot be deleted → remove `~/.claude/teams/{team-name}` and `~/.claude/tasks/{team-name}` (paths may vary by build), verify with `ListAgents`, log affected agents in the captain's log, re-issue their tasks from the last verified point.

## Δ₂ Session Resumption

New session, interruption (context limit, crash, timeout), or compaction mid-mission.

1. Locate the mission: the newest directory under `.nelson/missions/` without `captains-log.md`; several candidates → ⏸ user picks. Set `{mission-dir}`.
2. 📖 `quarterdeck-report.md` (fallback: the highest `quarterdeck-report-N.md`), then `battle-plan.md`, then `estimate.md` if present.
3. `ListAgents` → ships still on station; reconcile with the task list where available.
4. ∀ `in_progress` task: verify partial outputs on disk against the deliverable; discard anything unconfirmed.
5. Re-issue sailing orders: original outcome, scope less completed work. Re-form the squadron at minimum size, briefing from recovered context.
6. ? a `/goal` was recorded in `battle-plan.md` ∧ none is active (bare `/goal`) → re-issue it.
7. Resume the quarterdeck rhythm at the next checkpoint.

Safe compaction points: after Ω₁, Ω₂, Ω₃, Ω₅ spawning, and ∀ Ω₆ checkpoint. Unsafe: between approval at Ω₅ and the spawn turn. ∀ compaction summary MUST name `{mission-dir}` and the coordination role.

**New mission:** `mkdir -p {mission-dir}`; previous missions persist untouched. Rotated reports (`quarterdeck-report-N.md`) are history; ✗ delete.

## Δ₃ Context Exhaustion

**ADM:** context is managed by the harness; the on-disk quarterdeck report is the recovery point. Approaching compaction → checkpoint now, 💾 the report, then `/compact` at a safe point (Δ₂). ✗ let compaction land between approval and spawn.

**Ship:** degraded output (repetition, missed instructions, shallow reasoning), a long-running task, or a ship that asks for relief → planned handover:

1. Signal the ship to pause and 💾 outputs.
2. Ship writes a Relief Brief to `{mission-dir}/` (`references/templates.md`).
3. Spawn the replacement; class fits the remaining work; the brief path is the first line of its prompt.
4. Reassign the task; `shutdown_request` to the relieved ship; update the battle plan.
5. Chained reliefs carry one line per predecessor. A third relief on one task → ADM re-scopes: the task is too large or ill-defined for one ship.

**Crew overrun** (crew burning tokens or time out of proportion to the task): CPT pauses ∀ crew → reviews each against its sub-task → scope creep → descope · blocked → resolve or reassign · role mismatch → correct role or CPT does it → resume with a revised budget → cannot recover → ADM with a recommendation: extend, descope, or split to a second ship.

## Δ₄ Recovery and Abort

**Partial rollback** (completed task found faulty, others sound): identify the faulty task and its dependents → faulty back to `in_progress`, dependents to `pending` → revert code via version control, archive other artifacts as `reverted` → re-assign to the original owner or a replacement → re-execute from the original definition with the failure mode as a constraint → verified → resume dependents.

**Escalation triggers:** ambiguous requirement or acceptance criteria → CPT asks ADM · agents disagree → ADM decides, unsure → ⏸ Admiralty · scope creep → ADM re-scopes ∨ ⏸ approval · unexpected out-of-scope dependency → pause dependents, ⏸ Admiralty · Σ₂+ risk found mid-task → raise Σ, apply its controls · budget near limit with critical work left → ⏸ extend, descope, or abort · planned human-action step → Awaiting Admiralty (Φ₆), ¬ a failure.

**Scuttle and re-form** (mission cannot succeed; budget exhausted with critical tasks pending; outcome unachievable; risk unacceptable):

1. Halt ∀ work; ∀ agent saves partial outputs and last state.
2. 💾 an abort log in captain's-log form: reason · tasks done and outputs · tasks abandoned and partial state · conditions before retry.
3. Task list: done → `completed`; incomplete → described as abandoned.
4. `shutdown_request` to ∀ agent.
5. ⏸ present the abort log and one recommendation: retry with new constraints, descope, or abandon. ? `/goal` active → state the abandonment reason in chat.
