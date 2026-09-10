---
name: nelson
description: Orchestrates multi-agent task execution using a Royal Navy squadron metaphor — from mission planning through parallel work coordination to stand-down. Use when work needs parallel agent orchestration, tight task coordination with quality gates, structured delegation with progress checkpoints, or a documented decision log.
argument-hint: "[mission description]"
paths: [".nelson/**"]
---

# Nelson♦Σ

```!
python3 "${CLAUDE_PLUGIN_ROOT}/skills/nelson/scripts/nelson-data.py" status
```

Execute Ω₁→Ω₈ for the user's mission. Write as Nelson's captains would: concise, elegant, confident — the register of an officer who respects the reader's time.

## Legend

Full reference: `references/sigma-legend.md`.

- Ω step · Π engine phase · Μ mode · Σ station · Η hull · Ε estimate question · Φ standing order · Δ damage control · Τ template
- Π: SAILING_ORDERS ⟶ ESTIMATE ⟶ BATTLE_PLAN ⟶ FORMATION ⟶ PERMISSION ⟶ UNDERWAY ⟶ STAND_DOWN
- Μ₁ `single-session` · Μ₂ `subagents` · Μ₃ `agent-team` · Μ₄ `workflow` · Μ₅ `hybrid-workflow`
- Σ₀🟢 Patrol · Σ₁🟡 Caution · Σ₂🟠 Action · Σ₃🔴 Trafalgar · Η🟢 ≥75 % · Η🟡 60–74 · Η🔴 40–59 · Η⚫ <40
- ADM admiral · CPT captain · RCN red-cell navigator · RM marine · crew XO PWO NO🔒 MEO WEO LOGO COX🔒 (🔒 read-only)
- → then · ∧ and · ∨ or · ¬ not · ∀ each · ∃ exists · ? if · : else · ~ optional · ! violation · ⛔ hard gate · ⏸ await human · 📖 MUST read · 💾 MUST write to disk · ✓ required · ✗ forbidden
- ND = `python3 .claude/skills/nelson/scripts/nelson-data.py` · NP = `python3 .claude/skills/nelson/scripts/nelson-phase.py` · NCS = `python3 .claude/skills/nelson/scripts/nelson_conflict_scan.py` (global install: `~/.claude/skills/nelson/scripts/`). Expand aliases before running.
- `{mission-dir}` = path printed by `ND init`

## 1. Issue Sailing Orders ⚓Ω₁

- ? brief ambiguous (outcome ∨ scope ∨ constraints) → ⏸ clarify first
- outcome, metric, deadline: one sentence each
- constraints: token budget, reliability floor, compliance, forbidden actions
- out of scope · stop criteria · handoff artifacts
- ¬ user structure → 📖 `references/admiralty-templates/sailing-orders.md`

```
Outcome: Refactor auth module to use JWT tokens
Metric: All 47 auth tests pass, no new dependencies
Deadline: This session
Constraints: Do not modify the public API surface
Out of scope: Migration script for existing sessions
```

**Mission directory:**

- new session → `ND init --outcome "..." --metric "..." --deadline "..."` → creates `.nelson/missions/{YYYY-MM-DD_HHMMSS}_{SESSION_ID}/` + `damage-reports/` + `turnover-briefs/` + `sailing-orders.json` + `mission-log.json` + `fleet-status.json` (Π SAILING_ORDERS) + marker `.nelson/.active-{SESSION_ID}`; prints path → `{mission-dir}`. SESSION_ID = 8 hex after the last `_` (`--session-id <8-hex>` to force).
- resumed session → `ND recover --missions-dir .nelson/missions` → ? active mission + handoff packets → resume from the briefing. : ? SESSION_ID known → read `.nelson/.active-{SESSION_ID}`. : list `.nelson/missions/` → ⏸ user selects. Then recover per `references/damage-control/session-resumption.md` (JSON first, quarterdeck prose fallback). ? no `/goal` active (check bare `/goal`) ∧ `sailing-orders.json` has `goal_condition` → re-issue per `references/goal-alignment.md`.
- ∀ artifacts (log, quarterdeck, damage, turnover) live inside `{mission-dir}`. Full arguments: `references/structured-data.md`.

