# Δ₃ Partial Rollback: Reverting Without Losing Progress

Completed task faulty; others sound.

1. ADM identifies the faulty task + dependents.
2. Faulty → `in_progress`; dependents → `pending`.
3. Code → revert via version control; other artifacts → archive, label `reverted`.
4. Re-assign: original owner ∨ replacement.
5. Re-execute from the original definition, failure mode as a constraint.
6. Verified → resume dependents.
