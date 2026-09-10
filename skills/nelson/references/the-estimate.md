# The Estimate

Ω₂, between Ω₁ and Ω₃. 📖 before conducting it. The Royal Navy's 7 Question Maritime Tactical Estimate: a brief → a plan every CPT can act on independently, as Nelson's Trafalgar Memorandum did.

## The seven questions

Ε₁ **Reconnaissance** what is the terrain, what are we working with? · Ε₂ **Intent** what are we really trying to achieve, and why? · Ε₃ **Effects** what changes must occur to fulfil the intent? · Ε₄ **Terrain** where in the codebase does each effect land? · Ε₅ **Forces** what agents, models, and context do we need? · Ε₆ **Coordination** what depends on what, what runs in parallel? · Ε₇ **Control** where are the quality gates and intervention points?

A thought-process, not a form: short answers for simple missions, deeper for complex ones. Always seven.

## Interactive flow

A conversation with the user, not a monologue.

**Ε₁ Reconnaissance.** ≥1 Explore with a scouting brief from the sailing orders; ambiguous ∨ unfamiliar terrain → parallel dispatches, different search targets. Synthesise a terrain assessment in ADM's own voice; ✗ paste raw agent output. Explorer discipline:

- focused Explores (one subsystem ∨ one question each, ≤ ten files ∨ one module per dispatch), ¬ one laundry-list dispatch
- ∀ brief MUST require a structured summary (`{file_path, finding, evidence}` list or equivalent); ✗ raw file contents; ADM synthesises across summaries
- ? only one dispatch warranted (small repo, narrow question, single subsystem) ∧ Explore prompt limits a concern → `subagent_type=general-purpose` + explicit "return a structured summary; do not return raw file contents"
- Explorers inherit ADM's model; the cost-savings haiku default ✗ during Ω₂ (`references/model-selection.md`)
- Explore fails (prompt-length limit, malformed output, error) → `references/standing-orders/pulling-the-oar.md`: fix the brief, re-dispatch; ✗ absorb into ADM context

**Checkpoint 1 — after Ε₁.** ⏸ *"Here is what I found. Is there anything I have missed? Are there additional constraints I should know?"* ? Ε₁ shows the stated mission will not achieve the user's actual intent → say so plainly, propose a reframing; the user confirms, amends, or overrides. Reframed → amend `sailing-orders.json`, keep the original as context.

**Dispatch 1 — Ε₂ ∧ Ε₃ (Estimate-Drafter).** One subagent. Ε₂ = commander's intent from Ε₁ + sailing orders, one paragraph carried in every CPT brief. Ε₃ = concrete effects, each with commander's guidance ∧ acceptance criteria (below). 💾 briefing → `{mission-dir}/estimate-briefing-1.md` (survives compaction), referenced in the `Agent` prompt:

- full sailing orders, ¬ summary
- pointer to `{mission-dir}/estimate.md` (Ε₁ synthesis)
- output: H2 sections Ε₂ Intent ∧ Ε₃ Effects; voice and register per this file
- mission directory path
- user preferences stated in conversation but absent from sailing orders (e.g. "admiral must not implement", "cost-savings enabled"); ADM surfaces these from its own context
- pointer to `references/the-estimate.md`

Subagent appends Ε₂ ∧ Ε₃ to `{mission-dir}/estimate.md`.

**Checkpoint 2 — after Ε₃.** ⏸ *"Here is what I believe needs to happen and why. Does this match your understanding?"* The substantive gate: *what* approved before *how* is planned.

**Dispatch 2 — Ε₄–Ε₇ (Estimate-Planner).** Second subagent reads approved Ε₁–Ε₃ from `{mission-dir}/estimate.md` → Terrain, Forces, Coordination, Control: ADM's judgement on execution. 💾 `{mission-dir}/estimate-briefing-2.md`:

- pointer to `{mission-dir}/estimate.md` (approved Ε₁–Ε₃)
- output: H2 sections Ε₄–Ε₇; voice and register per this file
- mission directory path
- user preferences stated in conversation but absent from sailing orders
- pointer to this file

Appends Ε₄–Ε₇ to `{mission-dir}/estimate.md` → ADM presents the complete estimate.

**Model inheritance.** Both subagents omit `model:` on `Agent` → ADM's model, even under cost-savings (`references/model-selection.md`).

**Final review.** ⏸ approve, amend, or override specific questions. Approved → `ESTIMATE` ⟶ `BATTLE_PLAN`.

## Checkpoint discipline

Checkpoints are *available*, not *mandatory*. Collapse to one end-of-estimate review only when all three hold: sailing orders specify outcome ∧ metric ∧ deadline · Ε₁ reveals no surprises requiring reframing · work lands in a single subsystem ∨ file. Otherwise two checkpoints; in doubt → checkpoint.

## Effects, acceptance criteria, and commander's guidance

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

**Effect** = what must change, outcome-focused. **Commander's guidance** = how (libraries, patterns, design decisions): specific enough to prevent wrong turns, loose enough for professional judgement. **Acceptance criteria** = what must be true when complete; ∀ criterion has a verification method: existing tests, type-checkers, linters, review agents, visual inspection are all valid, ¬ every criterion needs a new unit test.

Flow: Ω₃ ∀ task inherits its parent effect's criteria → CPTs know "done" before coding and pick the method per criterion → Ω₆ verifies ∀ criterion, records outcome (`pass` / `fail` / `not-verified`) ∧ method via `ND estimate-outcome`.

## Adaptive planning — dated addenda

A living document, not a contract. ADM ∨ CPT meets a contradiction (unexpected complexity, hidden dependency, unworkable approach) → dated addendum to the relevant section; original reasoning stays visible, correction explicit, downstream plans adjust:

```markdown
## Addendum — 14:32

Reconnaissance assumed `src/auth/session.ts` was a single-concern module.
In practice it also owns refresh token rotation and rate-limit state.
Effects revised: the original signing effect now splits into two.
Coordination updated accordingly.
```

## Voice and register

A capable officer briefing peers: concise never terse, clear never flat, confident never glib. Cross-references between questions in natural prose, ¬ IDs or schemas: Ω₃ is the same ADM reading its own work.

## Output

💾 `{mission-dir}/estimate.md`, one H2 per question:

```
{mission-dir}/estimate.md
  ## 1. Reconnaissance
  ## 2. Intent
  ## 3. Effects
  ## 4. Terrain
  ## 5. Forces
  ## 6. Coordination
  ## 7. Control
```

Unwieldy section → split to `{mission-dir}/estimate/0N-name.md` with a prose pointer in the parent; one file is the default. Skeleton: `admiralty-templates/estimate.md`.