**Advance** SAILING_ORDERS to ESTIMATE: `NP advance --mission-dir {mission-dir}`

**Hygiene:** 📖 `references/damage-control/session-hygiene.md` (skip when resuming).

**Standing goal (~):** long autonomous ∨ headless `-p` ∨ scheduled ∨ ultracode mission → `ND goal-condition --mission-dir {mission-dir} --record` → present the printed `/goal ...` line for the user. ? user already set `/goal` → ¬ replace: read it back, reconcile sailing orders to it, re-issue only with agreement. Skip for short interactive missions. ⛔ 📖 `references/goal-alignment.md` before setting: the evaluator sees the transcript only, so evidence must surface in chat at Ω₈.

**Estimate opt-in:** ⏸ ask *"Shall I carry out The Estimate before drafting the Battle Plan? I would recommend it for this mission — [brief reason]."* Honest recommendation: clear scope in one subsystem → skip; complex ∨ ambiguous ∨ multi-system → recommend.

- accept → Ω₂
- decline → record and skip to Ω₃:

```bash
ND skip-estimate --mission-dir {mission-dir} --reason "[one-line rationale]"
NP advance --mission-dir {mission-dir}   # SAILING_ORDERS -> ESTIMATE
NP advance --mission-dir {mission-dir}   # ESTIMATE -> BATTLE_PLAN (validator accepts: estimate_skipped recorded)
```

## 2. Conduct The Estimate 🔭Ω₂

📖 `references/the-estimate.md` · scaffold `references/admiralty-templates/estimate.md`

Ε₁ Reconnaissance (terrain, what we have) · Ε₂ Intent (what, why) · Ε₃ Effects (changes required) · Ε₄ Terrain (where each effect lands) · Ε₅ Forces (agents, models, context) · Ε₆ Coordination (deps, parallelism) · Ε₇ Control (gates, intervention points)

- Ε₁ → dispatch ≥1 Explore agents with a scouting brief from the sailing orders; synthesise. Explorer discipline per `references/the-estimate.md`: focused dispatches, structured summaries, no raw file contents.
- Ε₂–Ε₃ → subagent **Estimate-Drafter** (after Ε₁, before checkpoint 2). Ε₄–Ε₇ → subagent **Estimate-Planner** (after checkpoint 2). Both inherit the ADM model; Ω₂ is exempt from cost-savings model selection. Briefs and dispatch templates: `references/the-estimate.md`.
- Checkpoints: ⏸ after Ε₁ (present findings, invite reframing) · ⏸ after Ε₃ (approve intent + effects before planning *how*). Ε₄–Ε₇ = ADM judgement, no interruption. Collapse to one final review only when: sailing orders carry outcome ∧ metric ∧ deadline; Ε₁ reveals no surprises; work lands in a single subsystem.
- ∀ effect in §3: commander's guidance (how) ∧ acceptance criteria (what must be true when done). Criteria flow to CPTs and are verified at stand-down; CPT chooses the verification method per criterion (test, type-check, lint, review, visual).
- 💾 `{mission-dir}/estimate.md`, one H2 per question. Split to `{mission-dir}/estimate/0N-name.md` only when a section grows unwieldy.

**Advance** ESTIMATE to BATTLE_PLAN after the user approves the final estimate: `NP advance --mission-dir {mission-dir}`

## 3. Draft Battle Plan 🗺️Ω₃

Estimate conducted → plan inherits Ε₄–Ε₇; Ω₃ is operational: approved effects → task assignments. Estimate skipped → ADM performs the analysis inline here.

**Scope preservation:** ! sailing orders say extend/expand/modify existing → ∀ task modifies the existing implementation, never a parallel or replacement one. Fill `Modification targets` (functions, env vars, config found in Ε₁). New files/functions/env vars where modifying existing ones would satisfy the effect = planning error.

