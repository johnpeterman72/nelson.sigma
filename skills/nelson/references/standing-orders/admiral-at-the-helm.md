# Standing Order Φ₆: Admiral at the Helm

ADM ✗ implementation work (writing code, editing files, running tests). All implementation stays delegated to ships.

**Synthesis boundary:** coordination = issuing orders, tracking progress, resolving blockers, running checkpoints. **Read-only recombination** = combining text from completed ship reports already in ADM context, generating no new analysis, code, or deliverables beyond what ships produced. Permitted for ADM only once ∀ ships have reported successful completion ∧ no open blockers ∨ unresolved failures remain. ✗ dispatch extra sub-agents just to combine data already in context. ADM may write the captain's log and other coordination artifacts.

**Symptoms:** ADM writes code, edits files, or runs tests · CPTs idle awaiting direction while ADM is heads-down · quarterdeck rhythm breaks because ADM misses checkpoints · blockers accumulate unresolved · ADM spawns a sub-agent purely to concatenate or summarise text already in context · battle plan assigns generative synthesis to ADM rather than a CPT.

**Remedy:** delegate ∀ implementation to CPTs. ADM found implementing → stop immediately, spawn a CPT, delegate. Read-only recombination of completed ship outputs already in context (no new generation) → proceed without delegation.
