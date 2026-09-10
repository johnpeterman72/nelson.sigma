# Templates

Output shapes, in the order a mission uses them. Labels inside the blocks are literal; text in `[brackets]` is filled in.

## Sailing Orders

Top of `{mission-dir}/battle-plan.md`.

```text
Sailing orders:
- Outcome:
- Success metric:
- Deadline:

Constraints:
- Token/time budget:
- Reliability floor:
- Compliance/safety constraints:
- Forbidden actions:

Scope:
- In scope:
- Out of scope:

Stop criteria:
- Stop when:

Required handoff artifacts:
- Must produce:

Standing goal: [the /goal condition, or "none"]
```

## Battle Plan

`{mission-dir}/battle-plan.md`, after the sailing orders. Prose authority for intent and per-task briefs; owners and status are updated as they change.

```text
Commander's intent:
[One paragraph from Ε₂ — prepended to every captain's brief.]

Estimate: [conducted → see estimate.md | inline → three lines on terrain, forces, control]

Workflow suitability:
[one line; charter fields follow only for Μ₄]

Standing Order Check:
- Φ₁ Right-Sized Squadron: [answer with reasoning]
- Φ₂ Admiral at the Helm: [answer]
- Φ₃ Pressed Crew: [answer]
- Φ₄ Split Keel: [answer]
- Φ₅ Drifting Anchorage: [answer]
- Φ₆ Paid Off: [answer]

Task [ID]:
- Name:
- Owner: [ship, assigned at Ω₄]
- Crew manifest: [roles with sub-tasks and sequence, or "Captain implements directly"]
- Marine capacity: [0-2]
- Deliverable:
- Dependencies:
- Station tier (0-3):
- File ownership: [files, or "worktree"]
- Modification targets: [functions, env vars, config to modify in place — omit for greenfield]
- Acceptance criteria (inherited from the effect):
  - [criterion — captain chooses: test | type-check | lint | review | visual]
- Validation required:
- Rollback note required: yes/no
- admiralty-action-required: yes/no
  - action: [what the human must do]
  - timing: before this task starts | after this task completes
  - blocks: [task name or "stand-down"]

Formation: [paste the SQUADRON FORMATION ORDERS block]
```

- `admiralty-action-required: yes` when only a human can act (external systems, credentials, approvals). A blank field is a claim that no human action is needed.
- Modification targets flow from Ε₁ and Ε₄; where reconnaissance found existing code, the plan preserves that specificity.

## Crew Briefing

The `Agent` prompt for every CPT. Agents inherit none of ADM's conversation, so the brief carries all context. Target ≈500 tokens.

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

## Marine Deployment Brief

The `Agent` prompt a CPT gives a marine.

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

## Quarterdeck Report

`{mission-dir}/quarterdeck-report.md` at every checkpoint; the previous one rotates to `quarterdeck-report-N.md`.

```text
Mission directory:
Checkpoint time:

Progress:
- pending:
- in_progress:
- completed:

Blockers:
- blocker:
  owner:
  next action:
  eta:

Budget:
- token/time spent:
- token/time remaining:

Context pressure:
- admiral: [none / approaching compaction — report written]
- ships: [none / ship — relief planned]

Standing order violations:
- order: (none / each triggered since last checkpoint)
  corrective action taken:

Risk updates:
- new/changed risks:
- mitigation:

Workflow telemetry (Μ₄ only):
- phase / agents done of total / tokens / accepted / rejected / uncertain / next gate:

Signal flag (if any):
- recognition:

Admiral decision:
- continue / rescope / stop:
- rationale:
```

## Relief Brief

Written by a ship before handover (Δ₁, Δ₃) to `{mission-dir}/relief-{ship}-{HHMM}.md`; the replacement reads it first.

```text
== RELIEF BRIEF ==
Ship / Role / Time / Reason for relief:
Task: [ID, name, deliverable, station tier, file ownership, dependencies]
Progress log:
- [completed item — specific: files, functions, tests]
Running plot (in flight when relieved):
- [what was being worked on, how far, where partial outputs are]
Files touched:
- [path — change]
Key decisions:
- [decision — rationale]
Hazards and blockers:
- [item — status]
Recommended course of action:
- [first thing the replacement should do; what to avoid]
Relief chain:
- [previous ship | time on station | key accomplishment | reason]
== END RELIEF BRIEF ==
```

Specific progress lines beat summaries; the running plot is what the replacement needs most. Chain lines are one per predecessor, never pasted briefs.

## Red-Cell Review

```text
Target task/artifact:

Challenge summary:
- Primary assumption being tested:
- Likely failure mode:
- Blast radius if wrong:

Checks run:
- check:
- result:

Recommendation:
- approve / revise / block:
- required changes:
```

## Captain's Log

`{mission-dir}/captains-log.md` at Ω₈. Its presence marks the mission complete.

```text
Mission summary:
- planned outcome:
- achieved outcome:
- success metric result:

Delivered artifacts:
- artifact:
- location:

Key decisions:
- decision:
- rationale:

Validation evidence:
- criterion:
  method:
  result: pass / fail / not-verified

Open risks:
- risk:
- owner:
- mitigation/next step:

Follow-ups:
- item:
- owner:
- due date:

Mentioned in Despatches:
- agent:
- contribution:

Reusable patterns:
- adopt:
- avoid:
```

Mentioned in Despatches names agents whose work was exemplary: proactive risk mitigation, output needing minimal rework, initiative beyond scope, coordination that unblocked others. Praise elsewhere is specific and brief ("Well fought clearing that blocker ahead of schedule"), never generic.

## Memory Entry

Appended to `.nelson/memory.md` at Ω₈.

```text
## [date] — [mission slug]
- adopt: [pattern]
- avoid: [pattern]
- recurring Φ: [order — what triggered it]
```