- ∀ effect (§3) → ≥1 task, each inside its parent effect's scope. Estimate skipped → derive tasks from sailing orders.
- Prepend commander's intent (§2) to ∀ CPT brief.
- Inherit: acceptance criteria (CPT owns verification method) · terrain = file ownership · coordination = dependencies · forces = CPT sizing + model class · control = Σ tier. Estimate skipped → supply these here.
- ? cost-savings → avoid several agents loading the same large inputs.
- ∀ task: crew composition via the decision tree in `references/crew-roles.md` → list roles, sub-tasks, sequence; or "Captain implements directly" (0 crew); note marine capacity if anticipated (max 2).
- ∀ task: `admiralty-action-required: yes` ∨ `no`.
- One task in progress per agent unless the mission explicitly requires multitasking.
- Schema: `references/admiralty-templates/battle-plan.md` · manifest: `references/admiralty-templates/ship-manifest.md`

**Workflow suitability:** ? large fan-out ∨ repeatable orchestration ∨ codebase-wide analysis ∨ broad migration ∨ audit ∨ cross-checking → ⛔ 📖 `references/workflow-doctrine.md` → decide Μ₄ ∨ Μ₅. Μ₄|Μ₅ → add a compact Workflow Charter (execution primitive, suitability, phases, human gates, verification contract, cost guardrail, fallback mode). Otherwise one line: `Workflow suitability: not selected because ...`. Σ₂|Σ₃ workflow work defaults to Μ₅: human approval belongs between runs, not mid-run.

**⛔ Battle Plan Gate — Standing Order Check.** ¬ finalize assignments until ∀ question is answered in writing with reasoning (bare yes/no ✗). Triggered Φ → apply the remedy → re-answer.

| Φ | Question |
|---|---|
| `becalmed-fleet.md` | Single-session instead of multi-agent? yes → skip Ω₄ (no squadron to form) |
| `light-squadron.md` | Task count = independent work units, or under-split? |
| `split-keel.md` | ∀ task exclusive file ownership, no conflicts? (auto-verified at Ω₄) |
| `unclassified-engagement.md` | ∀ task has a Σ tier? |
| `all-hands-on-deck.md` | ∀ task crewed only with roles its work demands? |
| `skeleton-crew.md` | Any task = exactly one crew for atomic work the CPT should do? |
| `crew-without-canvas.md` | ∀ agent justified by actual scope? |
| `captain-at-the-capstan.md` | Crewed tasks: CPT coordinates, ¬ implements? |
| `press-ganged-navigator.md` | RCN assigned implementation? |
| `admiral-at-the-helm.md` | ADM assigned implementation (beyond read-only recombination)? |
| `wrong-ensign.md` | Planned coordination tools match the Μ? |
| `pulling-the-oar.md` | Subagent failure plan = fix brief + re-dispatch, ¬ absorb into senior context? |

Situations not covered → Standing Orders table below.

**💾 Persist:** write the full plan to `{mission-dir}/battle-plan.md` per the template: commander's intent verbatim (§2), ∀ task brief, the Standing Order Check answers. Safe compaction point: ADM state is on disk. No ND calls here (owners arrive at Ω₄).

## 4. Form the Squadron 🚢Ω₄

**Mode** per `references/squadron-composition.md`; an explicit user request overrides the matrix.

- Μ₁ sequential ∨ low complexity ∨ heavy same-file editing
- Μ₂ parallel, fully independent, report to ADM only
- Μ₃ shared task list ∨ peer messaging ∨ coordinated deliverables ∨ ≥4 CPTs
- Μ₄ one autonomous workflow run: large fan-out, repeatable review, broad migration, audit, cross-checked research
- Μ₅ Nelson-gated workflow stages with human approval between

**⛔ Mode-Tool Gate** 📖 `references/tool-mapping.md`

- Μ₂: CPTs ✗ `TaskCreate`/`TaskList`/`TaskGet`/`TaskUpdate`/`SendMessage(type="message")`; they report via the `Agent` return value only. ADM uses `TaskCreate`/`TaskUpdate`/`TaskList` for visibility (CPTs cannot see them).
- Μ₃: ✗ `Agent` with `subagent_type` for CPTs (RM still use it). `TeamCreate` first → `Agent` with `team_name` + `name`. Coordinate via `TaskList` + `SendMessage`.
- Μ₁: ADM uses `TaskCreate`/`TaskUpdate`/`TaskList`/`TaskGet` while completing tasks in order.
- Μ₄: the workflow is a fleet asset, not ordinary CPTs. Nelson v1 produces a Workflow Charter, prompt, and telemetry plan; it does not call a workflow API or generate `.claude/workflows/*.js`.
- Μ₅: ∀ stage = separate asset. Stop at the gate, present results, launch the next run only after explicit approval.

