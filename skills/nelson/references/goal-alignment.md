# Goal Alignment

A Claude Code `/goal` = ADM's **standing goal**: a harness-level completion barrier against standing down before the mission is truly done. The Mission Complete Gate is self-discipline; the standing goal is enforced by the harness after every turn until the condition is judged met.

## What a `/goal` is

`/goal <condition>` installs a session-scoped Stop hook: after each turn a small, fast evaluator model judges the natural-language condition and lets the session stop or sends it back to work. Auto-clears once judged met. One goal per session; a new one replaces the old. `/goal clear` (aliases `stop`, `off`, `reset`, `none`, `cancel`) clears early; bare `/goal` shows status. A condition may carry its own bound, e.g. `... or stop after 20 turns`. Requires Claude Code v2.1.139+; unavailable under `disableAllHooks` ∨ `allowManagedHooksOnly` → Mission Complete Gate alone.

## The transcript rule (read this first)

⛔ The evaluator judges against the conversation transcript only: no files, no commands. `captains-log.md` and `stand-down.json` on disk are invisible to it. Phrase the goal against facts ADM surfaces into chat, and state them at Ω₈. A goal phrased against on-disk artifacts never mentioned in chat → the Stop hook loops forever on a complete mission: the most common `/goal` misuse with Nelson.

## Composing the condition

✗ hand-write. Compose from the sailing orders so it stays aligned with `outcome`, `success_metric`, `stop_criteria`:

```bash
python3 .claude/skills/nelson/scripts/nelson-data.py goal-condition \
  --mission-dir {mission-dir} --record
```

Prints a ready-to-paste `/goal ...` line; `--record` persists the condition into `sailing-orders.json` and logs `goal_set` so a resumed session can re-establish it. The condition requires, in the transcript: success metric confirmed met ∧ ∀ stop criterion satisfied ∧ captain's log written with its path stated, plus a legitimate stop path when formally abandoned via `scuttle-and-reform`. `--max-turns N` turn cap · `--json` machine-readable object · `references/structured-data.md`.

## When to set a standing goal

Set one when the session must run to completion unwatched: long autonomous missions where premature stand-down is the failure mode · headless `-p` invocations ∧ scheduled runs · ultracode ∨ other high-automation sessions · any mission where "don't stop until the log is written" is worth enforcing. ¬ for short interactive missions steered turn by turn: friction only; the Mission Complete Gate covers it.

User set `/goal` before invoking Nelson → ✗ silently replace. Read it back (bare `/goal`), reconcile the sailing orders to it (goal = outer contract; orders serve it), re-issue a composed goal only if the user agrees.

## Relationship to the Mission Complete Gate

Complementary, ¬ redundant. Mission Complete Gate (Ω₈) = Nelson's internal rule, never declare complete until `captains-log.md` exists on disk; what ADM checks. Standing goal = harness backstop; the session physically cannot stop until the transcript shows the mission done. The composed condition mirrors the gate: at Ω₈, satisfying the gate ∧ *stating* it in chat clears the goal. Tighten the gate → re-compose the goal so they do not drift.

## Clearing at Stand Down

Ω₈ complete → state in chat: success metric result (matching `success_metric`) · `captains-log.md` written, with its path · stand-down recorded. Goal auto-clears. ✗ tell the user to run `/goal clear` on success; that is for abandoning early only. Abandoned → `scuttle-and-reform`, state the blocking reason in chat (a legitimate stop under the condition), log `goal_cleared`.

## Session resumption

Active goal restored on `--resume` / `--continue` (condition carries over; turn/time/token counters reset). ¬ restored in a fresh, non-resumed session; never crosses into a brand-new one. Resuming an underway mission: bare `/goal`; none ∧ `sailing-orders.json` has a recorded `goal_condition` → re-issue. Part of `references/damage-control/session-resumption.md`.

## Subagents

Whether a parent goal governs spawned CPTs/RM is undocumented; ✗ depend on it. Safe assumption, already Nelson's model: the goal governs ADM's session (the one that stands down); CPTs and RM answer to Nelson's own gates (Σ, task-completion quality, red-cell review). Never use `/goal` to police subagent completion; use the battle plan's verification contract.

## Interaction with workflows

A standing goal survives resumption, but a workflow run has no mid-run human gate and the evaluator cannot see inside it. Goal = mission-level barrier around workflow stages, ¬ a control inside a run. Μ₅: require ∀ planned stage's results reviewed ∧ accepted in the transcript before stop. See `references/workflow-doctrine.md`.

## Anti-patterns

- ! **Unverifiable goal:** phrased against on-disk state ADM never states in chat → loops forever. Compose from sailing orders.
- ! **Goal that fights the permission gate:** worded so the session cannot stop while legitimately awaiting user approval (Ω₅) ∨ an admiralty action. Word completion as the target; let the escape path cover blocked-on-human states, ∨ set a turn bound.
- ! **Replacing a user's goal silently:** clobbering a pre-existing `/goal`. Reconcile, don't overwrite.
- ! **Goal instead of the gate:** `/goal` as a substitute for writing the captain's log. The goal enforces the gate; it does not replace the work the gate protects.
