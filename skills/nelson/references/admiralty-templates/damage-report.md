# Τ Damage Report

Reports context-window usage to ADM. 💾 one JSON per ship at `{mission-dir}/damage-reports/{ship-name}.json` for the duration of the mission.

```json
{
  "ship_name": "",
  "agent_id": "",
  "timestamp": "",
  "token_count": 0,
  "token_limit": 0,
  "hull_integrity_pct": 0,
  "hull_integrity_status": "",
  "relief_requested": false,
  "context_summary": "",
  "report_path": "{mission-dir}/damage-reports/{ship-name}.json"
}
```

## Field Definitions

| Field | Type | Description |
|---|---|---|
| `ship_name` | string | ship name from the battle plan (e.g. `"HMS Argyll"`) |
| `agent_id` | string | agent identifier from the team config |
| `timestamp` | string | ISO 8601 (e.g. `"2026-02-20T14:30:00Z"`) |
| `token_count` | integer | tokens consumed so far in the current session |
| `token_limit` | integer | agent's maximum context window size |
| `hull_integrity_pct` | integer | remaining capacity: `floor((token_limit - token_count) / token_limit * 100)` |
| `hull_integrity_status` | string | `"Green"` ∨ `"Amber"` ∨ `"Red"` ∨ `"Critical"`, per thresholds below |
| `relief_requested` | boolean | `true` when status is `"Red"` ∨ `"Critical"`, else `false` |
| `context_summary` | string | one line on current work (e.g. `"Implementing API endpoint for user search"`) |
| `report_path` | string | where this report is stored; expand `{mission-dir}` to the concrete path when writing |

## Hull Integrity Thresholds

| Η | Remaining capacity | Meaning |
|---|---|---|
| Η🟢 Green | 75–100 % | operating normally |
| Η🟡 Amber | 60–74 % | monitor closely |
| Η🔴 Red | 40–59 % | relief on station recommended |
| Η⚫ Critical | < 40 % | relief on station required |

## Notes

- Η = remaining capacity, ¬ usage: 75 % hull = 25 % of the window used.
- Η🔴 ∨ Η⚫ → `relief_requested: true`; ADM uses the flag to prioritise relief on station.
- ∀ ship updates its report ∀ quarterdeck checkpoint ∨ when Η crosses a threshold boundary.
- ADM reads ∀ report in `{mission-dir}/damage-reports/` → squadron readiness board.

## Read-Only Agent Variant

Agents spawned with `subagent_type="Explore"` (NO, COX, Recce Marines) 🔒 cannot write files, damage reports included.

- They report Η to their CPT via `SendMessage` → CPT writes the JSON on their behalf, same template and fields.
- Recce Marine reporting directly to a CPT → CPT folds the marine's Η into its own report under `context_summary`.