**Task list visibility** (∀ Μ, including Μ₁): ∀ battle-plan task → `TaskCreate` with `subject` (imperative task name), `description` (one-line deliverable), `activeForm` (present continuous). All start `pending`. Μ₄|Μ₅: also create entries for workflow phases or gates being tracked.

- ∀ task → CPT + ship name from `references/crew-roles.md` by weight: frigate general · destroyer high-risk · patrol vessel small · flagship critical-path · submarine research.
- Finalize manifests: crew roles per task, or "Captain implements directly".
- Medium/high threat → +1 RCN. Squadron cap 10 (ADM + CPTs + RCN); crew are additional.
- ? sailing orders express cost-savings → 📖 `references/model-selection.md` → weight-based model on ∀ `Agent` call + haiku briefing enhancements for haiku agents.

```
SQUADRON FORMATION ORDERS

Mode: [single-session | subagents | agent-team | workflow | hybrid-workflow]
Captain count: [N]

Ships:
  [Ship name] — [vessel type] — [one-line task summary]
    Crew: [roles, or "Captain implements directly"]
  [repeat for each ship]

[Red-cell navigator — HMS X, if present]
```

Μ₄|Μ₅ also include:

```
WORKFLOW CHARTER
Execution primitive: [workflow | hybrid-workflow]
Suitability: [why dynamic workflow orchestration is justified]
Phases: [probe / full run / stage names]
Human gates: [approval points, especially for hybrid-workflow]
Verification contract: [how accepted, rejected, and uncertain findings are handled]
Cost guardrail: [Sounding-the-Channel probe, scope cap, token/time stop]
Fallback mode: [agent-team | single-session]
```

∃ task `admiralty-action-required: yes` → append before awaiting approval:

```
ADMIRALTY ACTION LIST — Actions required from Admiralty

1. [Task name]
   action: [what you must do]
   timing: [before task starts | after task completes]
   unblocks: [task name or stand-down]
```

`timing: before task starts` → sign-off required before that CPT is spawned.

⛔ ¬ spawn agents, ¬ create tasks, ¬ launch Μ₄|Μ₅ until the user approves. Changes requested → revise and redisplay. Headless/CI: `ND headless --auto-approve` combines Ω₁–Ω₃ and skips the gate (`references/structured-data.md`).

**ND capture** (once formation is approved). Composite, recommended: write a plan JSON, then

```bash
ND form --mission-dir {mission-dir} --plan {mission-dir}/plan-input.json --mode [mode]
```

= register tasks + record squadron + DAG metrics + conflict scan in one step. Plan JSON schema: `references/structured-data.md`.

Individual alternative, in order:

1. `ND task --mission-dir {mission-dir} --id N --name "..." --owner "..." ...` ∀ task
2. `ND plan-approved --mission-dir {mission-dir}` → finalises plan, DAG metrics
3. `NP advance --mission-dir {mission-dir}` BATTLE_PLAN ⟶ FORMATION (validates ∀ task has a Σ tier)
4. `ND squadron --mission-dir {mission-dir} --admiral "..." --admiral-model [model] --captain "name:class:model:task_id" ... --mode [mode]` (repeat `--captain`)
5. `NCS --plan {mission-dir}/battle-plan.json` → ⛔ conflicts → resolve and update the plan first
6. `NP advance --mission-dir {mission-dir}` FORMATION ⟶ PERMISSION

⛔ before Ω₅: sailing orders exist ∧ ∀ task has owner + deliverable ∧ ∀ task has a Σ tier.

**Crew briefing:** spawning and assignment are two steps. 1) `Agent` ∀ CPT with a brief from `references/admiralty-templates/crew-briefing.md` in the prompt: teammates start with a clean slate and need explicit mission context. 2) `TaskUpdate` assigns work to the existing entries. Parameters by Μ: `references/tool-mapping.md`.

**Task status after formation:** Μ₃ `TaskUpdate` owner = CPT name, status `in_progress` as each spawns · Μ₂ `in_progress` as each dispatches · Μ₁ `in_progress` as ADM begins each · Μ₄ run/phase `in_progress` + log `workflow_run_started` · Μ₅ only the approved stage `in_progress`, later stages stay `pending`.

