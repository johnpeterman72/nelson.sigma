# Δ Damage Control

## Δ.stuck (agent unresponsive/looping)

COORD: identify → record partials → shutdown → spawn replacement(same role)
Brief replacement: [task, deps, partials, blockers]
Resume from last verified checkpoint (not scratch)
Update plan w/ new assignment
Crew variant: LEAD handles at unit level; same role fails 2x → escalate COORD

## Δ.interrupted (context limit/crash/timeout)

Read last checkpoint → list task states
Verify in_progress partials → discard unverified
Re-define(updated scope for remaining) → re-compose min team
Resume from next checkpoint

## Δ.rollback (completed task found faulty, others sound)

Identify faulty + downstream deps
Mark: faulty=in_progress, deps=pending
Revert changes (VCS for code; archive w/ "reverted" for non-code)
Re-assign → re-exec w/ failure mode as constraint
Verify → unblock + resume deps

## Δ.abort (mission cannot succeed, budget waste)

?triggers: budget exhausted | outcome unachievable | risk > threshold
Halt all → save partials + state
Produce close log: [reason, completed, abandoned, retry_conditions]
Shutdown all → present to human: retry|descope|abandon

## Δ.escalate (exceeds authority/needs clarification)

Agent: pause → report[summary, options, 1 recommendation] → COORD
COORD: resolve within authority? yes → decide + document | no → escalate human
Human directs → COORD communicates → agent resumes

Authority:
  Crew: within sub-task | escalate → LEAD if affects others
  LEAD: within task | escalate → COORD if affects other tasks/shared resources
  COORD: re-assign, replace, adjust timeline, elevate Σ, descope within original | escalate → human for scope/budget/abort
  Human: final authority on scope, budget, abort, irreversible/high-blast

## Δ.overrun (unit consuming disproportionate tokens/time)

LEAD: pause crew → review progress vs budget
Identify cause: scope_creep|blocked|role_mismatch
Fix: descope|unblock+reassign|correct role
Resume w/ revised budget
?unrecoverable → escalate COORD: extend|descope|split to 2nd unit
