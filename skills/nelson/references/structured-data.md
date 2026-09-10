# Structured Data Capture

ND (`nelson-data.py`) and NP (`nelson-phase.py`) reference. Run via Bash at each Ω to write JSON beside the prose artifacts. Scripts own validation, timestamps, file I/O. Only stdout is consumed — ✗ load script source into context.

Mode enum: `single-session | subagents | agent-team | workflow | hybrid-workflow`. Tables: ✓ required · ~ optional.

## Script Commands

### `init`

Ω₁. Generates ∨ accepts an 8-hex SESSION_ID → creates `.nelson/missions/{YYYY-MM-DD_HHMMSS}_{SESSION_ID}/` + `damage-reports/` + `turnover-briefs/` → writes `sailing-orders.json`, `mission-log.json`, `fleet-status.json` (Π SAILING_ORDERS) + marker `.nelson/.active-{SESSION_ID}` (read by recovery ∧ hooks) → prints the mission dir. SESSION_ID = segment after the last `_`.

| Flag | Req | Meaning |
|---|---|---|
| `--outcome` | ✓ | |
| `--metric` | ✓ | success metric |
| `--deadline` | ✓ | e.g. `this_session` |
| `--token-budget N` | ~ | |
| `--time-limit N` | ~ | minutes |
| `--constraints` | ~ | repeatable |
| `--out-of-scope` | ~ | repeatable |
| `--stop-criteria` | ~ | repeatable |
| `--handoff-artifacts` | ~ | repeatable |
| `--session-id <8-hex>` | ~ | exactly 8 lowercase hex, else rejected |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py init \
  --outcome "Refactor auth module to use JWT tokens" \
  --metric "All 47 auth tests pass, no new dependencies" \
  --deadline "this_session" \
  --token-budget 200000
```

### `goal-condition`

Ω₁ after `init`; long autonomous ∨ headless ∨ scheduled ∨ ultracode missions. Reads `sailing-orders.json` → condition from `outcome`, `success_metric`, `stop_criteria` → prints a paste-ready `/goal ...` line. 🔒 unless `--record`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--max-turns N` | ~ | append `or stop after N turns` |
| `--record` | ~ | persist as `goal_condition` ∧ log `goal_set`; resumed session re-establishes |
| `--json` | ~ | `{condition, command, char_count, within_limit, recorded}` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py goal-condition \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --max-turns 40 --record
```

- Worded against transcript-visible facts (metric confirmed, stop criteria met, captain's log written with path stated, stand-down recorded): the `/goal` evaluator sees only the transcript. A formal `scuttle-and-reform` abandonment is a legitimate stop.
- > 4,000 chars (`/goal` limit) → stderr warning ∧ `within_limit: false`. Doctrine: `references/goal-alignment.md`.

### `skip-estimate`

Ω₁ when the user declines The Estimate. Writes `estimate_skipped: true` ∧ `estimate_skip_reason` into `sailing-orders.json` → logs an `estimate_skipped` event (checkpoint 0) → lets `NP advance` pass ESTIMATE ⟶ BATTLE_PLAN without `estimate.md`. Requires `init` first.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--reason` | ✓ | one-line rationale, non-empty |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py skip-estimate \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --reason "trivial scope, single subsystem"
```

### `squadron`

Ω₄. Writes `squadron` in `battle-plan.json`; appends `squadron_formed`; writes initial `fleet-status.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--admiral` | ✓ | ship |
| `--admiral-model` | ✓ | |
| `--captain "name:class:model:task_id"` | ~ | repeatable |
| `--red-cell` | ~ | RCN ship |
| `--red-cell-model` | ~ | |
| `--mode` | ~ | default `subagents` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py squadron \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --admiral "HMS Victory" --admiral-model opus \
  --captain "HMS Argyll:frigate:sonnet:1" \
  --captain "HMS Kent:destroyer:sonnet:2" \
  --red-cell "HMS Astute" --red-cell-model haiku \
  --mode agent-team
```

### `task`

Ω₄, once per task after owners are assigned. Appends to `battle-plan.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--id N` | ✓ | |
| `--name` | ✓ | |
| `--owner` | ✓ | ship |
| `--deliverable` | ✓ | |
| `--deps` | ~ | comma-separated ids, `""` = none |
| `--station-tier` | ✓ | 0 ∨ 1 ∨ 2 ∨ 3 |
| `--files` | ~ | comma-separated globs |
| `--modification-targets` | ~ | comma-separated functions, env vars, config being extended |
| `--validation` | ~ | → `validation_required` |
| `--rollback-note` | ~ | flag → `rollback_note_required` |
| `--admiralty-action` | ~ | flag → `admiralty_action_required` |
| `--task-type` | ~ | free-form, trust calibration |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py task \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --id 1 --name "Auth module refactor" --owner "HMS Argyll" \
  --deliverable "Refactored auth module with JWT support" \
  --deps "" --station-tier 1 \
  --files "src/auth/**" \
  --modification-targets "auth_handler, JWT_SECRET"
