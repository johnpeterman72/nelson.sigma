# Tool Mapping

Nelson operations → native Claude Code tools. Nothing here needs a script, a hook, or an environment variable except where stated.

## Reference

```
op(tool, Μ) =
  [scout terrain Ε₁      (Agent subagent_type: "Explore", ∀),
   plan read-only Ω₂–Ω₅  (EnterPlanMode → work → ExitPlanMode = the approval, ∀),
   gated question        (AskUserQuestion, ∀),
   spawn CPT             (Agent{name, subagent_type, model~, mode: "acceptEdits", isolation: "worktree"~}, Μ₂ Μ₃),
   spawn Σ₂+ CPT         (same + mode: "plan"; approve via SendMessage plan_approval_response, Μ₂ Μ₃),
   deploy RM (by CPT)    (Agent subagent_type, ∀),
   message a ship        (SendMessage to its name, Μ₃; Μ₂ only to re-brief),
   who is on station     (ListAgents, Μ₂ Μ₃),
   shut down a ship      (SendMessage {"type": "shutdown_request"} → ship replies shutdown_response, Μ₂ Μ₃),
   track tasks           (TaskCreate | TaskUpdate | TaskList when CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 : quarterdeck-report.md, ∀),
   launch a workflow     (Workflow, after loading the workflow-authoring skill, Μ₄),
   standing goal         (/goal <condition> · bare /goal shows · /goal clear abandons, ∀),
   compaction            (/compact at phase boundaries only, ∀),
   resume                (--resume ∨ --continue → Δ₂, ∀),
   wait on long work     (completion ∧ idle notifications arrive on their own; Monitor for external state; ✗ polling, ∀),
   cross-mission memory  (.nelson/memory.md + the session memory directory when provided, ∀)]
```

## Agent Parameters

```
Agent = {name: ship in lowercase (hms-daring) — the SendMessage address; team_name ignored in recent builds (one implicit team),
         subagent_type: general-purpose (implementers) | Explore (read-only: NO, COX, Recce RM) | a project agent by name,
         model: omit → inherit ADM's | "haiku" for weight ≤4 under cost-savings (references/squadron.md),
         mode: "acceptEdits" ∀ agent that edits (omitting it can stall the agent silently at its first edit) | "plan" for Σ₂+ CPTs,
         isolation: "worktree" when file ownership overlaps (ADM merges afterwards)}
agents run in the background ∧ notify on completion ∧ idle; the prompt is their entire context → Τ_crew_briefing must be self-contained
```

## Mode Differences

```
Μ₁ = {no spawning; ADM works in order; keeps the task list ∨ quarterdeck report current}
Μ₂ = {CPTs see neither each other nor the task list; results return via Agent completion; ADM re-briefs by SendMessage only when needed;
      ✗ CPT-to-CPT messaging, ✗ expecting CPTs to update shared tasks}
Μ₃ = {CPTs message each other by SendMessage ∧ share the task list when enabled; ADM still confirms results before shutdown; ✗ unnamed CPTs}
Μ₄ = {one scripted run = a fleet asset, ¬ CPTs; ✗ launching without user opt-in; ✗ expecting a human gate inside a run}
```

## Workflows (Μ₄)

```
opt_in   = run Workflow only when the user asked in their own words ("use a workflow", "ultracode", "fan out agents") ∨ a session flag says so : describe what it would do ∧ roughly cost → ask
suitable = [codebase-wide audits, one transformation across many independent targets, cross-checked research, repeatable verification sweeps, broad triage]
prefer Μ₃ = [tightly coupled work, frequent human steering, vague acceptance criteria]
charter  = [execution_primitive, suitability, phases, human_gates, verification_contract, cost_guardrail, fallback (usually Μ₃)]
probe (Sounding the Channel) = one representative slice first → report{slice, agents done/total, elapsed, tokens, accepted, rejected, uncertain, charter changes}; ⛔ full run only after ADM reviews it
verification_contract = [completion ≠ acceptance; findings above a risk threshold → independent verify stage; edits → tests ∨ lint ∨ review; rejected ∧ uncertain surfaced separately, never folded into the summary]
Σ₂+ = split into separate runs with approval between (a run cannot pause for red-cell review ∨ human confirmation)
cost = [probe first, cap targets per phase ∧ agents per wave, stop after repeated agent failures, narrow ∨ fall back on low signal ∨ duplicate findings ∨ excessive cost]
telemetry (∀ quarterdeck report) = {workflow, phase, agents done/total, failed, elapsed, tokens, accepted, rejected, uncertain, next_gate}
scripts live at .claude/workflows/<name>.js (project) ∨ ~/.claude/workflows/<name>.js (personal), re-run as /<name>; workflow agents run in acceptEdits ∧ inherit the session tool allowlist → state tool needs in the charter
```

## Standing Goal

`/goal <condition>` installs a session-scoped stop hook: after each turn an evaluator judges the condition against the **conversation transcript only** (no files, no commands) and either lets the session stop or sends it back to work. It auto-clears once met.

```
transcript_rule = ⛔ phrase against facts ADM will state in chat, never against files alone (else the session loops forever on a finished mission)
condition = /goal Mission <slug> complete: success metric "<metric>" confirmed met in chat, every stop criterion satisfied, and captains-log.md written under .nelson/missions with its path stated. Legitimate stop: mission formally abandoned with the reason stated. Or stop after <N> turns.
when      = [long autonomous, headless -p, scheduled, ultracode]; ¬ short interactive missions
rules     = [user's own goal → ✗ replace silently: read back, reconcile, re-issue only with agreement,
             Ω₈ → state {metric result, log path, stand-down} in chat → auto-clears; ✗ tell the user to /goal clear on success; abandoned → Δ₄ ∧ state the reason,
             restored on --resume ∨ --continue, ¬ in a fresh session → re-issue from battle-plan.md if still underway,
             subagents are not governed by it; they answer to Σ ∧ the verification contract]
```

## Anti-Patterns

```
! = [CPT-to-CPT SendMessage in Μ₂        (no surface planned → choose Μ₃ ∨ route through ADM),
     spawning a CPT without name          (unaddressable → always name the ship),
     omitting mode: "acceptEdits" on an editor (silent stall → set it whenever the task edits files),
     expecting CPTs to see ADM's task entries in Μ₂ (visibility only → put instructions in the brief),
     a human gate inside a workflow run   (runs cannot pause → separate approved runs),
     launching Workflow without opt-in    (policy → describe, cost, ask),
     /goal phrased against on-disk artifacts (evaluator never sees them → state evidence in chat),
     setting /goal inside a CPT ∨ run     (session-level backstop → set once at Ω₁),
     polling ListAgents ∨ "are you done?" (notifications arrive → wait; act on the idle rule)]
```
