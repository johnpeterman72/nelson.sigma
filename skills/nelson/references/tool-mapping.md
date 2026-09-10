# Tool Mapping Reference

Nelson operations → Claude Code tool calls, by Μ.

## Tool Reference

| Nelson Operation | Claude Code Tool | Μ |
|---|---|---|
| Form the squadron | `TeamCreate` | Μ₃ |
| Spawn CPT | `Agent` with `team_name` + `name` | Μ₃ |
| Spawn CPT | `Agent` with `subagent_type` | Μ₂ |
| Charter dynamic workflow | battle-plan Workflow Charter prompt | Μ₄ / Μ₅ |
| Launch workflow stage | Claude Code workflow run from approved charter | Μ₄ / Μ₅ |
| Record workflow telemetry | `ND event --type workflow_* ...` | Μ₄ / Μ₅ |
| Compose a standing-goal condition | `ND goal-condition --mission-dir ...` | ∀ Μ |
| Set the standing goal | `/goal <condition>` (Stop hook) | ∀ Μ |
| Check / clear the standing goal | `/goal` / `/goal clear` | ∀ Μ |
| Create task (coordination) | `TaskCreate` | Μ₃ |
| Assign task to CPT | `TaskUpdate` with `owner` | Μ₃ |
| Check task progress (coordination) | `TaskList` / `TaskGet` | Μ₃ |
| Track task visibility (ADM) | `TaskCreate` / `TaskUpdate` / `TaskList` | ∀ Μ ¹ |
| Message a CPT | `SendMessage(type="message")` | Μ₃ |
| Broadcast to squadron | `SendMessage(type="broadcast")` | Μ₃ |
| Shut down a ship | `SendMessage(type="shutdown_request")` | Μ₃ / Μ₂ |
| Respond to shutdown | `SendMessage(type="shutdown_response")` | Μ₃ |
| Deploy RM | `Agent` with `subagent_type` | ∀ Μ |
| Approve CPT's plan | `SendMessage(type="plan_approval_response")` | Μ₃ |
| Stand down squadron | `TeamDelete` | Μ₃ |

## Mode Differences

