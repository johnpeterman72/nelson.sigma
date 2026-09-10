# Standing Order Φ₂: Light Squadron

! Grouping independent tasks onto fewer CPTs than their independence warrants.

**Symptoms:** sections, documents, or code areas sharing no files ∧ no sequencing dependency bundled onto one CPT · CPT serialises work that could run concurrently, extending wall-clock time for no gain · fewer CPTs than independent work units · ADM fixes a CPT count before counting parallelisable leaves.

**Remedy:** split bundled tasks onto separate CPTs. CPT count = truly independent work units, bounded by the squadron cap. Ask "maximum tasks runnable concurrently with zero shared state?" → target CPT count.

Bundle onto one CPT only when tasks:

- share files where parallel edits would conflict unresolvably (same functions, tight coupling) — `isolation: "worktree"` when files overlap but merge cost is justified (`squadron-composition.md`), ∨
- have a genuine sequencing dependency (B needs A's output), ∨
- are so small that a separate agent's context-setup cost clearly exceeds the work.

Inverse risk: `standing-orders/crew-without-canvas.md` — ✗ add agents without shortening the critical path.
