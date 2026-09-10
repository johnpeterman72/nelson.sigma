# Standing Order Φ₁₆: Wrong Ensign

! Using tools that belong to a different Μ.

∀ Μ has its own coordination surface; wrong-mode tools fail silently: `TaskGet` returns "Task not found" in Μ₂, `SendMessage` fails without a prior `TeamCreate`, agents spawned with mismatched parameters cannot reach the squadron.

**Symptoms:** `TaskGet` ∨ `TaskList` empty or "Task not found" after CPTs report work complete · `SendMessage` fails, no team exists (Μ₂ has none) · CPTs spawned with `team_name` in Μ₂, or `subagent_type` in Μ₃ · ADM retrieves results via `TaskGet` when CPTs were dispatched as teammates (Μ₃) · CPTs cannot update shared task state, no task list exists.

**Remedy:** before spawning, 📖 `references/tool-mapping.md`; confirm ∀ planned tool call is valid for the selected Μ:

- Μ₂ `subagents` — ✓ `Agent` with `subagent_type` to spawn, `SendMessage(type="shutdown_request")` to shut down. ✗ (CPTs) `TaskCreate`, `TaskList`, `TaskGet`, `TaskUpdate`, `SendMessage(type="message")`, `SendMessage(type="broadcast")`, `TeamCreate`, `TeamDelete`. Exception: ADM uses `TaskCreate`/`TaskUpdate`/`TaskList` for session-level visibility (the user's Ctrl+T task list); invisible to CPTs, for the user only.
- Μ₃ `agent-team` — ✓ `TeamCreate` to form the squadron, `Agent` with `team_name` + `name` to spawn, `TaskCreate`/`TaskList`/`TaskGet`/`TaskUpdate` for coordination, `SendMessage` for all message types, `TeamDelete` to stand down. ✗ `Agent` with `subagent_type` for CPTs (RM still use `subagent_type`).
- Μ₁ `single-session` — ✓ `TaskCreate`, `TaskUpdate`, `TaskList`, `TaskGet` (visibility tracking). ✗ `Agent`, `TeamCreate`, `TeamDelete`, `SendMessage`.

Detected mid-mission → `references/damage-control/comms-failure.md`.
