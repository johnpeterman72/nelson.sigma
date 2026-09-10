# Tool Mapping

Nelson operations → native Claude Code tools. Nothing here needs a script, a hook, or an environment variable except where stated.

## Reference

| Operation | Tool | Μ |
|---|---|---|
| Scout terrain (Ε₁) | `Agent` with `subagent_type: "Explore"` | ∀ |
| Plan read-only (Ω₂–Ω₅) | `EnterPlanMode` → work → `ExitPlanMode` (the approval) | ∀ |
| Ask a gated question | `AskUserQuestion` | ∀ |
| Spawn a CPT | `Agent` with `name`, `subagent_type`, `model` (~), `mode: "acceptEdits"`, `isolation: "worktree"` (~) | Μ₂ Μ₃ |
| Spawn a Σ₂+ CPT | same with `mode: "plan"`; approve via `SendMessage` `plan_approval_response` | Μ₂ Μ₃ |
| Deploy an RM (by a CPT) | `Agent` with `subagent_type` | ∀ |
| Message a ship | `SendMessage` to its `name` | Μ₃; Μ₂ only to re-brief |
| See who is on station | `ListAgents` | Μ₂ Μ₃ |
| Shut down a ship | `SendMessage` `{"type": "shutdown_request"}`; the ship replies `shutdown_response` | Μ₂ Μ₃ |
| Track tasks | shared task list (`TaskCreate`, `TaskUpdate`, `TaskList`) when `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1`; otherwise `quarterdeck-report.md` | ∀ |
| Launch a workflow | `Workflow` after loading the `workflow-authoring` skill | Μ₄ |
| Standing goal | `/goal <condition>` · bare `/goal` shows it · `/goal clear` abandons | ∀ |
| Compaction | `/compact` at phase boundaries only | ∀ |
| Resume a session | `--resume` ∨ `--continue`, then Δ₂ | ∀ |
| Wait on long work | completion and idle notifications arrive on their own; `Monitor` for external state; ✗ polling | ∀ |
| Cross-mission memory | `.nelson/memory.md`; also the session memory directory when the harness provides one | ∀ |

## Agent Parameters

- `name` — the ship in lowercase (`hms-daring`); the address for `SendMessage`. Recent builds run every named agent in one implicit team; `team_name` is ignored.
- `subagent_type` — `general-purpose` for implementers; `Explore` for read-only research (NO, COX, Recce RM); a project agent by its name.
- `model` — omit to inherit ADM's model; `"haiku"` for weight ≤4 under cost-savings (`references/squadron.md`).
- `mode` — `"acceptEdits"` ∀ agent that edits files; omitting it can stall the agent silently at its first edit. `"plan"` for Σ₂+ CPTs: read-only until ADM approves the plan.
- `isolation: "worktree"` — when file ownership overlaps; ADM merges afterwards.
- Agents run in the background and notify on completion and idle. Their prompt is their entire context → the crew briefing must be self-contained (`references/templates.md`).

## Mode Differences

- **Μ₁ `single-session`:** no spawning; ADM does the work in order and keeps the task list or quarterdeck report current.
- **Μ₂ `subagents`:** CPTs cannot see each other or the task list; they return results through the `Agent` completion. ADM re-briefs by `SendMessage` only when needed. ✗ CPT-to-CPT messaging, ✗ expecting CPTs to update shared tasks.
- **Μ₃ `agent-team`:** CPTs message each other by `SendMessage` and share the task list when the env var is set; results still confirmed by ADM before shutdown. ✗ spawning CPTs without a `name`.
- **Μ₄ `workflow`:** one scripted run is a fleet asset, ¬ a set of CPTs. ✗ launching without the user's opt-in; ✗ expecting a human gate inside a run.

## Workflows (Μ₄)

