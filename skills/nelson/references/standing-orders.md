# Standing Orders

Six anti-patterns, checked in writing at the Ω₃ gate and scanned at ∀ Ω₆ checkpoint. Correction is graduated: first occurrence → a signal citing the order · repeated ∨ moderate impact → apply the remedy and log it in the quarterdeck report · severe ∨ remedy failed → Δ. An uncorrected violation ⛔ blocks progress until resolved.

## Φ₁ Right-Sized Squadron

! Teaming sequential work · under-splitting independent work · adding agents that shorten nothing · crewing every role · one crew member for atomic work.

**Symptoms:** CPTs idle on a single predecessor · sections sharing no files bundled onto one CPT · more CPTs, mission no sooner · coordination messages up, throughput flat · a ship musters four crew for PWO work · crew invent busywork · a CPT spawns one PWO for a single-pass task.

**Remedy:** form a squadron only when ≥2 tasks run concurrently; otherwise Μ₁. CPT count = independent work units (zero shared state), bounded by the cap. Before adding an agent, name the critical-path task it parallelises; none → ✗. Crew only roles with a concrete sub-task; start from a PWO. Atomic task → CPT implements directly. Sizing rules: `references/squadron.md`.

## Φ₂ Admiral at the Helm

ADM ✗ implementation (writing code, editing files, running tests). CPT ✗ implementation while crew are mustered. Senior ✗ absorbing a failed subordinate's work.

**Synthesis boundary:** coordination = orders, tracking, unblocking, checkpoints. Read-only recombination of completed ship reports already in context, with no new analysis or code, is permitted once ∀ ships have reported successfully with no open blockers. ✗ dispatching a sub-agent just to concatenate text already in context. ADM writes the captain's log and other coordination artifacts.

**Symptoms:** ADM or a crewed CPT writes code · ships idle awaiting direction while the senior is heads-down · checkpoints missed · blockers accumulate · "I'll just do this directly, faster" after a subagent fails · context spikes with raw inputs a subagent should have summarised · the same dispatch never re-attempted with a fixed brief.

**Remedy:** stop; delegate. Subagent failed (error, prompt-length limit, malformed or thin output, timeout) → diagnose the brief (too broad, ambiguous output format, wrong agent type, tool unavailable) → re-brief (split into narrow dispatches, tighten scope, require structured summaries) → re-dispatch. Two failed re-briefs on one task → ⏸ escalate; ✗ absorb on the third attempt.

## Φ₃ Pressed Crew

! Any agent working outside its role: crew doing another role's work · RCN given implementation · marines used for crew work or sustained tasks.

**Symptoms:** NO asked to write code · PWO asked to run tests instead of MEO · a 🔒 role expected to modify files · RCN assigned tickets, quality challenges vanish from reports, verification evidence comes from the author · RM deployed constantly, objectives growing past single sorties, more marines than crew.

**Remedy:** assign sub-tasks matching the role table in `references/squadron.md`; no crewed role fits → crew the right one or the CPT handles it. RCN on review, challenge, and validation only, ever. RM for focused sorties only; sustained independent work → request a new ship from ADM; work that subdivides the deliverable → crew.

## Φ₄ Split Keel

! One file assigned to several editors.

**Symptoms:** CPTs overwrite each other · repeated merge conflicts on one artifact · ADM reconciling divergent edits.

**Remedy:** exclusive file ownership in the battle plan. Two tasks must touch one file → serialise them, split the file into modules first, or give each CPT `isolation: "worktree"` and let ADM merge (`references/squadron.md`).

## Φ₅ Drifting Anchorage

! Scope expanding beyond the sailing orders without re-scoping; replacing where the orders said extend.

**Symptoms:** features or refactors not in the plan · the metric disconnected from active work · budgets overrun without matching progress · new functions, files, or env vars duplicating existing ones · existing code deprecated, bypassed, or shadowed instead of modified.

**Remedy:** at the checkpoint, re-scope ∨ split the task; work outside the orders → defer ∨ add explicitly with ADM approval. Parallel implementation → remove the duplicate, modify the existing code in place; already completed → partial rollback (Δ₄) and re-task with explicit modification targets.

## Φ₆ Paid Off

Stand down agents the moment they have no remaining work in the task graph. Also: no task executes without a Σ tier; no tool from the wrong Μ; human-action steps handled by convention.

**Symptoms:** an agent idles after completion while others continue · agents held "just in case" with no re-task trigger in the sailing orders · tasks proceed with no stated tier or controls · `SendMessage` to an unnamed ship, CPT-to-CPT messaging in Μ₂, a human gate expected inside a workflow run.

**Remedy:** task confirmed complete → is the agent a prerequisite for any pending task, or does a written re-task trigger exist? ¬ → confirm results are received (return value, `SendMessage`, or files on disk) → `shutdown_request`. "We might need them later" is not a trigger. Classify ∀ task per `references/action-stations.md` before execution. Check planned tools against `references/tool-mapping.md`.

**Awaiting Admiralty:** a CPT reaching a planned human-action step (`admiralty-action-required: yes`) finishes all autonomous work, 💾 artifacts, prefixes its task description with `[AWAITING-ADMIRALTY]:` (status stays `in_progress`; the task list has no such status), reports what is done and the exact ask, then ⏸ waits; ✗ continue, skip, substitute, or poll. ADM surfaces the ask immediately, holds dependents, relays the answer by `SendMessage`, clears the prefix, records the value in the quarterdeck report. Such a ship is exempt from stand-down until the step completes.
