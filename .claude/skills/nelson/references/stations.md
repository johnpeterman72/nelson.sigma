# Σ Station Tiers

## Risk → Station (?first yes = tier)

?irreversible|regulated|severe-incident → Σ₃ 🔴
?security|privacy|data-integrity|high-blast → Σ₂ 🟠
?user-visible|reliability|coupled → Σ₁ 🟡
?else → Σ₀ 🟢

## Controls (cumulative)

Σ₀ 🟢: validate + rollback_note
Σ₁ 🟡: Σ₀ + non-author review + negative test
Σ₂ 🟠: Σ₁ + REV review + failure checklist + COORD go/no-go + staged rollout(~)
Σ₃ 🔴: Σ₂ + human confirm + 2-step verify + contingency plan
  !unavailable controls → do not execute; min scope + isolate

## Failure Checklist (Σ₁+)

?[prod failure scenario, detection method, fastest rollback, dep invalidation, least-certain assumption]
