# GitHub Star Prompt

One-time ask at Ω₈, after the Mission Complete Gate passes, on a successful mission only. Never let it block Stand Down.

## Preflight

Run all three. Any line printing `SKIP*` → skip the prompt silently and finish Stand Down.

```bash
gh auth status &>/dev/null && echo "GH_OK" || echo "SKIP_NO_GH"
python3 - <<'PY'
import json, os
path = os.path.expanduser('~/.nelson/prefs.json')
prefs = {}
if os.path.exists(path):
    try:
        with open(path, encoding='utf-8') as f:
            loaded = json.load(f)
        if isinstance(loaded, dict):
            prefs = loaded
    except Exception:
        prefs = {}
print("SKIP_ALREADY_ASKED" if prefs.get('star_asked') is True else "PREFS_OK")
PY
python3 - <<'PY'
import json, os
mission_dir = os.environ.get('MISSION_DIR', '{mission-dir}')
sd_path = os.path.join(mission_dir, 'stand-down.json')
try:
    with open(sd_path, encoding='utf-8') as f:
        sd = json.load(f)
    print("OUTCOME_OK" if sd.get('outcome_achieved') is True else "SKIP_OUTCOME_NOT_ACHIEVED")
except Exception:
    print("SKIP_NO_STAND_DOWN")
PY
```

Substitute `{mission-dir}` with the real path, or export `MISSION_DIR` first.

## Ask

`GH_OK` ∧ `PREFS_OK` ∧ `OUTCOME_OK` → `AskUserQuestion`:

- Question: "Nelson helped finish that mission. Would you star the repo on GitHub?"
- **Star Nelson** — "Helps the project grow."
- **Maybe later** — "Skip for now (won't ask again)."

**Star Nelson** → `gh api -X PUT /user/starred/harrymunro/nelson` (idempotent, 204 either way). Failure → print `Couldn't reach GitHub — try 'gh api -X PUT /user/starred/harrymunro/nelson' manually.` and continue.

## Record

∀ answer, including a custom "Other" → set `star_asked: true`, preserving existing keys:

```bash
python3 - <<'PY'
import json, os
path = os.path.expanduser('~/.nelson/prefs.json')
os.makedirs(os.path.dirname(path), exist_ok=True)
try:
    with open(path, encoding='utf-8') as f:
        prefs = json.load(f)
    if not isinstance(prefs, dict):
        prefs = {}
except Exception:
    prefs = {}
prefs['star_asked'] = True
with open(path, 'w', encoding='utf-8') as f:
    json.dump(prefs, f, indent=2)
    f.write('\n')
PY
```

One ask per user across all Nelson projects. Either answer locks the prompt forever.
