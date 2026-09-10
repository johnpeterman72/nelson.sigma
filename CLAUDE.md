# Nelson♦Σ

Nelson♦Σ is a zero-dependency edition of [Nelson](https://github.com/harrymunro/nelson), a Claude Code skill for coordinating agent work using Royal Navy terminology. It keeps upstream's eight-step doctrine (Sailing Orders, The Estimate, Battle Plan, Form the Squadron, Get Permission to Sail, Quarterdeck Rhythm, Action Stations, Stand Down), drops upstream's Python scripts and hooks in favour of native Claude Code tools, and writes the skill in the symbolic notation of [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma).

## Key references

- **[skills/nelson/references/sigma-legend.md](./skills/nelson/references/sigma-legend.md)** — the symbol vocabulary every skill file uses
- **[skills/nelson/references/tool-mapping.md](./skills/nelson/references/tool-mapping.md)** — which native tool each Nelson operation runs on
- **[docs/project_structure.md](./docs/project_structure.md)** — repository layout
- **[README.md](./README.md)** — user-facing overview and quick start

## Design rules

- **No code.** Mission state is three markdown files per mission plus `.nelson/memory.md`. Tracking uses the task list, isolation uses worktrees, planning uses plan mode, persistence uses `/goal`, learning uses memory. If a change needs a script or a hook, it belongs upstream, not here.
- **Symbols replace phrases, never rules.** Use only symbols defined in `sigma-legend.md`; add a new one there first. Thresholds, tool names, parameters, paths, and template labels stay literal.
- **Terseness is the saving.** Greek letters and math operators cost about as many tokens as the words they replace; cut prose and repetition instead of adding glyphs. Judge changes with a tokenizer, not byte counts.
- **Structure is arrays.** Field lists, checklists, procedures, and tables are written in the array notation defined in `sigma-legend.md` (`[a, b]`, `{f: v}`, `g[{f}]`, `f~`, tuples with positional meaning stated once). Use named members, never numeric index slices: `+{Explore, plan mode}` is cheaper and safer to follow than `+𝕋[0:1]`.
- **Inside code fences, write the literal form** (full tool names, full template labels); captains read briefs without the legend.
- **Keep every `references/...md` path cited from SKILL.md resolvable.** `scripts/check-references.sh` must print OK.

## Sensors

Run before considering a task done:

```
npx markdownlint-cli2 "**/*.md"      # repo config in .markdownlint-cli2.yaml
bash scripts/check-references.sh     # every cited reference exists and is cited
```

CI runs the same two plus YAML lint, link check, and spell check (`.github/workflows/ci.yml`). Green locally and red in CI usually means a path or link that only resolves on this machine.

## Syncing with upstream

Upstream doctrine changes (a new standing order, a changed gate) are merged by hand: read the upstream diff, fold the rule into the matching consolidated file, re-run the sensors. Upstream code changes are ignored by design. The last faithful, script-bearing edition is tag `v2.4.0-sigma`.
