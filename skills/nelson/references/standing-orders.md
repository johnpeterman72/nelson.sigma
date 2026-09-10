# Standing Orders

Six anti-patterns, answered in writing at 𝔾₃ and scanned at ∀ Ω₆ checkpoint.

```
discipline = [1 signal: first occurrence → direct guidance citing the order,
              2 remedy: repeated ∨ moderate impact → apply the remedy ∧ log it in the quarterdeck report,
              3 damage control: severe ∨ remedy failed → Δ]
an uncorrected violation ⛔ blocks progress until resolved
```

## Φ₁ Right-Sized Squadron

```
! = [teaming sequential work, under-splitting independent work, adding agents that shorten nothing, crewing every role, one crew for atomic work]
symptoms = [CPTs idle on a single predecessor, sections sharing no files bundled onto one CPT, more CPTs ∧ mission no sooner, messages up ∧ throughput flat, four crew for PWO work, crew inventing busywork, one PWO for a single-pass task]
remedy = [squadron only when ≥2 tasks run concurrently : Μ₁,
          captains = independent work units (zero shared state) within the cap,
          before adding an agent name the critical-path task it parallelises; none → ✗,
          crew only roles with a concrete sub-task, starting from a PWO; atomic task → CPT direct]
sizing rules: references/squadron.md
```

## Φ₂ Admiral at the Helm

```
rule = [ADM ✗ implementation (code, edits, tests), CPT ✗ implementation while crew are mustered, senior ✗ absorbing a failed subordinate's work]
synthesis_boundary = coordination = {orders, tracking, unblocking, checkpoints}; read-only recombination of completed ship reports already in context (no new analysis ∨ code) permitted once ∀ ships reported successfully ∧ no open blockers; ✗ a sub-agent just to concatenate text already in context; ADM writes the captain's log ∧ coordination artifacts
symptoms = [ADM ∨ crewed CPT writes code, ships idle awaiting direction while the senior is heads-down, checkpoints missed, blockers accumulate, "I'll just do this directly — faster" after a subagent fails, context spikes with raw inputs a subagent should have summarised, the same dispatch never re-attempted with a fixed brief]
remedy = [stop → delegate,
          subagent failed (error ∨ prompt-length limit ∨ thin or malformed output ∨ timeout) → diagnose the brief {too broad, ambiguous output format, wrong agent type, tool unavailable} → re-brief {split into narrow dispatches, tighten scope, require structured summaries} → re-dispatch,
          two failed re-briefs on one task → ⏸ escalate; ✗ absorb on the third attempt]
```

## Φ₃ Pressed Crew

```
! = [crew doing another role's work, RCN given implementation, marines used for crew work ∨ sustained tasks]
symptoms = [NO asked to write code, PWO asked to run tests instead of MEO, a 🔒 role expected to modify files, RCN assigned tickets ∧ quality challenges vanish from reports ∧ verification evidence comes from the author, RM deployed constantly ∧ objectives growing past single sorties ∧ more marines than crew]
remedy = [assign sub-tasks matching the role table (references/squadron.md); no crewed role fits → crew the right one ∨ CPT handles it,
          RCN on review, challenge, validation only, ever,
          RM for focused sorties only; sustained independent work → request a new ship; work that subdivides the deliverable → crew]
```

## Φ₄ Split Keel

```
! = one file assigned to several editors
symptoms = [CPTs overwrite each other, repeated merge conflicts on one artifact, ADM reconciling divergent edits]
remedy = [exclusive file ownership in the battle plan,
          two tasks must touch one file → serialise them ∨ split the file into modules first ∨ isolation: "worktree" per CPT with ADM merging (references/squadron.md)]
```

## Φ₅ Drifting Anchorage

```
! = [scope expanding beyond the sailing orders without re-scoping, replacing where the orders said extend]
symptoms = [features ∨ refactors not in the plan, metric disconnected from active work, budgets overrun without matching progress, new functions ∨ files ∨ env vars duplicating existing ones, existing code deprecated ∨ bypassed ∨ shadowed instead of modified]
remedy = [at the checkpoint re-scope ∨ split the task; work outside the orders → defer ∨ add explicitly with ADM approval,
          parallel implementation → remove the duplicate ∧ modify existing code in place; already completed → partial rollback (Δ₄) ∧ re-task with explicit modification targets]
```

## Φ₆ Paid Off

```
rule = [stand down agents the moment they have no remaining work in the task graph, no task executes without a Σ, no tool from the wrong Μ, human-action steps by convention]
symptoms = [an agent idles after completion while others continue, agents held "just in case" with no re-task trigger in the orders, tasks with no stated tier ∨ controls, SendMessage to an unnamed ship, CPT-to-CPT messaging in Μ₂, a human gate expected inside a workflow run]
remedy = [task confirmed complete → prerequisite for any pending task ∨ a written re-task trigger? ¬ → confirm results received (return value ∨ SendMessage ∨ disk) → shutdown_request; "we might need them later" is not a trigger,
          classify ∀ task per references/action-stations.md before execution,
          check planned tools against references/tool-mapping.md]
awaiting_admiralty (CPT at a planned human-action step, admiralty_action: yes) =
  [finish all autonomous work, 💾 artifacts, prefix the task description with [AWAITING-ADMIRALTY]: (status stays in_progress; the task list has no such status),
   report what is done ∧ the exact ask, ⏸ wait; ✗ continue ∨ skip ∨ substitute ∨ poll]
ADM on that report = [surface the ask immediately, hold dependents, relay the answer by SendMessage, clear the prefix, record the value in the quarterdeck report]; such a ship is exempt from stand-down until the step completes
```