**Edit permissions:** ∀ agent whose task edits files → `mode: "acceptEdits"` on the `Agent` call; omitting it can stall the agent silently at its first edit. When in doubt, include it.

**Turnover briefs:** a ship relieved for context exhaustion writes a typed handoff via `ND handoff ...` (`references/structured-data.md`); optional prose companion `references/admiralty-templates/turnover-brief.md`. Procedure: `references/damage-control/relief-on-station.md`.

## 5. Get Permission to Sail 🫡Ω₅

1. `becalmed-fleet.md` in effect → display the complete battle plan. : display the complete squadron formation (battle plan from Ω₃ also available).
2. Μ₄|Μ₅ → also display the Workflow Charter, verification contract, cost guardrail, fallback mode, next human gate.
3. ⛔⏸ explicit permission required. Μ₄|Μ₅: before ∀ launch; Μ₅: repeat this gate between stages.

Granted → log and advance PERMISSION ⟶ UNDERWAY (unlocks spawning and task creation):

```bash
ND event --mission-dir {mission-dir} --type permission_granted --checkpoint 0
NP advance --mission-dir {mission-dir}
```

## 6. Run Quarterdeck Rhythm 📊Ω₆

**Idle rule (immediate, never deferred to a checkpoint).** ∀ idle notification from a ship, ask first: 1) task marked complete? 2) any pending task depends on its output? 3) Μ₃ only: ADM received and processed its results? Complete ∧ no dependents → shutdown per `references/standing-orders/paid-off.md`. Μ₃: confirm receipt (`SendMessage` or read output files) before `shutdown_request`. Μ₂: `Agent` returns synchronously, no confirmation needed. Evaluate ∀ notification independently against the current `TaskList`, even while other ships run.

**Shutdown ceiling:** 3 unacknowledged `shutdown_request` to one agent → abandon, note in the captain's log, continue. `TeamDelete` blocked → `references/damage-control/man-overboard.md`.

- ADM = coordination + unblocking only. ADM sets the mood: acknowledge progress, recognise strong work, stay cheerful under pressure.
- **⛔ Checkpoint Cadence Gate:** ¬ process a third task completion without writing a quarterdeck checkpoint; confirm the last checkpoint is ≤2 completions old before dispatching new work or processing the next completion. The on-disk report is the only recovery point after compaction.
- Checkpoint after every 1–2 completions ∨ CPT reports a blocker ∨ CPT idle with unverified output:
    - `TaskList` → states `pending` `in_progress` `completed`; `TaskUpdate` completed tasks (Μ₂|Μ₁ ADM updates; Μ₃ CPTs or ADM).
    - Blockers → concrete next action; `SendMessage` to unblock or redirect.
    - ∀ crew member has an active sub-task? flag idle crew or role mismatch. Marines returned, outputs incorporated?
    - Safety net: an idle ship with a complete task missed between checkpoints → apply `paid-off.md` now.
    - Burn vs token/time budget.
    - Μ₄|Μ₅ telemetry: phase, agents complete/total, token burn, elapsed, failed agents, accepted/rejected/uncertain findings, next gate → events `workflow_probe_completed` ∨ `workflow_run_completed` ∨ `workflow_run_stopped`.
    - Η: collect damage reports from ∀ ship (ADM checks its own too) → update the readiness board → act per `references/damage-control/hull-integrity.md`. 💾 ∀ ship ∀ checkpoint `{mission-dir}/damage-reports/{ship-name}.json` per `references/admiralty-templates/damage-report.md`, even at Η🟢.
    - Φ scan, ∀ order "has this arisen since the last checkpoint?" yes → remedy now: `admiral-at-the-helm.md` ADM drifted into implementation? · `drifting-anchorage.md` scope crept, parallel implementation, duplicate function, new env var instead of extending? · `captain-at-the-capstan.md` CPT implementing with crew active? · `pressed-crew.md` crew outside role? · `press-ganged-navigator.md` RCN implementing? · `all-hands-on-deck.md` idle or unjustified crew? · `battalion-ashore.md` marines used for crew work or sustained tasks? · `wrong-ensign.md` wrong-mode tools? · `pulling-the-oar.md` senior absorbed a failed dispatch instead of re-dispatching?
    - 💾 `{mission-dir}/quarterdeck-report.md` ∀ checkpoint per `references/admiralty-templates/quarterdeck-report.md`, even at Η🟢. Rotate first: ? file exists → N = highest `quarterdeck-report-[0-9]*.md` + 1 (0 if none) → rename existing to `quarterdeck-report-N.md` → write the new one.
    - `ND checkpoint --mission-dir {mission-dir} --pending N --in-progress N --completed N ...` with progress, budget, hull, decisions. Between checkpoints `ND event --mission-dir {mission-dir} --type <event_type> ...` for completions, blockers, Η threshold crossings, Φ violations. Event types: `references/structured-data.md`.
    - `TaskList` description prefixed `[AWAITING-ADMIRALTY]:` → surface to Admiralty immediately, ¬ batch.
    - ∀ task `admiralty-action-required: yes` with status `completed` → quarterdeck log records admiralty sign-off? ¬ → flag for manual verification.
