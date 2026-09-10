# Templates

Output shapes as field arrays, in the order a mission uses them. Written to disk as `Field: value` lines in array order, repeated groups as bullets; labels are literal, `[brackets]` are filled. The two briefs that agents receive as prompts stay in plain English, because they read them without the legend.

## Τ_orders — Sailing Orders

Top of `{mission-dir}/battle-plan.md`.

```
Τ_orders = [outcome, success_metric, deadline,
            constraints{token/time budget, reliability floor, compliance/safety, forbidden actions},
            scope{in, out}, stop_criteria[], handoff_artifacts[], standing_goal | "none"]
```

## Τ_plan — Battle Plan

`{mission-dir}/battle-plan.md` after the orders. Prose authority for intent and briefs; owners and status updated as they change.

```
Τ_plan = [commander_intent (one paragraph from Ε₂, prepended to every captain's brief),
          estimate: "see estimate.md" | inline{terrain, forces, control},
          workflow_suitability (one line; charter fields only for Μ₄),
          Φ_check[Φ₁, Φ₂, Φ₃, Φ₄, Φ₅, Φ₆ — each answered with reasoning],
          tasks[Τ_task],
          formation (Τ_formation)]
Τ_task = [id, name, owner (ship, at Ω₄), crew_manifest[{role, sub-task, sequence}] | "Captain implements directly", marine_capacity 0-2,
          deliverable, dependencies[], station_tier 0-3, file_ownership[] | "worktree", modification_targets~[] (functions, env vars, config to modify in place; omit for greenfield),
          acceptance_criteria[{criterion, method: test|type-check|lint|review|visual}], validation_required, rollback_note yes|no,
          admiralty_action{yes|no, action~ (what the human must do), timing~: before|after, blocks~: task | "stand-down"}]
```

- `admiralty_action: yes` when only a human can act (external systems, credentials, approvals); a blank field claims none is needed.
- `modification_targets` flow from Ε₁ and Ε₄: where reconnaissance found existing code, the plan preserves that specificity.

## Τ_crew_briefing — Crew Briefing

The `Agent` prompt for every CPT; agents inherit none of ADM's conversation. Target ≈500 tokens. Verbatim:

```text
== CREW BRIEFING ==
[If assigned haiku: insert the three haiku blocks from references/squadron.md first.]
Mission: [name from sailing orders]
Commander's intent: [paragraph from the battle plan]
Your Role: Captain [N] — [role description]
Ship: [ship name]
Your Task: [task from the battle plan]
Deliverable: [what you must produce]
Acceptance criteria: [inherited list; you choose the verification method per criterion]
Action Station: [0-3] — [Patrol / Caution / Action / Trafalgar]
File Ownership: [files you own — no other agent edits these | "worktree — you have an isolated checkout"]
Dependencies: [tasks that must finish first / tasks waiting on yours | "Independent"]
Mission Directory: [{mission-dir} absolute path]
Marine Capacity: [0-2 — omit if 0]
Execution mode: [single-session | subagents | agent-team | workflow]
Standing Orders:
- Do NOT implement work outside your assigned task scope
- Do NOT edit files not assigned to you
- If any part of your task is ambiguous, signal the admiral before implementing
- When extending existing code, modify it in place. Do NOT create replacement functions,
  parallel implementations, or new environment variables that duplicate existing ones;
  if a rewrite seems necessary, signal the admiral with your rationale first
- Report blockers immediately with options and one recommendation
- When done, report: deliverable, validation evidence per acceptance criterion, failure
  modes, rollback note
- You may deploy Royal Marines (short-lived sub-agents) for focused sorties via the Agent
  tool: Recce Marine = subagent_type "Explore" (read-only); Assault Marine / Sapper =
  "general-purpose". Max 2 at a time; marines cannot deploy marines; Station 2+ sorties
  need admiral approval first; never use marines instead of crew for sustained work
- To muster or pay off crew mid-task, request admiral approval with a brief rationale
- At a step needing human action (admiralty-action-required: yes): finish all autonomous
  work, save artifacts, prefix your task description with [AWAITING-ADMIRALTY]:, report the
  exact ask, and wait for the admiral's message. Do not continue, skip, or substitute
- Shutdown protocol: on {"type": "shutdown_request"} reply {"type": "shutdown_response"}
  and cease all activity
== END BRIEFING ==
```

