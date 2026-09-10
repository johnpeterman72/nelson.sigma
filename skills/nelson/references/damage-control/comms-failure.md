# Δ₁₁ Communications Failure: Agent Team Infrastructure

The channel itself is broken: agent IDs unreachable, `SendMessage` errors, task list inaccessible. Stuck agents still reachable → `man-overboard.md`.

**Symptoms:** `SendMessage` agent-not-found ∨ similar · several ships unreachable at once · `TaskList` ∨ `TaskGet` errors ∨ stale data · completed results unretrievable.

1. ADM records unreachable ships + their tasks.
2. `TaskList` → results written before the failure.
3. ∀ lost ship: recover disk output (files, partial deliverables); record status + outputs in the quarterdeck report.
4. Viability: enough done → redistribute lost tasks to reachable ships ∨ new sub-agents · team infrastructure down → Μ₂ `subagents` for the rest, replacement CPTs briefed with recovered context · cannot continue → `scuttle-and-reform.md`.
5. ⛔ ADM ✗ implement lost ships' work; `admiral-at-the-helm` holds. Spawn replacements.
6. Log failure, ships, recovered outputs, remedy in the quarterdeck report.

**Prevention:** ≥4 ships → intermediate outputs to disk, ¬ bus alone · long tasks → "write checkpoint to disk" in crew briefings · ∀ checkpoint → verify ∀ ship reachable.