```

### `plan-approved`

Ω₄ after all tasks, before `squadron`. Computes `parallel_tracks` ∧ `critical_path_length`; appends `battle_plan_approved`; updates `fleet-status.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py plan-approved \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4
```

### `event`

Ω₆ between checkpoints. Appends to `mission-log.json`; extra `--{field} {value}` pairs → `data.field` (hyphen → underscore), validated per type.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--type` | ✓ | Event Types table |
| `--checkpoint N` | ~ | omitted → last checkpoint |
| `--{field} {value}` | ~ | type-specific data |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py event \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --type task_completed \
  --checkpoint 2 \
  --task-id 1 --task-name "Auth module refactor" --owner "HMS Argyll" \
  --station-tier 1 --verification passed
```

Workflow telemetry: same mechanism. v1 allows manual entry from the `/workflows` view; ✗ require fields not programmatically exposed.

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py event \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --type workflow_probe_completed \
  --workflow-name "auth-audit" --phase probe --status completed \
  --agents-total 8 --agents-completed 8 \
  --tokens-used 42000 --elapsed-minutes 11 \
  --summary "Probe found two real issues and three false positives" \
  --next-gate "User approval before full run"
```

### `handoff`

Ω₆ on relief (context exhaustion ∨ session resumption ∨ mid-mission resize). Writes a validated packet `{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.json`; appends `relief_on_station` with the packet path. Supersedes `event --type relief_on_station`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--ship-name` | ✓ | outgoing ship |
| `--task-id` | ✓ | |
| `--task-name` | ✓ | |
| `--handoff-type` | ✓ | `relief_on_station` ∨ `session_resumption` ∨ `mid_mission_resize` |
| `--completed-subtask` | ~ | repeatable |
| `--partial-output` | ~ | repeatable, `subtask:progress:notes` |
| `--known-blocker` | ~ | repeatable |
| `--file-ownership` | ~ | repeatable; ✓ when task `station_tier > 0` (from `battle-plan.json`) |
| `--next-step` | ✓ | repeatable, ≥ 1 |
| `--open-decision` | ~ | repeatable |
| `--hull-at-handoff` | ✓ | % |
| `--tokens-consumed` | ✓ | |
| `--key-finding` | ~ | repeatable |
| `--relief-entry` | ~ | repeatable, `ship:reason:time`, ≤ 3 |
| `--incoming-ship` | ~ | replacement |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py handoff \
  --mission-dir .nelson/missions/2026-04-08_140000_a1b2c3d4 \
  --ship-name "HMS Argyll" \
  --task-id 3 --task-name "API endpoint implementation" \
  --handoff-type relief_on_station \
  --completed-subtask "Schema design" \
  --completed-subtask "GET endpoint" \
  --partial-output "POST endpoint:60%:Validation logic pending" \
  --file-ownership "src/api/endpoints.py" \
  --file-ownership "src/api/validators.py" \
  --next-step "Complete POST validation" \
  --next-step "Write integration tests" \
  --hull-at-handoff 38 --tokens-consumed 145000 \
  --key-finding "API rate limiting needs custom middleware" \
  --key-finding "Existing auth works with new endpoints" \
  --relief-entry "HMS Argyll:context_exhaustion:2026-04-08T14:30:00Z" \
  --incoming-ship "HMS Kent"
```

### `checkpoint`

Ω₆ ∀ checkpoint, beside the prose report. Appends `checkpoint` (auto-numbered); overwrites `fleet-status.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--pending` `--in-progress` `--completed` | ✓ | task counts |
| `--blocked` | ~ | default 0 |
| `--tokens-spent` `--tokens-remaining` | ✓ | |
| `--hull-green` `--hull-amber` `--hull-red` `--hull-critical` | ✓ | ships per Η |
| `--decision` | ✓ | `continue` ∨ `rescope` ∨ `stop` |
| `--rationale` | ✓ | |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py checkpoint \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --pending 2 --in-progress 2 --completed 1 --blocked 0 \
  --tokens-spent 45000 --tokens-remaining 155000 \
  --hull-green 3 --hull-amber 1 --hull-red 0 --hull-critical 0 \
  --decision continue \
  --rationale "On track. HMS Kent approaching amber but no relief needed yet."
