# Δ₉ Circuit Breakers: Automated Budget Alarms

Threshold alarms backing the checkpoint rhythm. Advisory: ¬ abort ships, ¬ auto-execute damage control; ADM picks the remedy. A future `strict` mode may auto-relieve on hull breach ∨ auto-abort on catastrophic overrun.

Evaluated ∀ checkpoint by `ND checkpoint` against the fresh `fleet-status.json` + mission log, and on `TeammateIdle` via the `idle-ship` hook handler (per-ship idle timeouts). Trip → `circuit_breaker_tripped` event in `mission-log.json` (value, threshold, recommended procedure) + one advisory line on stdout (stderr for idle-ship).

## Thresholds and Defaults

| Threshold | Default | Evaluated at | Recommends |
|---|---|---|---|
| `hull_integrity_threshold` — any ship hull ≤ N% | 80 | checkpoint | `damage-control/hull-integrity.md` |
| `budget_alarm_ratio` / `budget_alarm_completion_ratio` — tokens spent ≥ R1 of limit AND tasks completed < R2 of total | 0.7 / 0.4 | checkpoint | ADM review, elevate to Σ₂ |
| `cost_per_task_multiplier` — latest burn/task ≥ N × rolling median (needs `cost_per_task_min_history` checkpoints of history) | 3.0 / 3 | checkpoint | `damage-control/crew-overrun.md` |
| `consecutive_failures` — `blocker_raised` events without an intervening `blocker_resolved` ≥ N | 2 | checkpoint | `damage-control/scuttle-and-reform.md` |
| `idle_timeout_minutes` — a single ship idle for ≥ N minutes with incomplete task | 10 | TeammateIdle hook | `damage-control/man-overboard.md` |
| `time_limit_grace_minutes` — mission duration ≥ `sailing_orders.budget.time_limit_minutes` + grace | 0 | checkpoint | ADM review, consider stand-down |

`enabled` (default `true`) = master switch.

## Configuration

`sailing-orders.json > circuit_breakers`, editable after `ND init`:

```json
{
  "version": 1,
  "outcome": "...",
  "budget": {"token_limit": 100000, "time_limit_minutes": 120},
  "circuit_breakers": {
    "hull_integrity_threshold": 75,
    "budget_alarm_ratio": 0.75,
    "idle_timeout_minutes": 5,
    "enabled": true
  }
}
```

Unknown keys ignored (typos cannot override defaults); missing keys → defaults in `nelson_circuit_breakers.py`. Disable per mission (e.g. smoke test): `enabled: false`.

## Output Format

`ND checkpoint`, one line per trip on stdout after the summary:

```
[nelson-data] Checkpoint 3 recorded
Fleet: 1/4 done | Budget: 80.0% | Hull: 4G 0A 0R 0C | Blockers: 0
[CIRCUIT BREAKER: budget_alarm] Budget alarm: 80% of tokens spent with only 25% of tasks complete. Elevate to Station 2 and review scope.
```

Same trip in `mission-log.json`:

```json
{
  "type": "circuit_breaker_tripped",
  "checkpoint": 3,
  "timestamp": "2026-04-11T12:34:56Z",
  "data": {
    "type": "budget_alarm",
    "value": {"spent_ratio": 0.8, "completion_ratio": 0.25},
    "threshold": {"spent_ratio": 0.7, "completion_ratio": 0.4},
    "action": "admiral-review",
    "message": "Budget alarm: 80% of tokens spent with only 25% of tasks complete. ..."
  }
}
```

Authoritative trail for `ND history` + the cross-mission memory store.

## Idle Timeout State

`TeammateIdle` payload has no idle duration; the breaker keeps `<mission-dir>/idle-tracker.json`:

1. First fire for ship X → `{X: "2026-04-11T12:00:00Z"}`, no advisory.
2. Later fires: elapsed ≥ `idle_timeout_minutes` → man-overboard advisory.
3. Task `completed` (paid-off path) → entry cleared.

Best-effort: unwritable → silent degrade.

## Fleet-Status Budget Extensions

∀ checkpoint, under `fleet-status.json > budget`:

- `burn_rate_per_task` — `tokens_spent / completed`, integer; `None` until a task completes.
- `projected_budget_at_completion` — `burn_rate_per_task × total`; `None` when `burn_rate_per_task` is `None` ∨ `total == 0`.

Advisory projections, ¬ guarantees.

## Limits

Backstop, ¬ substitute for: checkpoint rhythm (ADM owns the decision) · hull reports (breach seen only if a damage report was filed ∨ the board updated) · standing orders (breakers ¬ evaluate anti-patterns; that is ADM's Φ check). Same root cause repeatedly → candidate standing order; see `docs/`.

## Related Procedures

`hull-integrity.md` secondary alarm over the readiness board, catches misses · `crew-overrun.md` burn-rate spike, ¬ self-report · `man-overboard.md` stuck ships ADM has not noticed · `scuttle-and-reform.md` blocker count without manual tally.
