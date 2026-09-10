# Δ₁₀ Session Hygiene: Clean Start Procedure

ADM runs this at Ω₁ of a new session, before forming the squadron ∨ launching ships.

## Directory Structure

∀ mission has its own timestamped directory; previous missions persist automatically, ¬ archive, ¬ delete.

```
.nelson/missions/{YYYY-MM-DD_HHMMSS}_{SESSION_ID}/
  captains-log.md         — Written at stand-down
  quarterdeck-report.md   — Updated at every checkpoint
  damage-reports/         — Ship damage reports (JSON)
  turnover-briefs/        — Ship turnover briefs (markdown)
```

## New Session

1. Confirm ¬ a resumption (resuming → Resumed Session below, skip this).
2. Verify `ND init` (Ω₁, "Structured Data Capture") ran: creates the mission directory, `damage-reports/` + `turnover-briefs/`, the three initial JSON files, and `.nelson/.active-{SESSION_ID}` in one step. `{mission-dir}` = the path printed.
3. Done → form the squadron.

## Resumed Session

1. ? SESSION_ID known → `.nelson/.active-{SESSION_ID}` → `{mission-dir}`. : list `.nelson/missions/` → ⏸ user picks.
2. `{mission-dir}/damage-reports/` → hull per ship.
3. `{mission-dir}/turnover-briefs/` → task state.
4. Then `damage-control/session-resumption.md`.

## Rotated Report Files

`quarterdeck-report-[0-9]*.md` and `captains-log-N.md` (`-0`, `-1`, …) are checkpoint history. Rotation: before a new write, rename the existing file to `-N.md`, N = highest existing + 1 (0 if none). ✗ delete: each mission's timestamped directory keeps them as its permanent record. Read them to review history.

## Browsing Previous Missions

List `.nelson/missions/` by name (= chronological).