```

### `admiralty-decision`

ADM approves ∨ modifies ∨ rejects an `admiralty_action_required` item. Appends `admiralty_action_completed` with `task_id`, `decision_type`, `recorded_by`, `session_marker_present` (+ `task_type` from `battle-plan.json`, `ship_class` from `fleet-status.json`, `notes` when given). Feeds the override-learned trust calibration aggregated at `stand-down`; `session_marker_present` separates admiral-confirmed from self-reported decisions.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--task-id` | ✓ | must exist in `battle-plan.json` |
| `--decision-type` | ✓ | `approved` ∨ `modified` ∨ `rejected` |
| `--recorded-by` | ✓ | `Admiral` ∨ captain's ship; empty rejected |
| `--notes` | ~ | |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py admiralty-decision \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --task-id 3 \
  --decision-type modified \
  --recorded-by Admiral \
  --notes "Narrowed scope to the auth module only"
```

### `stand-down`

Ω₈ beside the captain's log. Auto-computes duration, budget, ships, reliefs, violations, blockers from `mission-log.json` ∧ `battle-plan.json` → `stand-down.json`; appends `mission_complete`; final `fleet-status.json`; updates `.nelson/memory/patterns.json` ∧ `standing-order-stats.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--outcome-achieved` | ~ | flag |
| `--actual-outcome` | ~ | |
| `--metric-result` | ~ | |
| `--adopt` `--avoid` | ~ | patterns, repeatable; omitted → `[]` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py stand-down \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --outcome-achieved \
  --actual-outcome "Auth module refactored with JWT support, all tests passing" \
  --metric-result "47/47 auth tests pass, 0 new dependencies" \
  --adopt "Station tier 1 for schema migrations worked well" \
  --adopt "Dedicated destroyer for DB-heavy tasks" \
  --avoid "Assigning DB work to a frigate"
```

### `form`

Ω₄, recommended over `task` + `squadron` + `plan-approved`. Plan JSON → tasks registered, squadron recorded, DAG metrics, conflict scan; workflow charter ∧ advisory fields preserved in `battle-plan.json`. Summary JSON → stdout, progress → stderr.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--plan` | ✓ | plan JSON path |
| `--mode` | ~ | default `subagents` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py form \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 \
  --plan battle-plan-input.json \
  --mode subagents
```

Plan JSON (`squadron` ∧ `tasks` required):

```json
{
  "squadron": {
    "admiral": { "ship_name": "HMS Victory", "model": "opus" },
    "captains": [
      { "ship_name": "HMS Argyll", "ship_class": "frigate", "model": "sonnet", "task_id": 1 }
    ],
    "red_cell": { "ship_name": "HMS Astute", "model": "haiku" }
  },
  "tasks": [
    {
      "id": 1,
      "name": "Auth module refactor",
      "owner": "HMS Argyll",
      "deliverable": "Refactored auth module with JWT support",
      "dependencies": [],
      "station_tier": 1,
      "file_ownership": ["src/auth/**"],
      "modification_targets": ["auth_handler", "JWT_SECRET"]
    }
  ]
}
```

- Μ₄|Μ₅ → add top-level `"mode"` ∧ an optional `workflow` object shaped as the `workflow` section of `battle-plan.json` (below); plan JSON without it stays valid.
- Top-level advisory fields preserved when present: `execution_primitive`, `workflow_suitability`, `workflow_phases`, `human_gates`, `verification_contract`, `cost_guardrail`, `fallback_mode`.

Output:

```json
{
  "status": "ok",
  "mission_dir": ".nelson/missions/2026-03-27_120000_a1b2c3d4",
  "tasks_registered": 1,
  "squadron": { "admiral": "HMS Victory", "captains": 1, "mode": "subagents", "has_red_cell": true },
  "dag_metrics": { "parallel_tracks": 1, "critical_path_length": 1 },
  "conflict_scan": { "clean": true, "exit_code": 0, "stdout": "..." }
}
```

### `headless`

`init` + `form` from JSON files; CI/CD. Output JSON: `mission_dir`, `sailing_orders`, `formation`.

| Flag | Req | Meaning |
|---|---|---|
| `--sailing-orders` | ✓ | JSON path |
| `--battle-plan` | ✓ | JSON path, `form` plan shape |
| `--mode` | ~ | default `subagents` |
| `--auto-approve` | ~ | skip the approval gate |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py headless \
  --sailing-orders sailing-orders.json \
  --battle-plan battle-plan.json \
  --mode subagents \
  --auto-approve
```

Sailing orders JSON (fields as `sailing-orders.json`):

```json
{
  "outcome": "Refactor auth module to use JWT tokens",
  "metric": "All 47 auth tests pass, no new dependencies",
  "deadline": "this_session",
  "budget": { "token_limit": 200000 },
  "constraints": ["Do not modify the public API surface"],
  "out_of_scope": ["Migration script for existing sessions"]
}
```

### `status` 🔒

Any time; resumption, hooks, context injection; auto-run by the SKILL.md `!` block. Reads `fleet-status.json` ∧ `mission-log.json`. No mission data → silent no-op.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ~ | omitted → silent no-op |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py status \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4
```

