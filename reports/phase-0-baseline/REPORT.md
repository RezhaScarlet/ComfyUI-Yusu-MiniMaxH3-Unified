# Phase 0 — Baseline & Branch

**Date:** 2026-10-17
**Branch:** `i18n/english` (created from `eager-nebula` worktree HEAD `4421a91`)

## Goal

Establish a green baseline before any translation so that every later phase can
prove it did not change behavior, only language.

## Environment setup

The host Python had **no** `torch` / `numpy` / `Pillow`, so the backend suite could
not run. An isolated virtual environment was created for verification only (not
committed):

```
C:\temp\opencode\venv-h3   (Python 3.13.15)
  numpy 2.5.4
  pillow
  torch 2.14.1+cpu
```

`node --version` = v24.18.0.

## Baseline results

| Check | Command | Result |
|---|---|---|
| Backend tests | `python -m unittest discover -s tests -v` | **28 tests, OK** |
| Frontend tests | `node tests/test_frontend.mjs` | **frontend tests passed** |
| Syntax | `python -m py_compile __init__.py media.py nodes.py` | **OK** |

## File encoding facts

All tracked text files are **UTF-8 without BOM** and use **CRLF** line endings:

| File | Ending | Size |
|---|---|---|
| `__init__.py` | CRLF | 2016 |
| `media.py` | CRLF | 29042 |
| `nodes.py` | CRLF | 23303 |
| `README.md` | CRLF | 17477 |
| `web/minimax_h3_unified.js` | CRLF | 73512 |
| `tests/test_plugin.py` | CRLF | 33160 |
| `tests/test_frontend.mjs` | CRLF | 9850 |
| `workflows/H3全能工作流.json` | CRLF | 44766 |

> Consequence: translation edits must preserve CRLF and UTF-8 encoding. All
> bulk replacement is done through a script that keeps `newline=''`, so line
> endings are never rewritten.

## "byte freeze" audit (from planning)

No freeze/hash/snapshot mechanism exists in the repo (the only `hash` token is
`"anomalous_hashes": {}` inside workflow metadata). The real "frozen" couplings
are literal strings, all accounted for in the phase plan:

1. `@图片/@视频/@音频` prompt-mention contract shared by `nodes.py` and the JS.
2. Test assertions matching Chinese error messages byte-for-byte.
3. `test_release_has_no_background_polling_or_telemetry` asserting ASCII code
   snippets inside the JS/backend files.
4. Workflow JSON `type` / widget `name` / `id` / `media_state` fields that must
   stay ASCII and unchanged.

## Status: PASSED

Proceed to Phase 1 (backend messages).
