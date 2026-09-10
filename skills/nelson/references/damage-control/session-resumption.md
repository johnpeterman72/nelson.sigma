# Δ₂ Session Resumption: Picking Up Mid-Mission

Session interrupted (context limit, crash, timeout); work continues.

1. **Auto (preferred):** `ND recover --missions-dir .nelson/missions`. Active mission → structured briefing (fleet status, handoff packets, pending tasks, recommended actions) → resume from it.
2. **Manual:** ? SESSION_ID known → `.nelson/.active-{SESSION_ID}` → `{mission-dir}`. : list `.nelson/missions/` → ⏸ user picks.
3. **State**, JSON first (more reliable than quarterdeck prose):
    - `{mission-dir}/fleet-status.json` → progress, hull, budget, blockers
    - `{mission-dir}/turnover-briefs/` `.json`, latest per ship → completed subtasks, partial outputs, blockers, next steps
    - `{mission-dir}/mission-log.json` → event history (completions, relief chains, Φ violations, ADM decisions)
    - no JSON → `{mission-dir}/quarterdeck-report.md`
    - missing too (crash mid-rotation) → `{mission-dir}/quarterdeck-report-N.md`, highest N; same for `captains-log.md` / `captains-log-N.md`
    - "Fleet status may be stale" (`last_updated` > 10 min ∨ `mission-log.json` events newer than `last_event_id`) → verify in-progress tasks against packets + files; ¬ trust cached counters
4. List ∀ task: `pending`, `in_progress`, `completed`.
5. ∀ `in_progress` → verify partial outputs against the deliverable; packet → `state.partial_outputs` + `state.next_steps`.
6. Discard outputs not confirmed correct.
7. Re-issue sailing orders: original outcome, scope less completed work.
8. Re-form the squadron at minimum size.
9. Resume quarterdeck rhythm at the next checkpoint.

**Safe compaction:** ∀ phase boundary (after Ω₁ Ω₂ Ω₃ Ω₄, ∀ Ω₆ checkpoint). Unsafe: inside Ω₅ between user permission and `permission_granted` + advance to UNDERWAY + spawn, one coupled turn.
