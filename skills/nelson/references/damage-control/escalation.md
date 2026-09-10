# Δ₅ Escalation: Chain of Command

Upward: crew → CPT → ADM → Admiralty (human).

## Triggers

| Trigger | First action |
|---|---|
| Ambiguous requirement ∨ acceptance criteria | CPT pauses, asks ADM |
| Agents disagree on approach | ADM decides; unsure → Admiralty |
| Scope creep past the original definition | ADM re-scopes ∨ Admiralty approval |
| Unexpected out-of-scope dependency | ADM pauses dependent work → Admiralty |
| Σ₂+ risk found mid-task | ADM raises Σ, applies its controls |
| Budget near limit, critical work left | ADM → Admiralty: extend ∨ descope ∨ abort |
| CPT at a planned human-action step (`admiralty-action-required: yes`) | `awaiting-admiralty` standing order (planned, ¬ a failure) |

## Procedure

1. Agent pauses the task.
2. Reports to ADM: issue, options, one recommendation.
3. Within ADM authority → decide, record rationale. : → Admiralty with summary + recommendation.
4. Admiralty directs.
5. ADM relays, updates the battle plan.
6. Agent resumes.

## Authority Boundaries

- **Crew:** own sub-task. Escalates to CPT: other crew ∨ ship deliverable affected.
- **CPT:** own task. Escalates: other tasks, shared resources, mission scope.
- **ADM:** re-assign, replace agents, adjust timelines, raise Σ, descope within the sailing orders. Escalates: scope changes, budget extensions, abort.
- **Admiralty:** final on scope, budget, abort; confirms ∀ irreversible ∨ high-blast-radius decision.
