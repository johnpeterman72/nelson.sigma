# Contributing

Thanks for your interest in contributing to Nelson♦Σ.

## How to contribute

1. Fork the repo and create a branch from `main`
2. Make your changes
3. Open a pull request

## What to contribute

Bug fixes, tighter compression of the skill text, template improvements, documentation fixes, and new ideas are all welcome. Doctrine changes (new standing orders, new procedures, workflow changes) are best proposed upstream at [harrymunro/nelson](https://github.com/harrymunro/nelson) first, then compressed here; that keeps the two editions in step. If you're thinking about a larger change, open an issue first so we can discuss it.

## Skill structure

The skill lives in `skills/nelson/`. The key files:

- `SKILL.md` — Main skill instructions (the entrypoint Claude reads), in Nelson♦Σ notation
- `references/sigma-legend.md` — The symbol vocabulary; read this before editing anything else
- `references/` — Supporting docs loaded on demand (risk tiers, templates, team sizing)
- `scripts/` — Python scripts run by the skill at mission time (unchanged from upstream)
- `agents/` — Agent interface definitions

## Notation

See [CLAUDE.md](./CLAUDE.md) → *Notation rules* and *Coupled literals*. In short: symbols replace recurring phrases, never rules; anything a Python script parses stays literal; code fences hold the full command, never an alias.

## Local development

The repo ships AI-targeted sensors (linter, formatter, pre-commit hooks,
secret scanner) so the same checks run locally and in CI. Set them up
once after cloning:

```bash
# Install pre-commit hooks (runs on every `git commit`)
pre-commit install
```

Day-to-day commands:

```bash
ruff check                              # Lint with AI-targeted thresholds
ruff format                             # Apply formatting
pre-commit run --all-files              # Run every hook on every file
pytest skills/nelson/scripts/ -v        # Tests — one directory at a
pytest hooks/ -v                        #   time (each dir has its
pytest scripts/ -v                      #   own conftest.py)
npx markdownlint-cli2 "**/*.md"         # Markdown lint with the repo config
bash scripts/check-references.sh        # Every cited references/ path must exist
```

See [CLAUDE.md](./CLAUDE.md) → *Maintainability sensors* for the
suppress-with-reason and bump-threshold conventions. If a sensor
disagrees with you, propose the threshold change rather than reaching
for `--no-verify`.

## Syncing with upstream

```bash
git remote add upstream https://github.com/harrymunro/nelson.git   # once
git fetch upstream
git merge upstream/main
```

After merging, re-compress the prose that arrived in any touched skill file rather than leaving the two styles side by side, then run the sensors above.

## Guidelines

- Keep things simple and clear
- Test your changes by installing the skill locally and running a mission
- Follow the existing style and tone
