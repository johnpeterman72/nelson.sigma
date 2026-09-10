# Δ₈ Hull Integrity: Context Window Management

∀ ship tracks hull integrity % = remaining context window.

## Thresholds

| Η | Status | Remaining | Action |
|---|---|---|---|
| Η🟢 | Green | 75–100 % | continue |
| Η🟡 | Amber | 60–74 % | ADM notes the ship on the readiness board; CPT finishes the current task, ¬ new work that extends the session |
| Η🔴 | Red | 40–59 % | CPT files a damage report, `relief_requested: true`; ADM plans relief: spawn replacement, brief with done + remaining work, transfer the task; CPT writes a clean handoff summary before context runs out |
| Η⚫ | Critical | below 40 % | ADM relieves now; no replacement → descope ∨ redistribute to Η🟢/Η🟡 ships; CPT stops non-essential work, writes a final status report |

## Squadron Readiness Board

Source: `{mission-dir}/damage-reports/`.

1. ∀ checkpoint: latest damage report from ∀ active ship.
2. ∀ ship: status, %, relief requested?
3. Flag Η🔴 ∨ Η⚫.
4. Record in the quarterdeck report under "Hull integrity (squadron readiness board):".

## Quarterdeck Rhythm

∀ checkpoint: ∀ CPT files a damage report per `references/admiralty-templates/damage-report.md` → ADM updates the board → threshold crossed since last checkpoint → that threshold's action → hull recorded in the quarterdeck report. Between checkpoints: CPT files at once on ∀ crossing; ¬ wait.

## Relief on Station

Trigger: Η🔴 Red.

1. ADM spawns a replacement, same role + class.
2. Outgoing CPT writes a handoff summary: task definition, completed sub-tasks, partial outputs, known blockers, file ownership.
3. ADM briefs the replacement with it + the original crew briefing.
4. Replacement resumes from the last verified checkpoint, ¬ scratch.
5. Update the battle plan.
6. Shutdown request to the outgoing ship.

Several at Η🔴 → nearest Η⚫ first.

## Flagship Self-Monitoring

ADM tracks own tokens, computes hull at ∀ checkpoint. Amber, Red, Critical actions: `references/damage-control/relief-on-station.md`. ¬ wait for Critical: coordination state lost there is unrecoverable.

## Related Procedures

- `session-resumption.md`: Critical ∧ session must end; resumes from the last quarterdeck report.
- `crew-overrun.md`: overrun accelerates hull loss; correction weighs current hull (Amber has less margin than Green).
- `man-overboard.md`: replacement costs context; weigh hull in replace vs descope.
- `scuttle-and-reform.md`: flagship Red ∧ several ships Red ∨ Critical → scuttle and reform with fresh context over piecemeal relief.

## Automated Circuit Breaker

`hull_integrity_breach` advisory ∀ checkpoint when any `hull_integrity_pct` ≤ `circuit_breakers.hull_integrity_threshold` (default 80 %): `damage-control/circuit-breakers.md`.

## Advanced: TeammateIdle Hook

`hooks/hooks.json` `TeammateIdle`: CPT idle → fleet status → task complete ∧ no pending dependents → advises paid-off.
