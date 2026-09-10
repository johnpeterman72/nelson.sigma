# Standing Order Φ₁₄: Awaiting Admiralty

CPT reaches a planned human-action step ∧ has completed all autonomous work → MUST invoke this order.

**Status convention:** `TaskUpdate` accepts only `pending`, `in_progress`, and `completed` as status values. `awaiting-admiralty` is a naming convention, not a status enum: prefix the task description with `[AWAITING-ADMIRALTY]:` and leave status `in_progress`.

**Trigger:** CPT completes all autonomous work for a task ∧ reaches a step marked `admiralty-action-required: yes`.

**CPT procedure:**

1. Complete all work needing no human input. 💾 ∀ artifacts to disk.
2. `TaskUpdate` → prefix the description with `[AWAITING-ADMIRALTY]:`; status stays `in_progress` (✗ `completed`).
3. Report to ADM: what was completed (artifact name and location) · exact ask (what the human must do and return) · what is blocked until resolved.
4. ⏸ ✗ continue, skip, or substitute. Wait for the ADM's `SendMessage` relaying Admiralty's input; no action before it; ✗ poll. Hull-integrity procedures still apply while holding: context pressure before the `SendMessage` arrives → signal ADM so a turnover brief is written before exhaustion.

**ADM procedure on this report:**

1. Surface to Admiralty immediately; ¬ defer to the next quarterdeck checkpoint.
2. Hold ∀ dependent tasks.
3. Admiralty input received → relay to CPT via `SendMessage` → `TaskUpdate` removes the `[AWAITING-ADMIRALTY]:` prefix → confirm status remains `in_progress`.
4. Record the resolved value in the quarterdeck report.
