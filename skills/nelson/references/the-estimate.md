# The Estimate

Ω₂, between Ω₁ and Ω₃. 📖 before conducting it. The Royal Navy's 7 Question Maritime Tactical Estimate turns a brief into a plan every CPT can act on independently, as Nelson's Trafalgar Memorandum did.

## The Seven Questions

Ε₁ **Reconnaissance** what is the terrain, what are we working with? · Ε₂ **Intent** what are we really trying to achieve, and why? · Ε₃ **Effects** what changes must occur to fulfil the intent? · Ε₄ **Terrain** where in the codebase does each effect land? · Ε₅ **Forces** what agents, models, and context do we need? · Ε₆ **Coordination** what depends on what, what runs in parallel? · Ε₇ **Control** where are the quality gates and intervention points?

A thought process, not a form: short answers for simple missions, deeper for complex ones. Always seven.

## Depth

Inline (three lines inside the battle plan) when all hold: sailing orders carry outcome ∧ metric ∧ deadline · work lands in one subsystem · no surprises expected. Otherwise the full flow below. In doubt → full.

## Flow

Runs in plan mode when available, so nothing is written until Ω₅ approval.

**Ε₁ Reconnaissance.** ≥1 `Explore` dispatch with a scouting brief from the sailing orders; ambiguous ∨ unfamiliar terrain → parallel dispatches with different targets. Synthesise a terrain assessment in ADM's own voice; ✗ paste raw agent output. Explorer discipline:

- focused dispatches: one subsystem ∨ one question each, ≤ ten files ∨ one module; ¬ a laundry list
- ∀ brief requires a structured summary (`{file_path, finding, evidence}` list or equivalent); ✗ raw file contents
- Explorers inherit ADM's model; the cost-savings haiku default ✗ during Ω₂
- a dispatch fails (prompt-length limit, malformed output, error) → Φ₂: fix the brief, re-dispatch; ✗ absorb into ADM context

**Checkpoint after Ε₁ (conditional).** Ε₁ shows the stated mission will not achieve the user's actual intent, or surfaces constraints the orders lack → ⏸ *"Here is what I found. Is there anything I have missed?"* Propose a reframing; the user confirms, amends, or overrides; reframed → amend the sailing orders, keep the original as context. No surprises → continue without interrupting.

**Ε₂ ∧ Ε₃.** Ε₂ = commander's intent, one paragraph from Ε₁ and the sailing orders, carried in every CPT brief. Ε₃ = concrete effects, each with commander's guidance and acceptance criteria (below). Large mission → one subagent (Estimate-Drafter) given the full sailing orders, the Ε₁ synthesis, user preferences stated in conversation, and a pointer to this file; it returns Ε₂ and Ε₃ as text.

**Ε₄–Ε₇.** Terrain, forces, coordination, control: ADM's professional judgement on execution, worked through without interrupting the user. Large mission → a second subagent (Estimate-Planner) given approved Ε₁–Ε₃ and this file; returns Ε₄–Ε₇ as text. Both subagents omit `model` and inherit ADM's.

**Approval.** The estimate is presented with the battle plan and formation at Ω₅; the user approves, amends, or overrides specific questions. Then 💾 `{mission-dir}/estimate.md`.

## Effects, Guidance, Criteria

∀ effect in Ε₃ carries three elements:

```markdown
### Effect: Replace session auth with JWT signing

Lands on `src/auth/session.ts`. High complexity.

**Commander's guidance:** Use the `jose` library, ES256 algorithm,
15-minute expiry with refresh rotation.

**Acceptance criteria:**
- All 47 existing auth tests pass without modification
- New unit tests cover token signing, verification, and expiry
- No runtime dependency on Redis for authentication
- Token payload contains only `sub`, `iat`, `exp` claims
```

**Effect** = what must change, outcome-focused. **Commander's guidance** = how: libraries, patterns, design decisions; specific enough to prevent wrong turns, loose enough for judgement. **Acceptance criteria** = what must be true when done; ∀ criterion has a verification method (existing tests, type-checkers, linters, review agents, visual inspection all count; ¬ every criterion needs a new unit test).

Flow: Ω₃ ∀ task inherits its parent effect's criteria → CPTs know "done" before coding and choose the method per criterion → Ω₇ verifies ∀ criterion → Ω₈ records each outcome (`pass` / `fail` / `not-verified`) and method in the captain's log.

## Adaptive Planning

A living document. ADM ∨ CPT meets a contradiction (unexpected complexity, hidden dependency, unworkable approach) → dated addendum under the relevant section; original reasoning stays visible, correction explicit, downstream plans adjust:

```markdown
## Addendum — 14:32

Reconnaissance assumed `src/auth/session.ts` was a single-concern module.
In practice it also owns refresh token rotation and rate-limit state.
Effects revised: the original signing effect now splits into two.
```

## Voice

A capable officer briefing peers: concise never terse, clear never flat, confident never glib. Cross-reference in natural prose ("the auth effect from §3"), ¬ IDs.

## Output

`{mission-dir}/estimate.md`, one H2 per question, prose first:

```markdown
# The Estimate — {mission title}

## 1. Reconnaissance
## 2. Intent
## 3. Effects
### Effect: {name}
{what must change, where, why}
**Commander's guidance:** {…}
**Acceptance criteria:**
- {criterion}
## 4. Terrain
## 5. Forces
## 6. Coordination
## 7. Control
```

Unwieldy section → split to `{mission-dir}/estimate/0N-name.md` with a prose pointer in the parent; one file is the default.
