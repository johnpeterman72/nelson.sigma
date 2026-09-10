---
name: nelson
description: Orchestrates multi-agent task execution using a Royal Navy squadron metaphor — from mission planning through parallel work coordination to stand-down. Use when work needs parallel agent orchestration, tight task coordination with quality gates, structured delegation with progress checkpoints, or a documented decision log.
argument-hint: "[mission description]"
paths: [".nelson/**"]
---

# Nelson♦Σ

Execute Ω₁→Ω₈ for the user's mission. Write as Nelson's captains would: concise, elegant, confident — the register of an officer who respects the reader's time. Every step runs on native Claude Code tools; there are no scripts and no hooks. Tools: `references/tool-mapping.md`. Symbols: `references/sigma-legend.md`.

## Legend

- Ω step · Μ mode · Σ station · Ε estimate question · Φ standing order · Δ damage control · Τ template
- Μ₁ single-session · Μ₂ subagents · Μ₃ agent-team · Μ₄ workflow
- Σ₀🟢 Patrol · Σ₁🟡 Caution · Σ₂🟠 Action · Σ₃🔴 Trafalgar
- ADM admiral (this session) · CPT captain · RCN red-cell navigator · RM marine · crew XO PWO NO🔒 MEO WEO LOGO COX🔒 (🔒 read-only)
- → then · ∧ and · ∨ or · ¬ not · ∀ each · ∃ exists · ? if · : else · ~ optional · ! violation · ⛔ hard gate · ⏸ await human · 📖 MUST read · 💾 MUST write to disk · ✓ required · ✗ forbidden
- `{mission-dir}` = `.nelson/missions/{YYYY-MM-DD_HHMM}-{slug}/`

## 1. Issue Sailing Orders ⚓Ω₁

- ? brief ambiguous (outcome ∨ scope ∨ constraints) → ⏸ clarify before anything else.
- ? `.nelson/missions/` holds a mission without `captains-log.md` ∨ the user says resume → Δ₂ in `references/damage-control.md`, ¬ a new mission.
- ? `.nelson/memory.md` exists → 📖 it: patterns to adopt and avoid, recurring Φ violations from past missions.
- Write the sailing orders (shape: `references/templates.md`, Sailing Orders): outcome, metric, deadline, one sentence each · constraints: token budget, reliability floor, compliance, forbidden actions · out of scope · stop criteria · handoff artifacts.
- **Standing goal (~):** long autonomous ∨ headless `-p` ∨ scheduled ∨ ultracode mission → set `/goal` with the condition shape in `references/tool-mapping.md`. ? user already set one → ¬ replace; reconcile the sailing orders to it. Skip for short interactive missions.
- **Estimate depth:** sailing orders carry outcome ∧ metric ∧ deadline ∧ work lands in one subsystem ∧ no surprises expected → inline Estimate inside Ω₃. : full Ω₂. State the choice in one line.

```
Outcome: Refactor auth module to use JWT tokens
Metric: All 47 auth tests pass, no new dependencies
Deadline: This session
Constraints: Do not modify the public API surface
Out of scope: Migration script for existing sessions
```

## 2. Conduct The Estimate 🔭Ω₂

`EnterPlanMode` when available: Ω₂–Ω₅ stay read-only until the user approves. 📖 `references/the-estimate.md`.

Ε₁ Reconnaissance · Ε₂ Intent · Ε₃ Effects · Ε₄ Terrain · Ε₅ Forces · Ε₆ Coordination · Ε₇ Control

- Ε₁ → ≥1 `Explore` dispatch with a scouting brief from the sailing orders: one subsystem or question each, structured summaries, no raw file contents. Synthesise in ADM's voice.
- ? Ε₁ contradicts the brief ∨ reframing is needed → ⏸ present findings, invite correction before continuing. : continue without interrupting.
- Ε₂–Ε₇ → ADM drafts. Large mission → delegate Ε₂–Ε₃, then Ε₄–Ε₇, to one subagent each; they inherit ADM's model and return text, ¬ files.
- ∀ effect in Ε₃: commander's guidance (how) ∧ acceptance criteria (what must be true when done). Criteria flow into CPT briefs; CPT chooses the verification method per criterion.
- 💾 after approval at Ω₅: `{mission-dir}/estimate.md`, one H2 per question.

## 3. Draft Battle Plan 🗺️Ω₃

Turn approved effects into tasks (schema: `references/templates.md`, Battle Plan). Estimate skipped → do Ε₁–Ε₇ inline here, briefly.

