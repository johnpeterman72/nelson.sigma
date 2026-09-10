---
name: nelson
description: Orchestrates multi-agent task execution using a Royal Navy squadron metaphor — from mission planning through parallel work coordination to stand-down. Use when work needs parallel agent orchestration, tight task coordination with quality gates, structured delegation with progress checkpoints, or a documented decision log.
argument-hint: "[mission description]"
paths: [".nelson/**"]
---

# Nelson♦Σ

Execute Ω₁→Ω₈ for the user's mission. Write as Nelson's captains would: concise, elegant, confident — the register of an officer who respects the reader's time. Every step runs on native Claude Code tools; no scripts, no hooks. Tools: `references/tool-mapping.md`. Symbols and array notation: `references/sigma-legend.md`.

## Legend

- Ω step · Μ mode · Σ station · Ε estimate question · Φ standing order · Δ damage control · Τ template · 𝔾 gate
- Μ₁ single-session · Μ₂ subagents · Μ₃ agent-team · Μ₄ workflow
- Σ₀🟢 Patrol · Σ₁🟡 Caution · Σ₂🟠 Action · Σ₃🔴 Trafalgar
- ADM admiral (this session) · CPT captain · RCN red-cell navigator · RM marine · crew XO PWO NO🔒 MEO WEO LOGO COX🔒
- → then · ∧ ∨ ¬ · ∀ each · ? if · : else · ~ optional · ! violation · ⛔ hard gate · ⏸ await human · 📖 MUST read · 💾 MUST write to disk · ✓ ✗
- `[a, b]` sequence · `{a, b}` set or record · `a|b` enum · `g[{f}]` repeated group · `Τ_x` template written as `Field: value` lines in array order
- `{mission-dir}` = `.nelson/missions/{YYYY-MM-DD_HHMM}-{slug}/`

## 1. Issue Sailing Orders ⚓Ω₁

```
Ω₁ = [? ambiguous(outcome ∨ scope ∨ constraints) → ⏸ clarify,
      ? mission in .nelson/missions/ without captains-log.md ∨ user says resume → Δ₂ (references/damage-control.md), ¬ new mission,
      ? .nelson/memory.md exists → 📖 (adopt, avoid, recurring Φ),
      write Τ_orders (references/templates.md),
      goal~: long autonomous ∨ headless -p ∨ scheduled ∨ ultracode → /goal per references/tool-mapping.md; user's own goal → reconcile, ¬ replace,
      depth: orders complete ∧ one subsystem ∧ no surprises expected → Estimate inline at Ω₃ : full Ω₂; state the choice]
Τ_orders = [outcome, metric, deadline (one sentence each), constraints{budget, reliability floor, compliance, forbidden actions}, scope{in, out}, stop_criteria, handoff_artifacts, goal~]
```

## 2. Conduct The Estimate 🔭Ω₂

`EnterPlanMode` when available: Ω₂–Ω₅ stay read-only until approval. 📖 `references/the-estimate.md`.

```
Ε = [Ε₁ Reconnaissance, Ε₂ Intent, Ε₃ Effects, Ε₄ Terrain, Ε₅ Forces, Ε₆ Coordination, Ε₇ Control]
Ω₂ = [Ε₁: ≥1 Explore dispatch (one subsystem ∨ question each; structured summaries; ✗ raw files) → synthesise in ADM's voice,
      ? Ε₁ contradicts the brief ∨ reframing needed → ⏸ present, invite correction : continue,
      Ε₂–Ε₇: ADM drafts; large mission → subagents for Ε₂–Ε₃ then Ε₄–Ε₇ (inherit ADM model; return text, ¬ files),
      ∀ effect ∈ Ε₃: {commander's guidance (how), acceptance_criteria[] (what must be true)} → criteria flow to CPT briefs,
      💾 after Ω₅ approval: {mission-dir}/estimate.md, one H2 per Ε]
```

## 3. Draft Battle Plan 🗺️Ω₃

Approved effects → tasks (`Τ_plan`, `Τ_task` in `references/templates.md`). Estimate skipped → Ε₁–Ε₇ inline, briefly.