- **Μ₂ `subagents`:** no shared task list. ADM tracks state directly; CPTs report only to ADM. Spawn CPTs with `Agent`.
    - available: `Agent` with `subagent_type`, `SendMessage(type="shutdown_request")`
    - ✗ for CPTs: `TaskCreate`, `TaskList`, `TaskGet`, `TaskUpdate`, `SendMessage(type="message")`, `SendMessage(type="broadcast")`, `TeamCreate`, `TeamDelete`
    - ADM exception: `TaskCreate`/`TaskUpdate`/`TaskList` for session-level visibility (the user's Ctrl+T task list). Invisible to CPTs; for the user's benefit only ¹
- **Μ₃ `agent-team`:** the task list (`TaskCreate`, `TaskList`, `TaskGet`, `TaskUpdate`) is the shared coordination surface; CPTs message each other via `SendMessage`. `TeamCreate` first → `Agent` with `team_name` and `name`.
    - available: `TeamCreate`, `TeamDelete`, `Agent` with `team_name` + `name`, all `Task*` tools, all `SendMessage` types
    - ✗ `Agent` with `subagent_type` for CPTs (RM still use `subagent_type`)
- **Μ₁ `single-session`:** no spawning; ADM executes all work directly.
    - available: `TaskCreate`, `TaskUpdate`, `TaskList`, `TaskGet` (visibility tracking) ¹
    - ✗ `Agent`, `TeamCreate`, `TeamDelete`, `SendMessage`
- **Μ₄ `workflow`:** one approved autonomous dynamic workflow run. Nelson v1 does not call a workflow API or write runnable workflow scripts; it produces the Workflow Charter and verification contract Claude Code uses to create or run the workflow. Track as a fleet asset; log `workflow_charter_created`, `workflow_run_started`, and `workflow_run_completed` / `workflow_run_stopped` as appropriate.
    - available: Workflow Charter, Claude Code workflow run, loose telemetry via `ND event`
    - ✗ mid-run Nelson approval gates → stop the run and use `hybrid-workflow` when approval is needed before continuing
- **Μ₅ `hybrid-workflow`:** a sequence of separately approved workflow stages, for Σ₂/Σ₃ work, Sounding-the-Channel probes, or any mission needing human sign-off between stages. ∀ stage = its own workflow run with Nelson review before the next launches.
    - available: same workflow primitives as `workflow` + Nelson permission gates between stages
    - ✗ arbitrary mid-run human input inside a workflow stage

¹ Visibility tracking uses the same task tools as Μ₃ coordination but serves a different purpose: mission progress in the user's Ctrl+T task list. Μ₂|Μ₁: only ADM calls these tools; CPTs never see or interact with the entries.

## Dynamic Workflow Notes

Workflow-spawned agents run in `acceptEdits` mode and inherit the session tool allowlist; shell, web, or MCP calls outside it may still prompt. → the charter MUST state expected tool needs up front, and ADM ✗ assume a workflow bypasses permission gates.

Reusable workflow: `.claude/workflows/<name>.js` (project) ∨ `~/.claude/workflows/<name>.js` (personal), re-run as the `/<name>` command; watch and pause/resume runs from the `/workflows` view. Nelson's charter is what you hand to that mechanism. Charter-to-script bridge, Sounding-the-Channel probes, verification contracts, cost guardrails, telemetry, damage-control mapping: `workflow-doctrine.md`.

## Standing Goal Notes

`/goal <condition>` = ADM-level Stop hook, ¬ a per-agent tool: set once per session, never inside a CPT or workflow run. Its evaluator judges the condition against the **conversation transcript only**; it reads no files and runs no commands. Nelson's completion evidence lives on disk → compose the condition with `ND goal-condition` (words it against transcript-visible facts), ¬ by hand, and surface that evidence into chat at Ω₈. Full doctrine (availability, resumption, subagent scope, anti-patterns): `references/goal-alignment.md`.

## Anti-Patterns

Full Φ: `references/standing-orders/wrong-ensign.md`.

| Anti-Pattern | Why It Fails | Correct Alternative |
|---|---|---|
| `TaskGet` in Μ₂ | no shared task list exists | read the `Agent` return value directly |
| `SendMessage(type="message")` in Μ₂ | no team exists to route messages | put instructions in the `Agent` prompt |
| `Agent` with `subagent_type` to spawn a CPT in Μ₃ | agent not registered as a teammate | `Agent` with `team_name` + `name` |
| `TeamCreate` in Μ₂ | unnecessary team structure | omit; spawn CPTs directly with `Agent` |
| `TaskCreate` by CPTs in Μ₂ | no shared task list exists for CPTs | ADM tracks visibility via `TaskCreate`/`TaskUpdate` in its own session; CPTs report via the `Agent` return value |
| treating a workflow stage as an Μ₃ squadron | workflows are scripted runs, ¬ peer-messaging teams | track as a fleet asset with a Workflow Charter and telemetry |
| expecting human input inside a workflow run | dynamic workflows provide no arbitrary mid-run Nelson gates | `hybrid-workflow`; require approval between separate workflow runs |
| assuming Nelson v1 invokes workflow APIs directly | v1 ships doctrine and charters, ¬ a workflow compiler | give Claude Code the approved charter/prompt to create or run the workflow |
| hand-writing a `/goal` against on-disk artifacts | the evaluator sees only the transcript, never observes them; the Stop hook loops forever | compose with `ND goal-condition`; state completion evidence in chat |
| setting a `/goal` inside a CPT or workflow run | the goal is a session-scoped ADM backstop, ¬ a per-agent control | set once at Ω₁; govern subagents with the verification contract |