- Task drifts from the mission metric → re-scope early. Difficulties → Damage Control table below.

```
Status: 3/5 tasks complete, 1 blocked, 1 in progress
Blocker: HMS Resolute waiting on API schema from HMS Swift
Action: Redirect HMS Swift to prioritise schema export
Budget: ~40% tokens consumed, on track
Hull: All ships green
```

Coordination tools: `references/tool-mapping.md`. Recognition and graduated correction: `references/commendations.md`.

## 7. Set Action Stations 🎯Ω₇

⛔ 📖 `references/action-stations.md`; apply the Σ tier to ∀ task.

- Evidence before `completed`: test or validation output ∧ failure modes + rollback notes ∧ Σ₁+ red-cell review (`references/admiralty-templates/red-cell-review.md`).
- Μ₄|Μ₅: apply the battle plan's verification contract before accepting outputs. Accepted findings carry the promised evidence; rejected or uncertain findings are surfaced separately; Σ₂+ outputs still need adversarial review or human confirmation.
- Triggers: task completion · agent idle with unverified output · before final synthesis.
- Crewed tasks: outputs stay within role boundaries (`references/crew-roles.md`; violations → Standing Orders table).
- RM follow Σ rules in `references/royal-marines.md`; Σ₂+ deployments need ADM approval; CPTs brief marines with `references/admiralty-templates/marine-deployment-brief.md`.
- Tasks without a tier ∨ RCN given implementation → Standing Orders table.

## 8. Stand Down And Log Action 📜Ω₈

- Stop or archive ∀ agent sessions, including crew.
- `ND stand-down --mission-dir {mission-dir} --outcome-achieved --actual-outcome "..." --metric-result "..."` before writing the log (arguments: `references/structured-data.md`).
- 💾 `{mission-dir}/captains-log.md` per `references/admiralty-templates/captains-log.md`; chat output alone ✗. Contents: decisions + rationale · diffs or artifacts · validation evidence · open risks + follow-ups · Mentioned in Despatches (`references/commendations.md`) · reusable patterns + failure modes.
- Task list: remaining `in_progress` → `completed` if done, else note incomplete tasks in the log.
- Delete `.nelson/.active-{SESSION_ID}`.
- **⛔ Mission Complete Gate:** ¬ declare complete until `{mission-dir}/captains-log.md` exists on disk ∧ is confirmed readable. High context pressure → minimal log noting abbreviated sections, but the file must exist. Skipping Ω₈ ✗.
- **Standing goal:** ? `/goal` active → state in chat: metric result (matching the sailing orders), `captains-log.md` written (with path), stand-down recorded → the goal auto-clears. ✗ tell the user to run `/goal clear` on success. Abandoned mission → `scuttle-and-reform`, state the blocking reason in chat, `ND event --type goal_cleared`. See `references/goal-alignment.md`.
- **GitHub star (once, success only):** after the gate passes → `references/stand-down-star.md`.

## Standing Orders

Consult the order that matches the situation. The library is empirically extensible: `scripts/nelson_data_patterns.py` mines candidate orders from mission patterns for human review.