- **Opt-in rule:** run `Workflow` only when the user asked for one in their own words ("use a workflow", "ultracode", "fan out agents") or a session flag says so. Otherwise describe what a workflow would do and roughly cost, and ask.
- **Suitability:** codebase-wide audits, one transformation repeated across many independent targets, cross-checked research, repeatable verification sweeps, broad triage. Prefer Μ₃ for tightly coupled work, frequent human steering, or vague acceptance criteria.
- **Charter** (in the formation orders): execution primitive · suitability · phases · human gates · verification contract · cost guardrail · fallback mode (usually Μ₃).
- **Sounding the Channel:** probe one representative slice first (one package, a handful of files, one pattern). Report slice, agents done/total, elapsed, token burn, accepted/rejected/uncertain findings, charter changes needed. ⛔ full run only after ADM reviews the probe.
- **Verification contract:** completion ≠ acceptance. Findings above a risk threshold need an independent verify stage; edits need tests, lint, or review; rejected and uncertain findings are surfaced separately, never folded into the summary.
- **Σ₂+ work:** Σ₂ needs red-cell review and Σ₃ human confirmation, which a run cannot pause for → split into separate runs with approval between them.
- **Cost controls:** probe first · cap targets per phase and agents per wave · stop after repeated agent failures · narrow or fall back on low signal, duplicate findings, or excessive cost.
- **Telemetry** in each quarterdeck report: workflow name, phase, agents done/total, failed agents, elapsed, token burn, findings accepted/rejected/uncertain, next gate.
- Reusable scripts live at `.claude/workflows/<name>.js` (project) or `~/.claude/workflows/<name>.js` (personal) and re-run as `/<name>`. Workflow-spawned agents run in `acceptEdits` and inherit the session tool allowlist; state expected tool needs in the charter.

## Standing Goal

`/goal <condition>` installs a session-scoped stop hook: after each turn an evaluator judges the condition against the **conversation transcript only** (no files, no commands) and either lets the session stop or sends it back to work. It auto-clears once met.

- **Transcript rule:** ⛔ phrase the goal against facts ADM will state in chat, never against files on disk alone; otherwise the session loops forever on a finished mission.
- **Condition shape:** `/goal Mission <slug> complete: success metric "<metric>" confirmed met in chat, every stop criterion satisfied, and captains-log.md written under .nelson/missions with its path stated. Legitimate stop: mission formally abandoned with the reason stated. Or stop after <N> turns.`
- **When:** long autonomous, headless `-p`, scheduled, or ultracode sessions. ¬ for short interactive missions.
- **User's own goal:** ✗ replace silently; read it back, reconcile the sailing orders to it, re-issue only with agreement.
- **Ω₈:** state metric result, log path, stand-down in chat → auto-clears. ✗ tell the user to `/goal clear` on success. Abandoned → Δ₄ and state the reason.
- **Resumption:** restored on `--resume`/`--continue`, ¬ in a fresh session; re-issue from `battle-plan.md` if the mission is still underway.
- **Subagents** are not governed by the goal; they answer to Σ and the verification contract.

## Anti-Patterns

| ! | Why it fails | Instead |
|---|---|---|
| CPT-to-CPT `SendMessage` in Μ₂ | no coordination surface was planned | choose Μ₃, or route through ADM |
| spawning a CPT without `name` | unaddressable; cannot be messaged or shut down | always name the ship |
| omitting `mode: "acceptEdits"` on an editing agent | permission race stalls the agent silently | set it whenever the task edits files |
| expecting CPTs to see ADM's task entries in Μ₂ | entries are ADM-side visibility only | put instructions in the brief |
| a human gate inside a workflow run | runs cannot pause for Nelson | separate approved runs |
| launching `Workflow` without opt-in | policy: the user must ask | describe, cost, ask |
| `/goal` phrased against on-disk artifacts | evaluator never sees them | state the evidence in chat |
| setting `/goal` inside a CPT or run | session-level backstop only | set once at Ω₁ |
| polling `ListAgents` or re-sending "are you done?" | notifications arrive on their own | wait; act on the idle rule |