```
NELSON FLEET STATUS
Mission: 2026-04-08_201214_a1b2c3d4 (underway)
Progress: 3/5 tasks complete | 1 blocked
Ships: HMS Argyll (Green 82%) | HMS Kent (Amber 65%) | HMS Daring (completed)
Last checkpoint: 2 (12 min ago)
Budget: 45% consumed
```

### `recover` 🔒

Resumption. Reads `fleet-status.json`, `battle-plan.json`, `turnover-briefs/*.json` → briefing; writes nothing. Auto-discovery: `.nelson/.active-*` : newest mission dir without `stand-down.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ~ | target one mission |
| `--missions-dir` | ~ | root for auto-discovery |
| `--format` | ~ | `json` (default) ∨ `text` |

```bash
# Auto-discover active mission
python3 .claude/skills/nelson/scripts/nelson-data.py recover \
  --missions-dir .nelson/missions

# Target a specific mission
python3 .claude/skills/nelson/scripts/nelson-data.py recover \
  --mission-dir .nelson/missions/2026-04-08_140000_a1b2c3d4

# Human-readable output
python3 .claude/skills/nelson/scripts/nelson-data.py recover \
  --mission-dir .nelson/missions/2026-04-08_140000_a1b2c3d4 \
  --format text
```

```json
{
  "mission_dir": ".nelson/missions/2026-04-08_140000_a1b2c3d4",
  "mission_status": "underway",
  "fleet_status": { "..." },
  "handoff_packets": [ { "..." } ],
  "pending_tasks": [ { "task_id": 3, "task_name": "...", "owner": "...", "status": "..." } ],
  "recommended_actions": ["Resume task 3 from handoff packet (HMS Argyll)"]
}
```

### `brief` 🔒

Before Ω₁. Reads `fleet-intelligence.json`, `.nelson/memory/patterns.json`, `.nelson/memory/standing-order-stats.json` → compact brief for context injection.

| Flag | Req | Meaning |
|---|---|---|
| `--missions-dir` | ~ | |
| `--context` | ~ | surface precedents from similar missions |
| `--json` | ~ | |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py brief \
  --missions-dir .nelson/missions \
  --context "auth module refactor"
```

### `analytics` 🔒

Reads `fleet-intelligence.json` ∧ `.nelson/memory/standing-order-stats.json`.

| Flag | Req | Meaning |
|---|---|---|
| `--missions-dir` | ~ | |
| `--metric` | ✓ | `success-rate` (win rate, trend, outcome by fleet size) ∨ `standing-orders` (violation frequency, top offenders, failure correlation) ∨ `efficiency` (tokens and duration per task, budget utilization) ∨ `estimate-outcomes` ∨ `all` |
| `--json` | ~ | |
| `--last N` | ~ | 0 = all |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py analytics \
  --missions-dir .nelson/missions \
  --metric success-rate

python3 .claude/skills/nelson/scripts/nelson-data.py analytics \
  --missions-dir .nelson/missions \
  --metric all --json --last 10
```

### `detect-patterns`

After stand-down ∧ `index`; read/write memory. Clusters `avoid` texts → scores each cluster (Fisher's exact + log-odds vs outcomes) → drops resemblance to an existing order ∨ a dismissed candidate → keeps only negative correlation with success (anti-patterns, ¬ win patterns) → appends survivors to `{memory_dir}/candidate-standing-orders.json` for human review (zero candidates on first run → file not created). ✗ writes or modifies standing orders; promotion is a separate human step.

| Flag | Req | Meaning |
|---|---|---|
| `--missions-dir` | ~ | memory dir derived as `{missions_dir}/../memory` (as `brief`) |
| `--memory-dir` | ~ | override |
| `--standing-orders-dir` | ~ | novelty-filter scan; default skill `references/standing-orders/` |
| `--min-missions N` | ~ | default 10 |
| `--confidence-threshold F` | ~ | drop `1 - p_value` < F; default 0.7 |
| `--json` | ~ | `{status, detected, queue_size, memory_dir}`; CI gate |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py detect-patterns \
  --missions-dir .nelson/missions
```

### `promote-candidate`

Writes a new `.md` under the standing-orders dir → inserts a SKILL.md Standing Orders row → removes the candidate from the queue. Transactional: a failed step rolls back the earlier ones (no orphan `.md`, no half-edited SKILL.md).

