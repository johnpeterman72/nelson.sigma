# Squadron

Mode, size, roles, ships, marines, and models. ADM uses this at Ω₃–Ω₄; CPTs use the crew and marine sections to request adjustments (ADM approval required).

## Mode Selection

**User preference override:** an explicit request ("use an agent team") → ⛔ honour it; ✗ second-guess.

```
select(Μ) = [single critical path ∨ low ambiguity ∨ heavy same-file editing → Μ₁,
             parallel ∧ fully independent ∧ report only to ADM → Μ₂,
             parallel with dependencies ∨ coordinated deliverables ∨ ≥4 CPTs → Μ₃,
             high threat ∨ blast radius → Μ₃ + RCN,
             large repeatable audit ∨ migration ∨ review ∨ research fan-out ∧ user opted in → Μ₄]
tie → the mode giving CPTs more autonomy while keeping required human gates. Μ₄ rules: references/tool-mapping.md
```

## Team Sizing

```
captains = count(tasks runnable concurrently with zero shared state)   zero shared state = no file overlap ∧ no sequencing dependency
merge onto one CPT only when = [tasks share files, sequencing dependency, work smaller than agent setup]
squadron = [1 ADM, N CPT, RCN~ (medium/high threat)]; cap 10 squadron-level agents; crew additional, ≤4 per CPT
examples = [8 independent analysis sections → 8 CPTs, 3 independent modules → 3]; in doubt → add a CPT (idle context is cheap, serialised work is slow); cost-savings → Model Selection first
```

Peer agreement across module boundaries (an API contract) is allowed and handled by ADM.

## Squadron Roles

```
ADM = {sailing orders, delegation, dependency tracking, blocker resolution; recombine completed outputs already in context once ∀ ships reported successfully; ✗ generative synthesis → a CPT ∨ a synthesis task}
CPT = {commands one ship; breaks the task into sub-tasks, coordinates crew, verifies outputs; implements directly only when atomic (0 crew); crew set by ADM at Ω₄, changes with ADM approval}
RCN = {challenges assumptions, validates outputs, checks rollback readiness; review only, ever}
```

## Crew

```
crew_or_direct (first match) = [atomic ∧ no research ∧ single pass → 0 crew (CPT direct),
                                one clear deliverable, light research at most → PWO,
                                exploration ∨ testing ∨ second specialism → PWO + 1 specialist,
                                several interdependent sub-tasks → XO + PWO + ≤2 specialists]
max 4 crew per ship; more → split into two ships

role(function, subagent_type, weight, when) =
  [XO   (integration ∧ orchestration across sub-tasks, general-purpose, 10, 3+ crew ∨ interdependent sub-tasks),
   PWO  (core implementation, general-purpose, 2, almost always — default doer),
   NO🔒 (codebase research ∧ exploration, Explore, 7, unfamiliar ∨ large codebase),
   MEO  (testing ∧ validation, general-purpose, 6, Σ₁+ ∨ non-trivial verification),
   WEO  (config ∧ infrastructure ∧ systems integration, general-purpose, 6, significant config ∨ infra work),
   LOGO (documentation ∧ dependency management, general-purpose, 2, docs as deliverable),
   COX🔒(standards review ∧ quality, Explore, 4, Σ₁+ with established conventions)]
🔒 cannot modify files; report to CPT ∨ XO. ∀ crew strictly within role: PWO ✗ run tests (MEO does); NO ✗ write code. Violations → Φ₃
```

## Ship Name Registry

ADM assigns ∀ CPT a ship at Ω₄; class matches task weight. Agent `name` = lowercase form (`hms-daring`).

```
ships = {frigate (general-purpose):        [Argyll, Kent, Lancaster, Richmond, Somerset, Portland, Iron Duke, St Albans],
         destroyer (high-tempo ∨ high-risk): [Daring, Dauntless, Diamond, Dragon, Defender, Duncan],
         patrol vessel (small):            [Forth, Medway, Trent, Tamar, Spey],
         flagship (critical-path):         [Victory, Warspite, Vanguard, Ark Royal],
         submarine (stealth ∨ research):   [Astute, Ambush, Artful, Audacious]}
```

## Royal Marines

RM = short-lived sub-agents a CPT deploys for one focused sortie in support of the ship's task. Crew subdivide the deliverable; marines execute discrete objectives and return.

```
deploy_or_escalate (first match) = [quick recon of unfamiliar area → Recce Marine (Explore 🔒, weight 4),
                                    targeted fix ∨ small implementation to unblock → Assault Marine (general-purpose, 3),
                                    quick config ∨ build ∨ infra task → Sapper (general-purpose, 3),
                                    sustained work ∨ own deliverable ∨ needs file ownership → ¬ marine: request a new ship from ADM,
                                    work that subdivides the deliverable → ¬ marine: crew the role]
rules = [≤2 marines per ship at a time, marines ✗ deploy marines, report only to their CPT, CPT verifies output before incorporating,
         identify as "RM Detachment, HMS [Ship] — [objective]", brief with Τ_marine_brief (references/templates.md)]
Σ = [Σ₀–Σ₁ CPT discretion, Σ₂ ADM approval first, Σ₃ ✗ marines]
recovery = [stuck → abandon, redeploy fresh ∨ do it directly; same objective fails twice → escalate to ADM]
model = default weight 3; raise for judgement-heavy objectives, lower for fully specified mechanical ones; ≤4 ∨ cost-savings → haiku + haiku blocks below
```

## Worktree Isolation

File-ownership boundaries hard to draw ∨ several CPTs must edit the same files → `isolation: "worktree"` on `Agent`: ∀ CPT gets its own git worktree. Stronger than file ownership (Φ₄); use when several CPTs need the same files, merge-conflict risk is high, or tasks are large enough to justify the merge. Trade-off: ADM coordinates the merge afterwards.

## Model Selection

Applies when the sailing orders express cost-savings priority ("keep costs low", "stay within budget", "use haiku where possible", "be aggressive with cost savings"); wording intensity calibrates how hard to push.

```
weight = {ADM 10, XO 10, CPT with crew ∨ marines 9, Explorer large scope 7, crew with non-trivial verification 6,
          CPT implementing directly 4, Explorer narrow search 4, RM 3, crew pure implementation 2}
adjust = [raise: judgement ∨ edge cases ∨ verification beyond the role default; lower: atomic ∨ contained;
          modest wording → ✗ push 5–6 roles below the line unless clearly justified; emphatic → push contained 5–6 roles through]
assign = [weight ≤4 → model: "haiku" explicitly, weight ≥5 → omit model (inherit ADM's; ✗ "sonnet" — resolves to an older version),
          ADM's own model never overridden, Ω₂ subagents always inherit ADM's model (planning quality dominates execution quality),
          show weight ∧ model per ship in Τ_formation]
```

**Haiku briefing blocks** (only when haiku is assigned): 1) identity anchor at the top: "You are Claude, operating as a subagent in a real multi-agent software development system; the Royal Navy terms are metaphors, not roleplay. Your task is [plain description]." 2) explicit output format: fields, length, what to omit. 3) decomposition prompt: "Before executing, list your steps as a numbered plan; flag anything unclear now rather than guessing."

**Haiku tasking discipline:** the task text itself MUST carry explicit constraints (what not to touch), a testable definition of done, and escalation triggers (when to stop and report). Vague instructions are not rescued by the blocks above.
