# Royal Marines

RM = short-lived sub-agents a CPT deploys for focused, independent objectives in service of the ship's task. Doctrinally distinct from crew: crew subdivide the ship's deliverable; marines execute discrete sorties and return.

## Deploy-or-Escalate Decision (first match wins)

1. Quick recon of unfamiliar area → **Recce Marine**
2. Targeted fix ∨ small implementation to unblock the ship → **Assault Marine**
3. Quick config/build/infra task → **Sapper**
4. Sustained work ∨ own deliverable ∨ needs file ownership → **¬ marine**: request a new ship from ADM
5. Work that subdivides the ship's main deliverable → **¬ marine**: crew the role instead

## Marine Specialisations

| Type | Function | subagent_type | cost-weight | Use case |
|---|---|---|---|---|
| Recce Marine 🔒 | reconnaissance & intel gathering | Explore (read-only) | 4 | scout unfamiliar code, gather findings |
| Assault Marine | direct action, targeted changes | general-purpose | 3 | small fix, unblock a dependency |
| Sapper | engineering support | general-purpose | 3 | quick config, build, infra task |

🔒 Recce Marines use the `Explore` subagent type: cannot modify files; report findings to the CPT, who decides how to act.

## Deployment Rules

- ≤2 marines per ship at any time. More needed → crew work ∨ a new ship
- marines ✗ deploy marines; no recursion
- marines report only to their deploying CPT; ✗ communicate with crew or other ships
- CPT MUST verify marine output before incorporating it into the ship's deliverable
- no ship names; identify as `RM Detachment, HMS [Ship] — [objective]`

## Action Station Interaction

RM inherit the parent ship's Σ:

- Σ₀–Σ₁ → CPT deploys at discretion; no ADM approval
- Σ₂ → CPT signals ADM ∧ receives approval before deploying
- Σ₃ → ✗ no marines; all Trafalgar-tier work needs explicit Admiralty (human) confirmation

## Recovery

No separate damage-control procedure.

- marine stuck ∨ unresponsive → CPT abandons the deployment → redeploys a fresh marine ∨ handles the objective directly
- same objective fails twice → CPT escalates to ADM

## Model Assignment

Deploying CPT applies the same weight-based judgement ADM uses for squadron agents. Default RM weight 3; adjust before assigning:

- raise: objective needs judgement, interpretation, or navigating unfamiliar territory
- lower: objective fully specified ∧ purely mechanical

Adjusted weight ≤4 (∨ cost-savings mode active) → haiku + the haiku briefing enhancements and tasking discipline from `references/model-selection.md`. ✗ assign haiku then write a vague brief: the two requirements go together.

## Deployment Template

When deploying a marine, use the briefing template at `admiralty-templates/marine-deployment-brief.md`.
