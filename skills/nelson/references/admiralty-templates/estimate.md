# Τ Estimate

Light scaffolding: ADM writes prose, ¬ forms. Thought process per section: `references/the-estimate.md`.

```markdown
# The Estimate — {mission title}

## 1. Reconnaissance
- What terrain was scouted (files, subsystems, external dependencies)
- What Explore agents were dispatched, and what they found
- Notable surprises, constraints, or prior art

## 2. Intent
- Commander's intent (one paragraph, propagates to every captain's brief)
- Why this matters; what success looks like in the user's terms

## 3. Effects

### Effect: {short outcome-focused name}

{One paragraph: what must change, where it lands, why.}

**Commander's guidance:** {library choices, patterns, design decisions.}

**Acceptance criteria:**
- {Criterion 1 — paired with verification method in captain's work}
- {Criterion 2}
- {Criterion 3}

### Effect: {next effect}
...

## 4. Terrain
- Files and modules each effect lands on
- Test suites affected
- Blast radius per effect

## 5. Forces
- Captains required (ship class, model, number)
- Crew roles where applicable
- Red-cell navigator? Marines?

## 6. Coordination
- Dependency graph (what must precede what)
- Parallel tracks
- Shared artifacts or coordination surfaces

## 7. Control
- Quality gates (where verification runs)
- Intervention points (where the user may wish to inspect)
- Action-station tiers per task
- Rollback plan
```

Notes for ADM:

- One H2 per question. Prose; bullets are a fallback, ¬ a default.
- ∀ effect in §3 carries commander's guidance ∧ ≥1 acceptance criterion.
- Cross-reference naturally ("the auth effect from §3", ¬ "Effect AC-1").
- The estimate evolves by dated addenda appended under the relevant section; ✗ rewrite history.
