# Standing Order Φ₁₅: Paid Off

Stand down agents the moment they have no remaining work in the task graph.

**Symptoms:** agent completed its task but idles while other tasks continue · ADM holds agents "just in case" with no concrete rework trigger in the sailing orders · idle agents occupy panel slots and coordination attention without adding throughput.

**Remedy:** task confirmed complete → is the agent a prerequisite for any remaining pending task? ¬ ∧ no rework loop in the sailing orders names a trigger that would re-task it → shutdown. Gate by Μ:

- Μ₃ `agent-team`: before `shutdown_request`, confirm ADM has received ∧ processed the CPT's results. Processed = ADM has read the deliverables, whether via `SendMessage`, written to disk, or recorded in the `TaskList` description. Not received → retrieve first (`SendMessage` ∨ read the output files) → then `shutdown_request`.
- Μ₂ `subagents`: `Agent` returns results synchronously on completion; no confirmation step → `shutdown_request` immediately.

Hold an agent only when a concrete re-task condition is written into the sailing orders (e.g. "if milestone < 90%, re-task WP1 captain for rework"). Trigger evaluated ∧ not fired → stand down without hesitation. "We might need them later" is not a trigger.

**Exception:** CPT whose task description is prefixed `[AWAITING-ADMIRALTY]:` ✗ stand down. ADM holds them `in_progress` until Admiralty provides the input. Only after ADM relays it, clears the prefix, ∧ CPT completes the remaining work → stand down normally.