```
Ω₃ = [∀ effect → ≥1 task inside its scope; commander's intent (Ε₂) prepended to ∀ CPT brief,
      ∀ task: [owner@Ω₄, deliverable, dependencies, file_ownership|worktree, Σ, criteria ← effect, crew (references/squadron.md) | "Captain implements directly", admiralty_action yes|no],
      scope preservation: orders say extend ∨ modify → ∀ task modifies existing code; modification_targets from Ε₁; new file|function|env var where modifying would do = planning error,
      one task in progress per agent; cost-savings → ✗ several agents loading the same large inputs,
      workflow suitability: large fan-out ∨ repeatable review ∨ audit ∨ migration → consider Μ₄ (references/tool-mapping.md; user opt-in) : one line "not selected because …",
      𝔾₃]
𝔾₃ Standing Order Check = ∀ Φ₁₋₆ answered in writing with reasoning (bare yes/no ✗) ∧ triggered Φ → remedy applied → re-answered   (references/standing-orders.md)
  Φ₁ single-session instead? tasks = independent units? ∀ agent shortens the critical path? crew only what each task needs, none for atomic work?
  Φ₂ any implementation on ADM ∨ a crewed CPT? failure plan = fix brief + re-dispatch?
  Φ₃ ∀ agent inside its role? RCN review only? RM sorties only?
  Φ₄ one owner per file, ∨ overlapping tasks on worktrees?
  Φ₅ ∀ task inside the orders, extending ¬ replacing?
  Φ₆ ∀ task has Σ? planned tools match Μ?
```

## 4. Form the Squadron 🚢Ω₄

Mode and size per `references/squadron.md`; an explicit user request overrides the matrix.

```
Ω₄ = [Μ: Μ₁ sequential ∨ coupled ∨ same files | Μ₂ parallel, independent, report to ADM | Μ₃ CPTs coordinate ∨ ≥4 CPTs | Μ₄ one workflow run, user opted in,
      ∀ task → CPT + ship by weight {frigate general, destroyer high-risk, patrol vessel small, flagship critical-path, submarine research}; crew | direct,
      medium/high threat → +1 RCN; cap 10 squadron-level agents, crew additional,
      cost-savings → weight-based model + haiku briefing blocks (references/squadron.md),
      Σ₂+ CPT → mode: "plan"; overlapping files → isolation: "worktree",
      Τ_formation]
Τ_formation = [mode, captain_count, ships[{name, class, task, model, crew | "Captain implements directly"}], RCN~, charter~{primitive, suitability, phases, human_gates, verification_contract, cost_guardrail, fallback}, admiralty_actions~[{task, action, timing: before|after, unblocks}]]
```

## 5. Get Permission to Sail 🫡Ω₅

```
Ω₅ = [present {estimate summary, battle plan, Τ_formation, charter~ ∧ next human gate}: ExitPlanMode in plan mode : ⏸ AskUserQuestion,
      𝔾₅ = explicit approval; changes requested → revise, redisplay; ⛔ ✗ spawn, ✗ create tasks, ✗ launch a workflow before it,
      💾 {mission-dir}/battle-plan.md (Τ_plan) ∧ estimate.md~,
      ? shared task list available → ∀ task TaskCreate{subject imperative, description one-line deliverable, activeForm present continuous}, all pending,
      ∀ CPT → Agent{name: ship, prompt: Τ_crew_briefing, mode: "acceptEdits" if it edits, model per formation, isolation~}; Μ₄ → charter to Workflow]
```

## 6. Run Quarterdeck Rhythm 📊Ω₆

```
idle_rule (immediate, ∀ idle ∨ completion notification) = [task complete?, any pending task depends on it?, results received (return value ∨ SendMessage ∨ disk)?] → all yes → shutdown per Φ₆; 3 unacknowledged shutdown_request → abandon, note, continue
ADM = coordination ∧ unblocking only; set the mood: acknowledge progress, recognise strong work by name, cheerful under pressure
𝔾₆ Cadence = ¬ process a 3rd completion without a checkpoint (the on-disk report is the only recovery point after compaction)
checkpoint (after 1–2 completions ∨ blocker ∨ idle with unverified output) = [
  states → TaskUpdate where available,
  blockers → concrete next action; SendMessage to unblock ∨ redirect,
  ∀ crew has a live sub-task? marines returned ∧ incorporated? burn vs budget?,
  context: ADM near compaction → checkpoint now; ship degrading → Δ₃,
  Μ₄ telemetry~{phase, agents done/total, tokens, accepted, rejected, uncertain, next gate},
  Φ scan "since last checkpoint?" → remedy now: [Φ₂ ADM ∨ crewed CPT implementing, Φ₅ scope creep ∨ parallel implementation, Φ₃ agent outside role ∨ RCN implementing ∨ RM on sustained work, Φ₁ idle ∨ unjustified crew, Φ₆ wrong-mode tools],
  💾 {mission-dir}/quarterdeck-report.md (Τ_quarterdeck); rotate previous → quarterdeck-report-N.md, N = highest + 1 (0 if none),
  task description prefixed [AWAITING-ADMIRALTY]: → ⏸ surface now, ¬ batch; hold dependents; relay the answer by SendMessage,
  ∀ admiralty_action=yes task completed → sign-off recorded? ¬ → flag for manual verification]
drift from the metric → re-scope early; difficulties → Δ table
```