| Flag | Req | Meaning |
|---|---|---|
| `--candidate-id` | ✓ | queue id |
| `--missions-dir` `--memory-dir` | ~ | as `detect-patterns` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py promote-candidate \
  --candidate-id cand-abc123 \
  --missions-dir .nelson/missions
```

- Exit 1 (stderr): id not in queue · same-title order exists (¬ overwrite hand-written) · SKILL.md missing ∨ `## Standing Orders` heading/table not found.
- Title re-slugified at the promotion boundary (¬ path traversal); free text sanitised before the SKILL.md cell.

### `dismiss-candidate`

Moves the candidate to `{memory_dir}/dismissed-candidates.json`; later `detect-patterns` runs ¬ re-surface the fingerprint.

| Flag | Req | Meaning |
|---|---|---|
| `--candidate-id` | ✓ | queue id |
| `--reason` | ✓ | kept in the archive |
| `--missions-dir` `--memory-dir` | ~ | as `detect-patterns` |

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py dismiss-candidate \
  --candidate-id cand-abc123 \
  --reason "duplicate of split-keel" \
  --missions-dir .nelson/missions
```

## Phase Engine

NP enforces Π transitions with exit criteria and validates tool use via PreToolUse hooks. Π is linear: SAILING_ORDERS ⟶ ESTIMATE ⟶ BATTLE_PLAN ⟶ FORMATION ⟶ PERMISSION ⟶ UNDERWAY ⟶ STAND_DOWN.

| Π | Exit criterion |
|---|---|
| SAILING_ORDERS | `sailing-orders.json` exists |
| ESTIMATE | `estimate.md` exists ∨ `sailing-orders.json` has `estimate_skipped: true` |
| BATTLE_PLAN | `battle-plan.json` has tasks, ∀ with `station_tier` |
| FORMATION | `battle-plan.json` has `squadron` |
| PERMISSION | `permission_granted` event in `mission-log.json` |
| UNDERWAY | ∀ tasks completed ∨ mission aborted |
| STAND_DOWN | terminal |

Blocked tools: `TeamCreate` ∀ Π ≠ UNDERWAY · `TaskCreate` ∀ Π ∉ {FORMATION, UNDERWAY} · `Agent` ∀ Π ∉ {ESTIMATE, UNDERWAY, STAND_DOWN}.

### `current`

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ~ | omitted → auto-discover from `.nelson/.active-*` |

```bash
python3 .claude/skills/nelson/scripts/nelson-phase.py current \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4
```

### `advance`

Validates the exit criterion → appends `phase_transition`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ~ | omitted → auto-discover |

```bash
python3 .claude/skills/nelson/scripts/nelson-phase.py advance \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4
```

### `validate-tool`

PreToolUse hook check. Exit 0 allowed, 1 blocked.

| Flag | Req | Meaning |
|---|---|---|
| `--tool` | ✓ | tool name |
| `--mission-dir` | ~ | omitted → auto-discover |

```bash
python3 .claude/skills/nelson/scripts/nelson-phase.py validate-tool \
  --tool Agent --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4
```

### `set`

Recovery escape hatch: skips exit validation; logs `phase_override`.

| Flag | Req | Meaning |
|---|---|---|
| `--mission-dir` | ✓ | |
| `--phase` | ✓ | Π name |

```bash
python3 .claude/skills/nelson/scripts/nelson-phase.py set \
  --mission-dir .nelson/missions/2026-03-27_120000_a1b2c3d4 --phase UNDERWAY
