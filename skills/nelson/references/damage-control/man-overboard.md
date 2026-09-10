# Δ₁ Man Overboard: Stuck Agent Replacement

Agent unresponsive ∨ looping ∨ no useful output. Several agents unreachable ∨ bus down → `comms-failure.md`.

1. ADM identifies the stuck agent + task.
2. Records last progress + partial outputs.
3. Shutdown request.
4. Spawns a replacement, same role.
5. Briefs it: task definition, dependencies, partial outputs, known blockers.
6. It resumes from the last verified checkpoint, ¬ scratch.
7. Updates the battle plan.

## Crew Variant

Stuck crew member → CPT runs the same steps at ship level (CPT/ADM, crew member/agent, sub-task/task, ship manifest/battle plan). Same role fails twice → escalate to ADM with summary + recommendation.

## Manual Cleanup Fallback

Last resort after graceful shutdown: 3 attempts failed ∧ `TeamDelete` blocked by unresponsive agents.

1. ADM confirms both.
2. Removes the team:
    - `rm -rf ~/.claude/teams/{team-name}` — removes the team registration
    - `rm -rf ~/.claude/tasks/{team-name}` — removes associated task data
3. Verifies it is gone from active team listings.
4. Logs affected agents + the cleanup in the captain's log.
5. Spawns a fresh team; re-issues affected tasks from the last verified checkpoint.