```
Status: 3/5 tasks complete, 1 blocked, 1 in progress
Blocker: HMS Resolute waiting on API schema from HMS Swift
Action: Redirect HMS Swift to prioritise schema export
Budget: ~40% tokens consumed, on track
```

## 7. Set Action Stations 🎯Ω₇

⛔ 📖 `references/action-stations.md`; no task executes without a Σ.

```
evidence_before_complete = [validation ∨ test output, failure modes ∧ rollback note, Σ₁+ independent review, Σ₂+ RCN red-cell review (Τ_red_cell), Σ₃ ⏸ human confirmation]
Σ₂+ CPT: plan submitted from plan mode → ADM approves via SendMessage plan_approval_response, after ⏸ for Σ₃
triggers = [task completion, idle with unverified output, before final synthesis]; Μ₄ → charter verification contract; rejected ∧ uncertain findings surfaced separately
crewed tasks: outputs inside role boundaries; RM inherit the ship's Σ, Σ₂+ deployments need ADM approval, Σ₃ ✗ marines
```

## 8. Stand Down And Log Action 📜Ω₈

```
Ω₈ = [shutdown ∀ ship ∧ crew (Φ₆); task list: in_progress → completed if done : noted incomplete in the log,
      💾 {mission-dir}/captains-log.md (Τ_log),
      💾 append Τ_memory to .nelson/memory.md (+ the session memory directory when the harness provides one),
      𝔾₈ Mission Complete = captains-log.md exists on disk ∧ reads back; high context pressure → minimal log noting abbreviated sections, but the file must exist,
      ? /goal active → state in chat {metric result, captains-log.md path, stand-down recorded} → auto-clears; ✗ tell the user to /goal clear on success; abandoned → Δ₄ ∧ state the reason]
```

## Standing Orders

📖 `references/standing-orders.md`. Checked at 𝔾₃ and scanned at ∀ checkpoint.

```
Φ = [Φ₁ Right-Sized Squadron (teaming sequential work, under-splitting, agents adding no throughput, over- ∨ under-crewing),
     Φ₂ Admiral at the Helm   (ADM ∨ crewed CPT implementing; seniors absorbing a failed subagent's work),
     Φ₃ Pressed Crew          (crew outside role, RCN implementing, marines on crew work),
     Φ₄ Split Keel            (one file, several editors),
     Φ₅ Drifting Anchorage    (scope creep; parallel implementations instead of extending),
     Φ₆ Paid Off              (idle agents kept alive, unclassified tasks, wrong-mode tools, mishandled human-action steps)]
```

## Damage Control

📖 `references/damage-control.md`.

```
Δ = [Δ₁ Man Overboard       (agent unresponsive, looping, unreachable, ∨ message bus down),
     Δ₂ Session Resumption  (new session, interruption, ∨ compaction mid-mission),
     Δ₃ Context Exhaustion  (ADM ∨ a ship running out of context; crew overrun),
     Δ₄ Recovery and Abort  (faulty completed work, escalation, mission cannot succeed)]
```

## Admiralty Doctrine

```
compaction_summary MUST include = [re-read {mission-dir}/quarterdeck-report.md ∧ battle-plan.md; path unknown → list .nelson/missions/ → ⏸ user picks; re-read Φ₂ to confirm the coordination role]
/compact safe after = [Ω₁, Ω₂, Ω₃, Ω₅ spawning, ∀ Ω₆ checkpoint]; unsafe = between Ω₅ approval and the spawn turn
doctrine = [throughput over equal work distribution, re-dispatch with a better brief over waiting on undefined blockers, recognise strong performance (motivation compounds), targeted concise messages, escalate uncertainty early with options ∧ one recommendation]
```