```

## Write Timing

Prose companion in parentheses; otherwise conversation-only.

- Ω₁ `init` → `sailing-orders.json`, `mission-log.json`
- Ω₁ ~ `goal-condition --record` → `sailing-orders.json`, `mission-log.json` (`/goal` set in session)
- Ω₃ none — owners not yet assigned
- Ω₄ `form` (recommended) ∨ `task` + `plan-approved` + `squadron` → `battle-plan.json`, `mission-log.json`, `fleet-status.json`
- Ω₁–Ω₄ CI/CD `headless` → all of the above in one step
- Ω₅ none
- Ω₆ ∀ checkpoint `checkpoint` → `mission-log.json`, `fleet-status.json` (`quarterdeck-report.md`)
- Ω₆ between checkpoints `event` → `mission-log.json`
- Ω₆ workflow stage boundary `event --type workflow_*` → `mission-log.json` (telemetry from `/workflows` view)
- Ω₆ relief `handoff` → `mission-log.json`, `turnover-briefs/{ship}.json` (~ `turnover-briefs/{ship}.md`)
- Ω₇ `event --type task_completed` → `mission-log.json`
- Ω₈ `stand-down` → `mission-log.json`, `fleet-status.json`, `stand-down.json`, `.nelson/memory/patterns.json`, `.nelson/memory/standing-order-stats.json` (`captains-log.md`)
- Post-mission `index` → `fleet-intelligence.json`, `.nelson/memory/patterns.json`, `.nelson/memory/standing-order-stats.json`
- Pre-mission `brief` 🔒 · any time `analytics` 🔒

## Event Types

| Event Type | Trigger | Key Data Fields |
|---|---|---|
| `squadron_formed` | Ω₄ | captain_count, has_red_cell, execution_mode, standing_order_check |
| `battle_plan_approved` | Ω₄ | task_count, parallel_tracks, critical_path_length, standing_order_check |
| `task_started` | CPT begins | task_id, task_name, owner |
| `task_completed` | verified complete | task_id, task_name, owner, station_tier, verification |
| `checkpoint` | ∀ checkpoint | progress, budget, hull_summary, blockers, admiral_decision |
| `blocker_raised` | blocker found | description, owner, blocking_task_id, blocked_task_ids |
| `blocker_resolved` | blocker cleared | description, resolution |
| `hull_threshold_crossed` | Η boundary crossed | ship_name, previous_status, new_status, hull_integrity_pct |
| `relief_on_station` | ship relieved | outgoing_ship, incoming_ship, reason, time_on_station_minutes |
| `standing_order_violation` | Φ triggered | order, description, corrective_action, severity |
| `commendation` | signal flag ∨ MID | ship_name, type, citation |
| `admiralty_action_required` | needs human input | task_id, action, timing |
| `admiralty_action_completed` | via `admiralty-decision` | task_id, decision_type, recorded_by, session_marker_present (+ optional task_type, ship_class, notes) |
| `battle_plan_amended` | ADM rescopes | changes, rationale |
| `phase_transition` | NP `advance` | from_phase, to_phase |
| `phase_override` | NP `set` | from_phase, to_phase |
| `permission_granted` | user approves formation | (empty data) |
| `mission_complete` | Ω₈ | outcome_achieved, tasks_completed, total_tokens_consumed, duration_minutes |
| `workflow_charter_created` | charter approved | workflow_name, phase, summary, next_gate |
| `workflow_probe_completed` | Sounding-the-Channel probe done | workflow_name, phase, status, agents_total, agents_completed, tokens_used, elapsed_minutes, summary, next_gate |
| `workflow_run_started` | stage launched | workflow_name, phase, status, agents_total, summary |
| `workflow_run_completed` | stage completed | workflow_name, phase, status, agents_total, agents_completed, tokens_used, elapsed_minutes, summary, next_gate |
| `workflow_run_stopped` | stage halted | workflow_name, phase, status, agents_total, agents_completed, tokens_used, elapsed_minutes, summary, next_gate |
| `goal_set` | `goal-condition --record` | goal_condition |
| `goal_cleared` | goal cleared (mission abandoned) | reason |

## JSON Schemas

∀ artifacts in `{mission-dir}/`.

### sailing-orders.json (Write-Once)

```json
{
  "version": 1,
  "outcome": "Refactor auth module to use JWT tokens",
  "success_metric": "All 47 auth tests pass, no new dependencies",
  "deadline": "this_session",
  "budget": {
    "token_limit": 200000,
    "time_limit_minutes": null
  },
  "constraints": ["Do not modify the public API surface"],
  "out_of_scope": ["Migration script for existing sessions"],
  "stop_criteria": ["All tests pass", "No regressions in integration suite"],
  "handoff_artifacts": ["Updated auth module", "Test results"],
  "goal_condition": "The Nelson mission is complete: ... (optional; present only when goal-condition --record was run)",
  "created_at": "2026-03-27T12:00:00Z"
}
```

- `goal_condition` optional, additive: written only by `goal-condition --record`, preserved through later writes; a resumed session reads it to re-establish the goal.

### battle-plan.json (Write-Once, Amendable)

```json
{
  "version": 1,
  "squadron": {
    "mode": "subagents",
    "admiral": { "ship_name": "HMS Victory", "model": "opus" },
    "captains": [
      {
        "ship_name": "HMS Argyll",
        "ship_class": "frigate",
        "model": "sonnet",
        "task_id": 1,
        "crew": [
          { "role": "PWO", "sub_task": "Core endpoint development" }
        ],
        "marine_capacity": 2,
        "estimated_token_budget": 50000
      }
    ],
    "red_cell": { "ship_name": "HMS Astute", "model": "haiku" }
  },
  "tasks": [
    {
      "id": 1,
      "name": "Auth module refactor",
      "owner": "HMS Argyll",
      "deliverable": "Refactored auth module with JWT support",
      "dependencies": [],
      "dependents": [4],
      "station_tier": 1,
      "file_ownership": ["src/auth/**"],
      "modification_targets": ["auth_handler", "JWT_SECRET"],
      "validation_required": "Unit tests pass, no API surface change",
      "rollback_note_required": true,
      "admiralty_action_required": false
    }
  ],
  "admiralty_actions": [
    {
      "task_id": 3,
      "action": "Approve database schema before migration begins",
      "timing": "before_task_starts",
      "unblocks": "Task 3: Database migration"
    }
  ],
  "workflow": {
    "suitability": "large fan-out across independent files",
    "phases": [
      {
        "name": "probe",
        "purpose": "Run a small representative slice",
        "requires_human_gate_after": true
      }
    ],
    "verification_contract": ["Findings require independent reviewer confirmation"],
    "cost_guardrail": "Run a small slice before full repo scope",
    "fallback_mode": "agent-team"
  },
  "created_at": "2026-03-27T12:05:00Z",
  "amended_at": null
}
```

### mission-log.json (Append-Only)

∀ event: `type`, `checkpoint`, `timestamp`, type-specific `data`.

```json
{
  "version": 1,
  "events": [
    {
      "type": "checkpoint",
      "checkpoint": 1,
      "timestamp": "2026-03-27T12:20:00Z",
      "data": {
        "progress": { "pending": 2, "in_progress": 2, "completed": 1, "blocked": 0 },
        "budget": { "tokens_spent": 45000, "tokens_remaining": 155000, "pct_consumed": 22.5 },
        "hull_summary": { "green": 3, "amber": 1, "red": 0, "critical": 0 },
        "blockers": [],
        "standing_order_violations": [],
        "admiral_decision": "continue",
        "admiral_rationale": "On track."
      }
    },
    {
      "type": "task_completed",
      "checkpoint": 2,
      "timestamp": "2026-03-27T12:38:00Z",
      "data": {
        "task_id": 1,
        "task_name": "Auth module refactor",
        "owner": "HMS Argyll",
        "station_tier": 1,
        "verification": "passed"
      }
    }
  ]
}
```

### fleet-status.json (Overwritten Per Checkpoint)

Snapshot for real-time consumers (hooks, dashboards).

```json
{
  "version": 1,
  "mission": {
    "outcome": "Refactor auth module to use JWT tokens",
    "status": "underway",
    "phase": "UNDERWAY",
    "started_at": "2026-03-27T12:00:00Z",
    "checkpoint_number": 2
  },
  "progress": { "pending": 1, "in_progress": 2, "completed": 2, "blocked": 0, "total": 5 },
  "budget": {
    "tokens_spent": 80000,
    "tokens_remaining": 120000,
    "pct_consumed": 40.0,
    "burn_rate_per_checkpoint": 15000
  },
  "squadron": [
    {
      "ship_name": "HMS Argyll",
      "ship_class": "frigate",
      "role": "captain",
      "hull_integrity_pct": 72,
      "hull_integrity_status": "Green",
      "task_id": 3,
      "task_name": "API endpoint tests",
      "task_status": "in_progress"
    }
  ],
  "blockers": [],
  "recent_events": ["Task 1 completed (HMS Argyll)", "HMS Kent hull crossed to Amber (68%)"],
  "last_updated": "2026-03-27T12:35:00Z"
}
```

- Freshness: `last_updated` (ISO 8601) bumped at ∀ checkpoint ∧ ∀ state-changing event (`task_started`, `task_completed`, `blocker_raised`, `blocker_resolved`, `hull_threshold_crossed`, `relief_on_station`); `last_event_id` = index of the latest mission-log event merged in; recovery uses it to find unmerged events.
- Non-state-changing events (commendations, Φ violations, decisions) → `mission-log.json` only.

### handoff-packet.json (Write-Once Per Relief)

`{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.json`, by `handoff`.

```json
{
  "version": 1,
  "ship_name": "HMS Argyll",
  "task_id": 3,
  "task_name": "API endpoint implementation",
  "handoff_type": "relief_on_station",
  "state": {
    "completed_subtasks": ["Schema design", "GET endpoint"],
    "partial_outputs": [
      {"subtask": "POST endpoint", "progress": "60%", "notes": "Validation logic pending"}
    ],
    "known_blockers": [],
    "file_ownership": ["src/api/endpoints.py", "src/api/validators.py"],
    "next_steps": ["Complete POST validation", "Write integration tests"],
    "open_decisions": []
  },
  "context": {
    "hull_at_handoff": 38,
    "tokens_consumed": 145000,
    "checkpoint_number": 4,
    "key_findings": ["API rate limiting needs custom middleware", "Existing auth works with new endpoints"]
  },
  "relief_chain": [
    {"ship": "HMS Argyll", "reason": "context_exhaustion", "handoff_time": "2026-04-08T14:30:00Z"}
  ],
  "created_at": "2026-04-08T14:30:00Z"
}
```

### stand-down.json (Write-Once)

Auto-computed from `mission-log.json` ∧ `battle-plan.json` by `stand-down`.

```json
{
  "version": 1,
  "outcome_achieved": true,
  "planned_outcome": "Refactor auth module to use JWT tokens",
  "actual_outcome": "Auth module refactored with JWT support, all tests passing",
  "success_metric_result": "47/47 auth tests pass, 0 new dependencies",
  "duration_minutes": 70,
  "budget": { "tokens_consumed": 120000, "tokens_budgeted": 200000, "pct_consumed": 60.0 },
  "fleet": { "ships_used": 4, "reliefs": 1, "max_concurrent_ships": 4 },
  "tasks": { "completed": 5, "total": 5, "by_station_tier": { "0": 1, "1": 3, "2": 1, "3": 0 } },
  "quality": {
    "standing_order_violations": 1,
    "blockers_raised": 1,
    "blockers_resolved": 1,
    "avg_blocker_duration_minutes": 14
  },
  "open_risks": [{ "risk": "JWT rotation not load-tested", "owner": "follow-up", "mitigation": "Add load test next sprint" }],
  "follow_ups": [{ "item": "Add JWT load testing", "owner": "team", "due": "next sprint" }],
  "mentioned_in_despatches": [{ "ship_name": "HMS Argyll", "contribution": "Fast, clean auth refactor" }],
  "reusable_patterns": {
    "adopt": ["Station tier 1 for schema migrations worked well"],
    "avoid": ["Assigning DB work to a frigate -- needed a destroyer"]
  },
  "created_at": "2026-03-27T13:10:00Z"
}
```

## Memory Store

`.nelson/memory/`, created by `stand-down` ∧ `index`.

### patterns.json (Append-Only)

Pattern library from ∀ completed mission; updated at stand-down.

```json
{
  "version": 1,
  "updated_at": "2026-04-08T14:30:00Z",
  "pattern_count": 5,
  "patterns": [
    {
      "mission_id": "2026-04-08_120000",
      "completed_at": "2026-04-08T14:30:00Z",
      "outcome_achieved": true,
      "planned_outcome": "Refactor auth module",
      "adopt": ["Station tier 1 for schema migrations worked well"],
      "avoid": ["Assigning DB work to a frigate"],
      "standing_order_violations": [
        {
          "order": "split-keel",
          "description": "File ownership overlap",
          "severity": "medium",
          "corrective_action": "Reassigned file ownership"
        }
      ],
      "damage_control_events": 1,
      "quality": {
        "violations": 1,
        "blockers_raised": 2,
        "blockers_resolved": 2,
        "task_completion_rate": 1.0
      }
    }
  ]
}
```

### standing-order-stats.json (Overwritten)

Aggregate violation statistics; updated at stand-down ∧ index.

```json
{
  "version": 1,
  "updated_at": "2026-04-08T14:30:00Z",
  "total_missions": 5,
  "total_violations": 3,
  "violations_per_mission": 0.6,
  "by_order": {
    "split-keel": { "count": 2, "missions": ["2026-04-08_120000", "2026-04-07_100000"] },
    "skeleton-crew": { "count": 1, "missions": ["2026-04-06_090000"] }
  },
  "correlation": {
    "missions_with_violations": 2,
    "failures_with_violations": 1,
    "successes_with_violations": 1
  }
}
```

## Error Handling

Messages to stderr.

- Missing `--mission-dir` → exit 1.
- Invalid event type → prints valid types, exit 1.
- Missing required field for an event type → prints required fields, exit 1.
- Corrupt JSON on disk → backed up, fresh file created.
- Missing directories → created.

## Script Output

∀ subcommand prints a brief stdout confirmation; this ~20-token line replaces a ~200-token JSON Write, the full JSON is already on disk.

```
[nelson-data] Checkpoint 2 recorded
Fleet: 3/5 done | Budget: 62% | Hull: 3G 1A 0R | Blockers: 0
```

## Schema Coupling

- `_build_mission_record` ∧ `_extract_fleet_details` (`nelson-data.py`) depend on the schemas above: renaming fields in `stand-down.json`, `battle-plan.json`, `sailing-orders.json`, `mission-log.json` → update both.
- `_compute_analytics` depends on the field names produced by `_build_mission_record`.
- `_extract_patterns_from_mission`, `_update_patterns_store`, `_update_standing_order_stats`, `_build_intelligence_brief` depend on the mission schemas ∧ the memory schemas (`patterns.json`, `standing-order-stats.json`).
