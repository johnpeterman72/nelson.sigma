# Crew Roles Reference

ADM uses this at Ω₄ to set initial crew per ship. CPTs use it to request mid-task crew adjustments (ADM approval required).

## Crew-or-Direct Decision (first match wins)

1. Atomic ∧ no file reads or research ∧ single pass → CPT implements directly (0 crew)
2. One clear deliverable (may include file reads or light research) → 1 PWO
3. Needs exploration ∨ testing ∨ a second specialism → PWO + 1 specialist
4. Multiple interdependent sub-tasks → XO + PWO + ≤2 specialists

Never >4 crew per ship. Task demands more → split into two ships.

## Crew Sizing

| Crew | When | Typical manifest |
|---|---|---|
| 0 | atomic task, single-pass fix | CPT implements directly |
| 1–2 | typical task | PWO, ~ + 1 specialist |
| 3 | complex task with research or testing needs | PWO + 2 specialists |
| 4 | multi-part task requiring internal orchestration | XO + PWO + 2 specialists |

## Role Definitions

| Role | Abbr | Function | subagent_type | cost-weight | When to crew |
|---|---|---|---|---|---|
| Executive Officer | XO | integration & orchestration across sub-tasks | general-purpose | 10 | 3+ crew ∨ interdependent sub-tasks |
| Principal Warfare Officer | PWO | core implementation work | general-purpose | 2 | almost always (default doer) |
| Navigating Officer | NO 🔒 | codebase research & exploration | Explore | 7 | unfamiliar code, large codebase |
| Marine Engineering Officer | MEO | testing & validation | general-purpose | 6 | Σ₁+ ∨ non-trivial verification |
| Weapon Engineering Officer | WEO | config, infrastructure, systems integration | general-purpose | 6 | significant config or infrastructure work |
| Logistics Officer | LOGO | documentation & dependency management | general-purpose | 2 | docs as deliverable, dependency management |
| Coxswain | COX 🔒 | standards review & quality enforcement | Explore | 4 | Σ₁+ with established conventions |

🔒 NO and COX use the `Explore` subagent type: cannot modify files; report findings to the CPT or XO, who decides how to act.

**Role boundaries:** ∀ crew works strictly within their role. PWO ✗ run tests (MEO does). NO ✗ write code (reports findings). Anti-pattern: `standing-orders/pressed-crew.md`.

## Ship Name Registry

ADM assigns ∀ CPT a ship name at Ω₄; match class to task weight.

| Class | Tasks | Names |
|---|---|---|
| Frigates | general-purpose | Argyll, Kent, Lancaster, Richmond, Somerset, Portland, Iron Duke, St Albans |
| Destroyers | high-tempo ∨ high-risk | Daring, Dauntless, Diamond, Dragon, Defender, Duncan |
| Patrol Vessels | small | Forth, Medway, Trent, Tamar, Spey |
| Historic Flagships | critical-path | Victory, Warspite, Vanguard, Ark Royal |
| Submarines | stealth ∨ research | Astute, Ambush, Artful, Audacious |

## Crew Standing Orders

- `standing-orders/captain-at-the-capstan.md` — CPT ✗ implement while crew are mustered
- `standing-orders/all-hands-on-deck.md` — ✗ crew roles the task does not need (too many)
- `standing-orders/skeleton-crew.md` — ✗ one crew member for an atomic task (too few)
- `standing-orders/pressed-crew.md` — ✗ crew work outside their designated role (wrong crew)

## Royal Marines

RM ≠ crew: short-lived sub-agents a CPT deploys for discrete objectives outside the crew's task scope. Crew subdivide the ship's deliverable; marines execute independent sorties in support of the ship's task. Deployment rules and specialisations: `references/royal-marines.md`.