## Τ_marine_brief — Marine Deployment Brief

The `Agent` prompt a CPT gives a marine. Verbatim:

```text
== MARINE DEPLOYMENT BRIEF ==
Ship: [parent ship]
Detachment: [Recce Marine / Assault Marine / Sapper]
Model: [assigned model; haiku → add the three haiku blocks and tasking discipline]
Objective: [single clear sentence]
Scope: [what to do, and explicitly what NOT to do]
Report back: [findings or outputs to return]
Constraints:
- Do NOT modify files outside objective scope
- Do NOT spawn sub-agents
- Report to the captain; do not act beyond the objective
== END BRIEF ==
```

## Τ_quarterdeck — Quarterdeck Report

`{mission-dir}/quarterdeck-report.md` at every checkpoint; the previous one rotates to `quarterdeck-report-N.md`.

```
Τ_quarterdeck = [mission_dir, checkpoint_time,
                 progress{pending[], in_progress[], completed[]},
                 blockers[{blocker, owner, next_action, eta}],
                 budget{spent, remaining},
                 context_pressure{admiral: none | "approaching compaction — report written", ships[]: none | "ship — relief planned"},
                 Φ_violations[{order, corrective_action}] | none,
                 risks[{new_or_changed, mitigation}],
                 telemetry~ (Μ₄){phase, agents done/total, tokens, accepted, rejected, uncertain, next_gate},
                 signal_flag~ (recognition),
                 decision{continue|rescope|stop, rationale}]
```

## Τ_relief — Relief Brief

Written by a ship before handover (Δ₁, Δ₃) to `{mission-dir}/relief-{ship}-{HHMM}.md`; the replacement reads it first.

```
Τ_relief = [ship, role, time, reason_for_relief,
            task{id, name, deliverable, station_tier, file_ownership, dependencies},
            progress_log[] (specific: files, functions, tests),
            running_plot[] (in flight when relieved: what, how far, where partial outputs are),
            files_touched[{path, change}],
            key_decisions[{decision, rationale}],
            hazards_and_blockers[{item, status}],
            recommended_course[] (first thing to do; what to avoid),
            relief_chain[{previous ship, time on station, key accomplishment, reason}]]
```

Specific progress lines beat summaries; the running plot is what the replacement needs most. Chain entries are one line per predecessor, never pasted briefs.

## Τ_red_cell — Red-Cell Review

```
Τ_red_cell = [target, challenge{primary_assumption, likely_failure_mode, blast_radius_if_wrong},
              checks[{check, result}], recommendation{approve|revise|block, required_changes[]}]
```

## Τ_log — Captain's Log

`{mission-dir}/captains-log.md` at Ω₈; its presence marks the mission complete.

```
Τ_log = [summary{planned_outcome, achieved_outcome, success_metric_result},
         artifacts[{artifact, location}],
         key_decisions[{decision, rationale}],
         validation_evidence[{criterion, method, result: pass|fail|not-verified}],
         open_risks[{risk, owner, mitigation}],
         follow_ups[{item, owner, due}],
         mentioned_in_despatches[{agent, contribution}],
         reusable_patterns{adopt[], avoid[]}]
```

Despatches name agents whose work was exemplary: proactive risk mitigation, minimal rework, initiative beyond scope, coordination that unblocked others. Praise elsewhere is specific and brief ("Well fought clearing that blocker ahead of schedule"), never generic.

## Τ_memory — Memory Entry

Appended to `.nelson/memory.md` at Ω₈.

```
Τ_memory = ["## {date} — {mission slug}", adopt[], avoid[], recurring_Φ[{order, trigger}]]
```
