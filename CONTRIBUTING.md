# Contributing

Thanks for your interest in contributing to Nelson♦Σ.

## How to contribute

1. Fork the repo and create a branch from `main`
2. Make your changes
3. Open a pull request

## What to contribute

Tighter compression of the skill text, clearer templates, documentation fixes, and mappings to new Claude Code capabilities are all welcome. Doctrine changes (new standing orders, new procedures, workflow changes) are best proposed upstream at [harrymunro/nelson](https://github.com/harrymunro/nelson) first, then folded in here. Anything that needs a script, a hook, or a runtime dependency is out of scope by design; see [CLAUDE.md](./CLAUDE.md).

## Skill structure

The skill lives in `skills/nelson/`:

- `SKILL.md` — the entrypoint Claude reads, in Nelson♦Σ notation
- `references/sigma-legend.md` — the symbol vocabulary; read this before editing anything else
- `references/` — seven supporting files loaded on demand: squadron, action stations, tool mapping, standing orders, damage control, templates, the Estimate

## Local checks

```bash
npx markdownlint-cli2 "**/*.md"      # markdown lint with the repo config
bash scripts/check-references.sh     # every cited references/ path exists and is cited
```

## Guidelines

- Keep things simple and clear
- Test your changes by installing the skill locally and running a mission
- Follow the existing style and tone
