# Model Selection

Apply when the sailing orders express cost-savings priority. Governs model assignment for ∀ squadron agent.

## Detecting Cost-Savings Intent

Inferred from natural language in the sailing orders or initial prompt:

- "keep costs low", "stay within budget", "budget is a concern"
- "use cheaper models", "use haiku where possible"
- "be aggressive with cost savings", "minimize spend"

Language intensity calibrates adjustment aggressiveness (Hybrid Adjustment below).

## Default Weight Table

| Agent | Default Weight |
|---|---|
| Admiral | 10 |
| XO | 10 |
| Captain (with crew or marines) | 9 |
| Explorer (large scope) | 7 |
| Crew with non-trivial verification | 6 |
| Captain (direct implementation, no crew) | 4 |
| Explorer (narrow/simple search) | 4 |
| Royal Marines | 3 |
| Crew (pure implementation) | 2 |

## Threshold Rule

Cost-savings mode, after adjustment: weight ≤4 → **haiku** · weight ≥5 → inherit **ADM's model**.

## Estimate Phase Carve-Out

Ω₂ (Ε₁–Ε₇) is exempt. ∀ Estimate subagent (Ε₁ Explorer dispatches, Ε₂–Ε₃ and Ε₄–Ε₇ dispatches) inherits ADM's model. Rationale: planning quality dominates downstream execution quality; a weaker model yields poorer terrain assessments, looser commander's guidance, weaker acceptance criteria, which the squadron carries into implementation. Degrading the Estimate to save tokens is a false economy.

- omit `model:` on the `Agent` call → subagent inherits ADM's model
- ✗ the haiku briefing enhancement blocks below (conditional on haiku assignment, which never occurs in Ω₂)
- cost-savings adjustment resumes at Ω₃ and Ω₄, where it applies normally

## Hybrid Adjustment

The tasking agent adjusts default weights before assignment:

- **raise** when the task involves judgement, edge cases, or verification beyond the role default
- **lower** when the task is more atomic or contained than the role default suggests

Scale to intensity of the request:

- modest ("keep costs low") → modest pressure; ✗ push roles at 5–6 below the threshold unless clearly justified
- emphatic ("be aggressive") → willing to push roles normally at 5–6 through the threshold when the task is contained

No hard bounds; ADM judgement.

## Model Assignment Rules

- ADM's model is **never overridden**
- weight ≥5 → inherit ADM's model: **omit the `model` parameter entirely** in the Task tool call. ✗ `"sonnet"`: the alias resolves to an older version and does not match ADM's model
- weight ≤4 → always `model: "haiku"` explicitly
- display weight and assigned model in the squadron formation summary alongside ship names and tasks

## Briefing Enhancements (haiku agents only)

Whenever any tasking agent (ADM, CPT, or crew) assigns haiku to a subordinate, add three blocks to that agent's briefing:

### 1. Identity Anchor (top of briefing)

> You are Claude, operating as a subagent in a real multi-agent software development system. The Royal Navy terms used for coordination (admiral, captain, crew, etc.) are metaphors — this is not roleplay. Your task is [plain-language description of role].

### 2. Explicit Output Format

State exactly what to return: format, required fields, length, what to omit. Remove ambiguity. Example:

> Return a JSON object with keys `status`, `summary`, and `files_changed`. Do not include implementation reasoning or next steps.

### 3. Task Decomposition Prompt

> Before executing, list your steps as a numbered plan. If any step is unclear, flag it now rather than guessing.

Conditional on haiku assignment: ✗ in standard (non-cost-savings) briefings ∨ briefings for agents on ADM's model.

## Tasking Agent Discipline (haiku agents only)

Whoever writes the task (ADM, CPT, or crew) compensates for reduced inferencing capacity by making the task itself precise. Vague instructions are not rescued by the blocks above. ∀ haiku task description MUST include:

### Explicit Constraints

What the agent must not do, must not touch, must stay within. ✗ rely on the agent inferring scope limits from context.

> Example: "Only read files under `src/auth/`. Do not modify any file. Do not follow imports outside that directory."

### Definition of Done

A concrete, testable condition that signals completion. ✗ open-ended outcomes.

> Example: "Done when you have returned a JSON list of all public method names in `JwtMiddleware`. Stop after that — do not analyze their bodies."

### Escalation Triggers

Specific conditions under which the agent stops and reports rather than proceeds. ✗ expect haiku to self-identify when out of depth.

> Example: "If the file does not exist, or if you find more than one class matching that name, stop and report what you found. Do not guess which one to use."

Conditional on haiku assignment; standard briefings for agents on ADM's model do not need this level of prescription.
