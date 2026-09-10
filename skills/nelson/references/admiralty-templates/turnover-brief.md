# Τ Turnover Brief

## Typed Handoff Packet (Primary)

Primary format = structured JSON handoff packet written by `nelson-data.py handoff` (schema and command: `references/structured-data.md`) to `{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.json`. The replacement ship reads it as its first action.

## Prose Turnover Brief (Companion)

- Typed packet written → the damaged ship ~ also writes a prose companion to `{mission-dir}/turnover-briefs/{ship-name}-{timestamp}.md` from the template below: a human-readable narrative for manual review, ¬ the primary recovery artifact.
- `nelson-data.py handoff` unavailable (script error, pre-upgrade missions) → the prose brief is the fallback format.

```text
== TURNOVER BRIEF ==
Ship: [ship name and class]
Role: [Captain N — role description]
Timestamp: [when this brief was written]
Reason for relief: [Red hull / Critical hull / degraded output / requested]

Mission context:
- Mission: [mission name from sailing orders]
- Outcome: [outcome from sailing orders]
- Success metric: [metric from sailing orders]

Task assignment:
- Task ID: [from battle plan]
- Task name: [from battle plan]
- Deliverable: [what must be produced]
- Action station: [0-3]
- File ownership: [files assigned to this task]
- Dependencies: [upstream and downstream tasks]

Progress log:
- [Completed item — specific description of what was done]
- [...]

Running plot (work in progress when relieved):
- [What was being worked on at the time of relief]
- [Current state of that work — how far along, what remains]
- [Any partial outputs saved and where to find them]

Files touched:
- [file path] — [description of changes made]
- [...]

Key decisions made:
- [Decision] — Rationale: [why this choice was made]
- [...]

Hazards and blockers:
- [Hazard or blocker — current status and impact]
- [None discovered, if applicable]

Recommended course of action:
- [What the replacement should do first]
- [What to do next]
- [What to avoid or watch out for]

Relief chain:
- [Previous Ship Name] | [time on station] | [key accomplishment] | [reason for relief]
- [This is the first ship on this task, if no previous reliefs]

== END TURNOVER BRIEF ==
```

## Flagship Turnover Brief

ADM handing over → append these sections after the standard fields; "Task assignment" is replaced by full squadron state.

```text
== FLAGSHIP TURNOVER BRIEF ==
Ship: Flagship [name]
Role: Admiral
Timestamp: [when this brief was written]
Reason for relief: [hull integrity level and percentage if known]

Sailing orders:
- Outcome: [verbatim from sailing orders]
- Success metric: [verbatim from sailing orders]
- Deadline: [verbatim from sailing orders]
- Constraints: [verbatim from sailing orders]
- Out of scope: [verbatim from sailing orders]

Battle plan status:
- Task [ID]: [name] | Owner: [ship] | Status: [pending/in_progress/completed] | Notes: [brief]
- [...]

Squadron state:
- [Ship name] ([class]) | Captain [N] | Task: [ID] | Hull: [Green/Amber/Red/Critical] | Status: [active/relieved/stood down]
- [...]

Key decisions made:
- [Decision] — Rationale: [why]
- [...]

Active blockers and risks:
- [Blocker/risk] — Owner: [who] — Status: [open/mitigating/resolved]
- [...]

Pending escalations:
- [Escalation description] — Awaiting: [Admiralty decision / agent response]
- [None, if applicable]

Quarterdeck rhythm:
- Cadence: [e.g., every 15 minutes]
- Last checkpoint: [timestamp or checkpoint number]
- Next scheduled checkpoint: [timestamp or checkpoint number]

Relief chain:
- [Previous Admiral session] | [time on station] | [key accomplishment] | [reason for relief]
- [This is the first admiral on this mission, if no previous reliefs]

Recommended course of action:
- [What the new admiral should do first]
- [Priority items requiring immediate attention]
- [Ships that may need relief soon]

== END FLAGSHIP TURNOVER BRIEF ==
```

## Field Notes

- 💾 to file, ¬ message: the replacement reads it from disk, so the brief never consumes message context.
- Progress log: be specific. ✗ "Implemented the auth module". ✓ "Implemented JWT validation in `src/auth/validate.ts` with RS256 signing, added tests in `tests/auth.test.ts` covering expired/malformed/valid tokens".
- Running plot ≥1 bullet, critical: exactly what was in flight, ¬ just what finished, with file paths, function names, and the point where work stopped.
- Relief chain bounded: one line per previous relief, summarised; ✗ paste previous briefs. Chain reaches 3 entries → ADM re-scopes the task rather than adding a fourth.
- Flagship briefs copy sailing orders verbatim: the new ADM session has no memory of the original orders.
