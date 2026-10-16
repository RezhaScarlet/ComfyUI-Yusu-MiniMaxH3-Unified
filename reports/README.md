# Translation review reports

Branch: `i18n/english` · Goal: translate the plug-in (Chinese → English) without
changing behavior.

Each phase has its own subfolder with a `REPORT.md`:

| Phase | Folder | Status |
|---|---|---|
| 0 — Baseline & branch | `phase-0-baseline/` | PASSED |
| 1 — Backend strings & comments | `phase-1-backend/` | PASSED |
| 2 — Frontend UI strings | `phase-2-frontend/` | PASSED |
| 3 — Test updates | `phase-3-tests/` | PASSED |
| 4 — README | `phase-4-readme/` | PASSED |
| 5 — Example workflow | `phase-5-workflow/` | PASSED |
| 6 — Final verification | `phase-6-verification/` | PASSED |

## Key decisions

- English-only output, but the prompt mention aliases `@图片` / `@视频` / `@音频`
  remain parseable (and are asserted by tests) so prompts and workflows saved
  before the change keep working.
- Only human-readable text changed. All identifiers stayed frozen: node `type`,
  widget/socket `name`, workflow `id`s, `media_state` paths, version constants.

## How to reproduce the checks

```bash
python -m unittest discover -s tests -v
node tests/test_frontend.mjs
python -m py_compile __init__.py media.py nodes.py
node --check web/minimax_h3_unified.js
```
