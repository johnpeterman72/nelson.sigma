# Δ₇ Relief on Station: Context Window Exhaustion

Ship's context depleted → fresh ship takes the task. Planned handover; stuck agents → `man-overboard.md`, unplanned interruption → `session-resumption.md`.

**Triggers** (any): Η🔴 Red (40–59 % context remaining) · Η⚫ Critical (below 40 %) · ADM sees degraded output (repetition, missed instructions, shallow reasoning) · ship requests relief. Fast burn → also check `crew-overrun.md`.

## Relief Sequence

1. ADM signals the ship to prepare for turnover.
2. Ship pauses; commits ∨ saves outputs.
3. Ship writes a typed packet: `ND handoff --mission-dir {mission-dir} --ship-name "..." --task-id N --task-name "..." --handoff-type relief_on_station ...` (arguments: `references/structured-data.md`) → `{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.json` + `relief_on_station` event. ~ prose companion `{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.md` per `references/admiralty-templates/turnover-brief.md`.
4. Ship signals ADM with the packet path.
5. ADM spawns the replacement; class fits the remaining work (destroyer → frigate if lighter).
6. Crew briefing carries the packet path; replacement reads it first.
7. ADM reassigns the task.
8. `TaskUpdate` → new owner.
9. Shutdown request to the relieved ship.
10. Update the battle plan.

## Flagship Self-Monitoring

ADM checks own hull at ∀ checkpoint.

- **Η🟢 Green (75–100 %):** continue.
- **Η🟡 Amber (60–74 %):** note hull in the quarterdeck report · draft a flagship turnover brief incrementally in the background · coordination will not finish within budget → plan the handover early.
- **Η🔴 Red (40–59 %):**
    1. `ND handoff --handoff-type relief_on_station ...` → `{mission-dir}/turnover-briefs/flagship-{timestamp}.json`, plus prose brief `{mission-dir}/turnover-briefs/flagship-{timestamp}.md`: sailing orders verbatim · battle plan with task statuses, owners, ships · ∀ ship status + hull · key decisions + rationale · blockers, risks, pending escalations · cadence + next checkpoint · relief chain history.
    2. Notify Admiralty: handing over, brief path.
    3. Admiralty opens a new session; new ADM reads the brief first, resumes from the last checkpoint.
- **Η⚫ Critical (below 40 %):** Red procedure now, ¬ at the next checkpoint; the brief outranks all other coordination.

## Chained Reliefs

A → B → C on one task:

1. ∀ packet carries `relief_chain`, ∀ prior handover; `handoff` validates ≤ 3 entries.
2. Entry: ship, reason, time.
3. Current ship writes a full packet for its own work; predecessors are chain entries only.
4. Max 3 reliefs per task; a third needed → ADM re-scopes (too large ∨ ill-defined for one ship).

## Crew Variant

Crew member exhausted → CPT relieves at ship level.

1. CPT identifies the member at Η🔴 ∨ Η⚫.
2. Member writes a packet to file (turnover brief if unable to run `nelson-data.py`).
3. CPT spawns a replacement with the packet path.
4. Shutdown request to the exhausted member.
5. Update the ship manifest.
6. Same role twice → escalate to ADM; re-scope the sub-task.
