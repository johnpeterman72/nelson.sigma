# Action Stations

Classify ∀ task before execution. Apply the minimum controls for its tier. Controls are cumulative: each tier includes everything below it.

## Classification (first yes wins)

1. Irreversible ∨ regulated? → **Σ₃**
    - destroys data with no backup · touches regulated, safety-critical, or compliance-governed systems · failure = severe incident that cannot be undone
    - e.g. drop a production table, delete a storage bucket without snapshots, modify HIPAA-regulated pipelines
2. Security ∨ privacy ∨ data integrity? → **Σ₂**
    - modifies auth, authorization, encryption · could expose user data or PII · high financial or customer blast radius
    - e.g. auth middleware or token validation, payment logic, rate limiting or access controls
3. User-visible ∨ coupled? → **Σ₁**
    - alters user-facing behaviour, UI, or API responses · noticeable reliability, performance, or cost effect · tightly coupled to in-flight tasks
    - e.g. API response format, shared config file, function used by several modules
4. None → **Σ₀**
    - e.g. rename an internal variable, fix a comment typo, add a unit test for existing logic

## Controls

| Σ | Criteria | Required controls |
|---|---|---|
| Σ₀ 🟢 Patrol | low blast radius · easy rollback · no sensitive, security, or compliance impact | basic validation evidence · record rollback step |
| Σ₁ 🟡 Caution | user-visible change · moderate reliability or cost impact · partial coupling | + independent review by a non-author agent · validation + negative test or failure case · explicit rollback note in task output |
| Σ₂ 🟠 Action | security, privacy, compliance, data integrity · high customer or financial blast radius · difficult rollback or uncertain side effects | + dedicated RCN participation · adversarial review with failure-mode checklist · ADM go/no-go before merge or release · staged rollout or guarded launch when possible |
| Σ₃ 🔴 Trafalgar | irreversible · regulated or safety-sensitive · failure likely causes a severe incident | + minimal scope, isolate risky changes · ⏸ explicit human confirmation before any irreversible action · two-step verification · documented contingency plan · controls unavailable → ✗ do not execute |

## Failure-Mode Checklist (Σ₁+)

- What could fail in production?
- How would we detect it quickly?
- What is the fastest safe rollback?
- What dependency could invalidate this plan?
- What assumption is least certain?

## Marine Deployments

RM inherit the parent ship's Σ.

- Σ₀–Σ₁ → CPT deploys at discretion
- Σ₂ → CPT signals ADM ∧ receives approval first
- Σ₃ → ✗ no marines; all Trafalgar work needs explicit Admiralty (human) confirmation

## Plan Mode (Σ₂–Σ₃)

Spawn CPTs for Σ₂|Σ₃ tasks with `mode: "plan"` on `Agent`: read-only until the ADM approves their plan.

- Σ₂ → CPT submits via `ExitPlanMode` → ADM reviews, approves with `SendMessage(type="plan_approval_response")` (= the go/no-go control)
- Σ₃ → same, but ADM obtains ⏸ human confirmation before approving (= the human-confirmation control)
- Σ₀–Σ₁ → plan mode not required

Tool details: `references/tool-mapping.md`.

## Advanced: TaskCompleted Hook

`hooks/hooks.json` ships a `TaskCompleted` hook that enforces these gates deterministically: it checks validation evidence, rollback notes, failure cases, and red-cell review by tier, and exits 2 with specific feedback to reject an incomplete task. It supplements, not replaces, the ADM's quarterdeck checkpoint and red-cell review.
