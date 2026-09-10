# Nelson♦Σ

Nelson♦Σ is a token-compressed edition of [Nelson](https://github.com/harrymunro/nelson), a Claude Code skill for coordinating agent work using Royal Navy terminology. It keeps upstream's eight-step framework (Sailing Orders, The Estimate, Battle Plan, Form the Squadron, Get Permission to Sail, Quarterdeck Rhythm, Action Stations, Stand Down), its Python scripts, hooks, and plugin packaging, and rewrites the skill markdown in the symbolic notation from [CursorRIPER♦Σ](https://github.com/johnpeterman72/CursorRIPER.sigma).

## Key references

- **[skills/nelson/references/sigma-legend.md](./skills/nelson/references/sigma-legend.md)** — the symbol vocabulary every skill file uses
- **[docs/project_structure.md](./docs/project_structure.md)** — full repository layout
- **[README.md](./README.md)** — user-facing overview and quick start

## Notation rules

When editing anything under `skills/nelson/`:

- Use only symbols defined in `sigma-legend.md`. Add a new symbol there first if one is needed.
- Symbols stand in for recurring phrases (Ω₆ for "Step 6", Σ₂ for "Station 2", ADM/CPT/RCN for roles, ND/NP/NCS for the script commands). Rules, thresholds, flags, JSON keys, tool names, file paths, and template field labels stay literal.
- Compression comes from cutting explanatory prose and repetition, not from dropping rules. Greek letters and math operators cost about the same number of tokens as the words they replace; the script aliases and role abbreviations are the real savings.
- Inside fenced code blocks (templates, JSON, shell) write the full literal form, never an alias.
- Merging from upstream: fetch `upstream/main`, merge, then re-compress any changed prose in the touched files rather than keeping both forms.

## Coupled literals

Python code parses these markdown structures. Keep them exactly:

- `SKILL.md`: the eight `## N. Title` headings in order, the `## Standing Orders` heading and its two-column table with `` `references/standing-orders/<slug>.md` `` cells, the phrases `SAILING_ORDERS to ESTIMATE`, `ESTIMATE to BATTLE_PLAN`, `skip-estimate`, `--reason`, the word `elegant` before Step 1, and the ```` ```! ```` auto-exec block.
- `admiralty-templates/turnover-brief.md`: every section label the `brief-validate` hook checks (`Ship:`, `Role:`, `Running plot`, and the rest).
- `admiralty-templates/battle-plan.md`: the `- Ship (if crewed):` and `- File ownership (if code):` lines parsed by the conflict scan.
- `admiralty-templates/damage-report.md`: JSON keys emitted by `scripts/count-tokens.py`.
- Every `` `references/...md` `` path cited anywhere: `scripts/check-references.sh` must print OK.

## Maintainability sensors

This repo has automated sensors that you (the agent) should run and read
carefully before considering a task done. They're how the codebase gives you
fast feedback about whether your changes are maintainable.

### Active sensors

- **Linter** — `ruff check`. Config in `pyproject.toml`. AI-targeted
  thresholds (`max-args=5`, `max-branches=10`, `max-statements=50`,
  `max-complexity=10`, `line-length=120`). Each violation message is
  feedback — read it, don't just look at the line. Many rules accept a
  suppress-with-reason or threshold-bump instead of forcing a fix.
- **Formatter** — `ruff format --check`. Apply with `ruff format` before
  committing.
- **Tests** — run separately per directory because each has its own
  `conftest.py`:

  ```
  pytest skills/nelson/scripts/ -v
  pytest hooks/ -v
  pytest scripts/ -v
  ```

- **Markdown** — `npx markdownlint-cli2 "**/*.md"` with the repo config.
- **References** — `bash scripts/check-references.sh`.
- **Pre-commit** — `pre-commit run --all-files`. Includes secret scanning
  (Gitleaks), Ruff, and standard hygiene. Install once with
  `pre-commit install`. If a hook fails, fix the underlying issue rather
  than passing `--no-verify`.
- **CI** — the same checks re-run on clean infra after push (see
  `.github/workflows/ci.yml`). Green locally + red in CI usually means
  environment drift (Python version, env vars, OS-specific paths).

### Suppressing or bumping a sensor

You may suppress a rule or bump a threshold when the rule is clearly wrong
for the situation. **The reason is mandatory.**

```python
result = subprocess.run(  # noqa: S603 -- args are repo-internal paths, sys.executable is trusted
    [sys.executable, str(_SCRIPT), ...],
    ...,
)
```

Naked suppressions (no `--` reason) are not acceptable — they're noise and
they hide the next regression. If you find existing naked suppressions,
treat them as a mini code-review task: add a reason or remove the
suppression.

If a threshold (`max-complexity`, `max-args`, `line-length`, etc.) needs to
go up project-wide, change it in `pyproject.toml` and add a comment
explaining the trade-off. Don't disable the rule entirely — leaving the
rule active means it will catch the *next* drift.

### Brownfield complexity backlog

The largest functions carry `# noqa: C901, PLR0912, PLR0915` suppressions
pointing to **beads issue `nelson-e6j`** (an upstream tracker). When you touch one of those
functions, check whether your edit reduces complexity below the
threshold. If yes, drop the noqa. If no, leave the noqa in place — the
refactor stays opportunistic, not blocking.

### When sensors disagree with you

Sensors are heuristics, not laws. If you genuinely believe a rule is wrong
for a particular file or function, propose the threshold change or
per-directory override explicitly — don't reach for a suppression as a
shortcut. Discussion in a comment / PR description beats a silent disable.