| Situation | Standing Order |
|---|---|
| Φ₁ choosing single-session vs multi-agent | `references/standing-orders/becalmed-fleet.md` |
| Φ₂ tasks under-split onto fewer CPTs than independence warrants | `references/standing-orders/light-squadron.md` |
| Φ₃ deciding whether to add another agent | `references/standing-orders/crew-without-canvas.md` |
| Φ₄ assigning files to agents in the battle plan | `references/standing-orders/split-keel.md` |
| Φ₅ task scope drifting from sailing orders | `references/standing-orders/drifting-anchorage.md` |
| Φ₆ ADM implementing instead of coordinating (beyond read-only recombination) | `references/standing-orders/admiral-at-the-helm.md` |
| Φ₇ assigning work to the RCN | `references/standing-orders/press-ganged-navigator.md` |
| Φ₈ tasks proceeding without a Σ tier | `references/standing-orders/unclassified-engagement.md` |
| Φ₉ CPT implementing instead of coordinating crew | `references/standing-orders/captain-at-the-capstan.md` |
| Φ₁₀ crewing every role regardless of need | `references/standing-orders/all-hands-on-deck.md` |
| Φ₁₁ spawning one crew member for an atomic task | `references/standing-orders/skeleton-crew.md` |
| Φ₁₂ assigning crew work outside their role | `references/standing-orders/pressed-crew.md` |
| Φ₁₃ CPT deploying marines for crew work or sustained tasks | `references/standing-orders/battalion-ashore.md` |
| Φ₁₄ CPT finished autonomous work, needs human action to continue | `references/standing-orders/awaiting-admiralty.md` |
| Φ₁₅ agent finished with no remaining work in the dependency graph | `references/standing-orders/paid-off.md` |
| Φ₁₆ using tools from the wrong execution mode | `references/standing-orders/wrong-ensign.md` |
| Φ₁₇ senior absorbing a failed subagent's work instead of fixing the brief | `references/standing-orders/pulling-the-oar.md` |

## Damage Control

Consult the procedure that matches the situation.

| Situation | Procedure |
|---|---|
| Δ₁ agent unresponsive, looping, or producing nothing useful | `references/damage-control/man-overboard.md` |
| Δ₂ session interrupted (context limit, crash, timeout) | `references/damage-control/session-resumption.md` |
| Δ₃ completed task found faulty, other tasks sound | `references/damage-control/partial-rollback.md` |
| Δ₄ mission cannot succeed, continuing wastes budget | `references/damage-control/scuttle-and-reform.md` |
| Δ₅ issue exceeds current authority or needs clarification | `references/damage-control/escalation.md` |
| Δ₆ ship's crew consuming disproportionate tokens or time | `references/damage-control/crew-overrun.md` |
| Δ₇ ship's context depleted, needs replacement | `references/damage-control/relief-on-station.md` |
| Δ₈ ship context approaching limits | `references/damage-control/hull-integrity.md` |
| Δ₉ automated budget, hull, and idle alarms crossing thresholds | `references/damage-control/circuit-breakers.md` |
| Δ₁₀ preparing the mission directory at session start | `references/damage-control/session-hygiene.md` |
| Δ₁₁ agent team communication failure (lost IDs, message bus down) | `references/damage-control/comms-failure.md` |

## Admiralty Doctrine

- ∀ ADM compaction summary MUST include: re-read the quarterdeck report at the mission directory path to recover `{mission-dir}`; path unknown → read `.nelson/.active-{SESSION_ID}` if SESSION_ID is known, else list `.nelson/missions/` and ⏸ let the user pick; then 📖 `references/standing-orders/admiral-at-the-helm.md` to confirm the coordination role.
- `/compact` is safe at ∀ phase boundary (after Ω₁, Ω₂, Ω₃, Ω₄, and at ∀ Ω₆ checkpoint). The one unsafe window is inside Ω₅: between user approval and the `permission_granted` / phase advance / spawn turn.
- Optimise for mission throughput, not equal work distribution.
- Prefer replacing stalled agents over waiting on undefined blockers.
- Recognise strong performance; motivation compounds across missions.
- Keep coordination messages targeted and concise.
- Escalate uncertainty early with options and one recommendation.
