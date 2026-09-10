# Standing Order Φ₁₇: Pulling the Oar

Senior agent (ADM ∨ CPT) ✗ absorb a failed subordinate's work into its own context. Subagent fails → fix the brief, re-dispatch.

**Trigger:** a dispatched subagent (Explore, CPT, RM, crew) returns an error, hits a prompt-length limit, returns insufficient or malformed output, or times out.

**! Anti-pattern:** senior absorbs the subagent's intended work: reads the files the Explore was to summarise, runs the tests the RM was to run, builds the artifact the CPT was to deliver. Senior context fills with raw inputs that should have been pre-digested.

**Symptoms:** senior reasons over raw file contents a subagent could have summarised · senior says a variant of "I'll just do this directly — faster" · context consumption spikes right after a subagent failure · the same dispatch is not re-attempted with a fixed brief.

**Remedy — fix the brief, don't take the oar:**

1. Stop. ✗ absorb the work.
2. Diagnose: brief too broad, prompt too long, output format ambiguous, wrong agent type, tool unavailable.
3. Re-brief: split into several focused subagents, tighten scope, ∨ change agent type. Explore failures → several narrow Explores, ¬ one laundry-list dispatch; require structured summaries, ¬ raw file contents.
4. Re-dispatch.
5. Two failed re-briefs on one task → escalate to the user; ✗ absorb on the third attempt.

**Not a violation:** read-only recombination of subagent results already in context (`admiral-at-the-helm.md`).

**Related:** `admiral-at-the-helm.md` (ADM does implementation) · `captain-at-the-capstan.md` (CPT does crew work).