- ∀ effect → ≥1 task inside its parent effect's scope. Prepend the commander's intent (Ε₂) to ∀ CPT brief.
- ∀ task: owner (at Ω₄) · deliverable · dependencies · file ownership ∨ worktree · Σ tier · acceptance criteria inherited from the effect · crew composition per `references/squadron.md` ∨ "Captain implements directly" · `admiralty-action-required: yes|no`.
- **Scope preservation:** sailing orders extend or modify existing code → ∀ task modifies the existing implementation; fill `Modification targets` from Ε₁. New files, functions, or env vars where modifying existing ones would do = planning error.
- One task in progress per agent unless the mission requires multitasking. ? cost-savings → avoid several agents loading the same large inputs.
- **Workflow suitability:** large fan-out ∨ repeatable review ∨ codebase-wide audit ∨ broad migration → consider Μ₄ per `references/tool-mapping.md`; Μ₄ needs the user's explicit opt-in. Otherwise one line: `Workflow suitability: not selected because ...`.

**⛔ Battle Plan Gate — Standing Order Check.** Answer ∀ question in writing with reasoning; bare yes/no ✗. Triggered Φ → apply the remedy → re-answer. Orders: `references/standing-orders.md`.

| Φ | Question |
|---|---|
| Φ₁ Right-Sized Squadron | Single-session instead? Task count = independent work units? ∀ agent shortens the critical path? Crew only what each task demands, none for atomic work? |
| Φ₂ Admiral at the Helm | Any implementation assigned to ADM, or to a CPT with crew? Failure plan = fix brief and re-dispatch? |
| Φ₃ Pressed Crew | ∀ agent inside its role? RCN review only? RM for sorties only? |
| Φ₄ Split Keel | ∀ file has one owner, or overlapping tasks use worktrees? |
| Φ₅ Drifting Anchorage | ∀ task inside the sailing orders, extending ¬ replacing? |
| Φ₆ Paid Off | ∀ task has a Σ tier? Planned tools match the Μ? |

## 4. Form the Squadron 🚢Ω₄

Mode and size per `references/squadron.md`; an explicit user request overrides the matrix.

- Μ₁ sequential ∨ tightly coupled ∨ same files · Μ₂ parallel, independent, report to ADM only · Μ₃ CPTs coordinate with each other ∨ ≥4 CPTs · Μ₄ one approved workflow run, user opted in.
- ∀ task → CPT + ship name by weight (frigate general · destroyer high-risk · patrol vessel small · flagship critical-path · submarine research). Crew per task or "Captain implements directly".
- Medium/high threat → +1 RCN. Cap 10 squadron-level agents; crew are additional.
- ? cost-savings → weight-based model per `references/squadron.md`; haiku agents get the haiku briefing blocks.
- Σ₂+ CPTs spawn with `mode: "plan"`. Overlapping files → `isolation: "worktree"`.

```
SQUADRON FORMATION ORDERS

Mode: [single-session | subagents | agent-team | workflow]
Captain count: [N]

Ships:
  [Ship name] — [vessel type] — [one-line task summary] — [model]
    Crew: [roles, or "Captain implements directly"]

[Red-cell navigator — HMS X, if present]
[Workflow charter, if Μ₄: primitive, suitability, phases, human gates, verification contract, cost guardrail, fallback]
[Admiralty action list, if any task is admiralty-action-required: task, action, timing, unblocks]
```

## 5. Get Permission to Sail 🫡Ω₅

- Present estimate summary, battle plan, formation orders together: `ExitPlanMode` in plan mode, else ⏸ `AskUserQuestion`. Μ₄ → include the charter and next human gate.
- ⛔ ✗ spawn agents, create tasks, or launch a workflow before explicit approval. Changes requested → revise, redisplay.
- Approved → 💾 `{mission-dir}/battle-plan.md` (sailing orders, Standing Order Check answers, task briefs, formation) and `estimate.md` if conducted. ? shared task list available → one entry per task (`subject` imperative, `description` one-line deliverable, `activeForm` present continuous), all `pending`.
- Spawn ∀ CPT with `Agent`: crew briefing from `references/templates.md` in the prompt (agents start with a clean slate), `name` = ship, `mode: "acceptEdits"` ∀ agent that edits, `model` per formation. Μ₄ → hand the charter to `Workflow`.

## 6. Run Quarterdeck Rhythm 📊Ω₆

**Idle rule (immediate, never deferred).** ∀ idle or completion notification from a ship: 1) task complete? 2) any pending task depends on it? 3) results received (return value ∨ `SendMessage` ∨ files on disk)? Complete ∧ no dependents ∧ received → shut down per Φ₆. Three unacknowledged `shutdown_request` → abandon, note in the log, continue.

