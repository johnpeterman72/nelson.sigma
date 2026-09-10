# Project Structure

```
.claude-plugin/
  plugin.json             — Plugin manifest
  marketplace.json        — Marketplace definition (self-hosted)
settings.json             — Plugin default settings (enables agent teams)
.github/workflows/ci.yml  — Markdown, YAML, link, spelling, and cross-reference checks
skills/nelson/
  SKILL.md                — Main entrypoint (what Claude reads), Nelson♦Σ notation
  references/             — Supporting docs loaded on demand
    sigma-legend.md         — Symbol legend: Ω Μ Σ Ε Ρ Φ Δ Τ, operators, mission directory
    squadron.md             — Mode selection, team sizing, roles, crew, ship names, marines, model selection
    action-stations.md      — Risk tiers Σ₀–Σ₃, classification, controls, plan mode for Σ₂+
    tool-mapping.md         — Native Claude Code tool per operation; workflows; standing goal; anti-patterns
    standing-orders.md      — Six anti-pattern guards Φ₁–Φ₆
    damage-control.md       — Four recovery procedures Δ₁–Δ₄
    templates.md            — Sailing orders, battle plan, crew briefing, marine brief, quarterdeck report, relief brief, red-cell review, captain's log, memory entry
    the-estimate.md         — 7 Question Maritime Tactical Estimate
agents/
  nelson.md               — Agent definition with skill binding
scripts/
  check-references.sh     — Cross-reference validation for skill links
demos/                    — Example application built with Nelson
docs/                     — This file and images
.nelson/                  — Created at mission time, git-ignored
  memory.md                 — Cross-mission patterns (adopt, avoid, recurring standing orders)
  missions/{stamp}-{slug}/  — battle-plan.md, estimate.md, quarterdeck-report.md, captains-log.md
```
