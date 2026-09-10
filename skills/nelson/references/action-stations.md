# Action Stations

Classify ∀ task before execution; apply the minimum controls for its tier. Controls are cumulative: each tier includes everything below it.

## Classification (first yes wins)

```
classify = [irreversible ∨ regulated → Σ₃   {destroys data with no backup, regulated ∨ safety-critical ∨ compliance-governed systems, failure = severe incident that cannot be undone}
                                            e.g. drop a production table, delete a storage bucket without snapshots, modify HIPAA-regulated pipelines,
            security ∨ privacy ∨ data integrity → Σ₂   {modifies auth ∨ authorization ∨ encryption, could expose user data ∨ PII, high financial ∨ customer blast radius}
                                            e.g. auth middleware ∨ token validation, payment logic, rate limiting ∨ access controls,
            user-visible ∨ coupled → Σ₁   {alters user-facing behaviour ∨ UI ∨ API responses, noticeable reliability ∨ performance ∨ cost effect, tightly coupled to in-flight tasks}
                                            e.g. API response format, shared config file, function used by several modules,
            else → Σ₀   e.g. rename an internal variable, fix a comment typo, add a unit test for existing logic]
```

## Controls

```
Σ₀ 🟢 Patrol    = {criteria: low blast radius ∧ easy rollback ∧ no sensitive impact,
                   controls: [basic validation evidence, record the rollback step]}
Σ₁ 🟡 Caution   = Σ₀ + {criteria: user-visible ∨ moderate reliability or cost impact ∨ partial coupling,
                   controls: [independent review by a non-author agent, validation + negative test ∨ failure case, explicit rollback note in the task output]}
Σ₂ 🟠 Action    = Σ₁ + {criteria: security ∨ privacy ∨ compliance ∨ data integrity ∨ high blast radius ∨ difficult rollback,
                   controls: [dedicated RCN participation, adversarial review with the failure-mode checklist, ADM go/no-go before merge ∨ release, staged rollout ∨ guarded launch when possible]}
Σ₃ 🔴 Trafalgar = Σ₂ + {criteria: irreversible ∨ regulated ∨ safety-sensitive ∨ failure likely causes a severe incident,
                   controls: [minimal scope ∧ isolate risky changes, ⏸ explicit human confirmation before any irreversible action, two-step verification, documented contingency plan, controls unavailable → ✗ do not execute]}
```

## Failure-Mode Checklist (Σ₁+)

```
checklist = [what could fail in production?, how would we detect it quickly?, what is the fastest safe rollback?, what dependency could invalidate this plan?, what assumption is least certain?]
```

## Marine Deployments

RM inherit the parent ship's Σ: `[Σ₀–Σ₁ CPT discretion, Σ₂ CPT signals ADM ∧ receives approval first, Σ₃ ✗ marines — all Trafalgar work needs explicit Admiralty (human) confirmation]`.

## Plan Mode (Σ₂–Σ₃)

Spawn Σ₂|Σ₃ CPTs with `mode: "plan"` on `Agent`: read-only until ADM approves their plan.

```
Σ₂ = CPT submits via ExitPlanMode → ADM reviews → SendMessage plan_approval_response   (= the go/no-go control)
Σ₃ = same, but ADM obtains ⏸ human confirmation before approving                   (= the human-confirmation control)
Σ₀–Σ₁ = plan mode not required
```

Tool details: `references/tool-mapping.md`.

## Enforcement

⛔ No task executes without a tier (Φ₆). ADM enforces the controls at Ω₇: a task is not `completed` until its tier's evidence is in hand, and the Ω₆ checkpoint records any task that slipped through. There is no hook; the gate is the admiral's discipline, backed by the RCN at Σ₂+ and the human at Σ₃.