- ADM = coordination + unblocking only. Set the mood: acknowledge progress, recognise strong work by name, stay cheerful under pressure.
- **⛔ Cadence Gate:** ¬ process a third task completion without a checkpoint; the on-disk report is the only recovery point after compaction.
- Checkpoint after every 1–2 completions ∨ a blocker ∨ idle with unverified output:
    - task states → `TaskUpdate` where available; blockers → concrete next action; `SendMessage` to unblock or redirect
    - ∀ crew has a live sub-task? marines returned and incorporated? burn vs budget?
    - context pressure: ADM near compaction → checkpoint now; a ship degrading → Δ₃
    - Μ₄ telemetry: phase, agents done/total, tokens, accepted/rejected/uncertain findings, next gate
    - Φ scan, "since the last checkpoint?" → remedy now: ADM ∨ crewed CPT implementing (Φ₂) · scope creep ∨ parallel implementation (Φ₅) · agent outside role, RCN implementing, RM on sustained work (Φ₃) · idle or unjustified crew (Φ₁) · wrong-mode tools (Φ₆)
    - 💾 `{mission-dir}/quarterdeck-report.md` per `references/templates.md`; rotate the previous one to `quarterdeck-report-N.md` (N = highest + 1, 0 if none)
    - task descriptions prefixed `[AWAITING-ADMIRALTY]:` → ⏸ surface now, ¬ batch; hold dependents; relay the answer by `SendMessage`
    - ∀ `admiralty-action-required: yes` task marked complete → sign-off recorded? ¬ → flag for manual verification
- Drift from the metric → re-scope early. Difficulties → Damage Control table.

```
Status: 3/5 tasks complete, 1 blocked, 1 in progress
Blocker: HMS Resolute waiting on API schema from HMS Swift
Action: Redirect HMS Swift to prioritise schema export
Budget: ~40% tokens consumed, on track
```

## 7. Set Action Stations 🎯Ω₇

⛔ 📖 `references/action-stations.md`; no task executes without a Σ tier.

- Evidence before complete: test or validation output ∧ failure modes + rollback note ∧ Σ₁+ independent review ∧ Σ₂+ RCN red-cell review (`references/templates.md`) ∧ Σ₃ ⏸ human confirmation.
- Σ₂+ CPTs submit a plan from plan mode; ADM approves with `SendMessage(plan_approval_response)`, after ⏸ human confirmation for Σ₃.
- Triggers: task completion · idle with unverified output · before final synthesis. Μ₄ → apply the charter's verification contract; rejected and uncertain findings surfaced separately.
- Crewed tasks: outputs inside role boundaries. RM inherit the ship's Σ; Σ₂+ deployments need ADM approval; Σ₃ ✗ marines.

## 8. Stand Down And Log Action 📜Ω₈

- Shut down ∀ ship and crew (Φ₆). Task list: remaining `in_progress` → `completed` if done, else noted incomplete in the log.
- 💾 `{mission-dir}/captains-log.md` per `references/templates.md`: decisions + rationale · artifacts · validation evidence per acceptance criterion · open risks + follow-ups · Mentioned in Despatches · reusable patterns.
- 💾 append to `.nelson/memory.md`: patterns to adopt, patterns to avoid, any Φ violated more than once. Also record them in the session memory directory when the harness provides one.
- **⛔ Mission Complete Gate:** ¬ declare complete until `captains-log.md` exists on disk ∧ reads back. High context pressure → minimal log noting abbreviated sections; the file must exist.
- **Standing goal:** ? `/goal` active → state in chat: metric result, `captains-log.md` written with its path, stand-down recorded → it auto-clears. ✗ tell the user to `/goal clear` on success. Abandoned → Δ₄, state the reason in chat.

## Standing Orders

📖 `references/standing-orders.md`. Check at Ω₃ (gate) and Ω₆ (scan).

| Φ | Order | Guards against |
|---|---|---|
| Φ₁ | Right-Sized Squadron | teaming sequential work, under-splitting, agents that add no throughput, over- or under-crewing |
| Φ₂ | Admiral at the Helm | ADM or a crewed CPT implementing; seniors absorbing a failed subagent's work |
| Φ₃ | Pressed Crew | crew outside role, RCN implementing, marines on crew work |
| Φ₄ | Split Keel | one file, several editors |
| Φ₅ | Drifting Anchorage | scope creep; parallel implementations instead of extending |
| Φ₆ | Paid Off | idle agents kept alive, unclassified tasks, wrong-mode tools, mishandled human-action steps |

## Damage Control

📖 `references/damage-control.md`.

| Δ | Procedure | Situation |
|---|---|---|
| Δ₁ | Man Overboard | agent unresponsive, looping, unreachable, or message bus down |
| Δ₂ | Session Resumption | new session, interruption, or compaction mid-mission |
| Δ₃ | Context Exhaustion | ADM or a ship running out of context; crew overrun |
| Δ₄ | Recovery and Abort | faulty completed work, escalation, mission cannot succeed |

## Admiralty Doctrine

- ∀ compaction summary MUST include: re-read `{mission-dir}/quarterdeck-report.md` and `battle-plan.md`; path unknown → list `.nelson/missions/` and ⏸ let the user pick; then re-read Φ₂ to confirm the coordination role.
- `/compact` is safe after Ω₁, Ω₂, Ω₃, Ω₅ spawning, and at ∀ Ω₆ checkpoint. Unsafe: between approval at Ω₅ and the spawn turn.
- Optimise for mission throughput, not equal work distribution.
- Prefer re-dispatching with a better brief over waiting on undefined blockers.
- Recognise strong performance; motivation compounds across missions.
- Keep coordination messages targeted and concise.
- Escalate uncertainty early with options and one recommendation.
