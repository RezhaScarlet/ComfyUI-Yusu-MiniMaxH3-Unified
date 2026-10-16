# Phase 6 — Final verification

**Branch:** `i18n/english`

## Commands and results

| Check | Command | Result |
|---|---|---|
| Backend syntax | `python -m py_compile __init__.py media.py nodes.py` | **OK** |
| Frontend syntax | `node --check web/minimax_h3_unified.js` | **OK** |
| Backend tests | `python -m unittest discover -s tests` | **Ran 28 tests … OK** |
| Frontend tests | `node tests/test_frontend.mjs` | **frontend tests passed** |
| Workflow JSON | `json.loads(...)` + invariant asserts | **valid, invariants hold** |
| Line endings | byte scan | all CRLF, 0 bare LF |
| Residual CJK | `git ls-files` sweep | only intentional aliases/handle (below) |

## Residual CJK allowlist (intentional)

| File | Lines | Why |
|---|---|---|
| `nodes.py` | 3 | `@图片/@视频/@音频` alias tuples (backward compat) |
| `web/minimax_h3_unified.js` | 7 | alias table, `normalize` regexes, editor split regex |
| `tests/test_plugin.py` | 2 | legacy-alias fixture |
| `tests/test_frontend.mjs` | 1 | legacy-alias fixture |
| `README.md` | 3 | channel handle + legacy-alias documentation |
| `workflows/H3_all_in_one_workflow.json` | 1 | channel handle `在下鱼酥` |

Everything else is English.

## Manual smoke checklist (needs a real ComfyUI host)

Not runnable in this environment (no ComfyUI). Recommended when a host is
available:

1. Load `workflows/H3_all_in_one_workflow.json` and confirm notes/labels read in
   English.
2. Open the node panel in all three modes.
3. Upload an image/video/audio and confirm English trim/waveform labels.
4. Type `@` in the prompt: confirm `@image1` chips, and that a legacy `@图片1`
   text also renders as a chip.
5. Run one generation per mode.

## Summary

All translation phases are complete, tests are green, and every residual Chinese
string is a deliberately preserved alias or a proper-noun handle.

## Status: PASSED (code-level); manual UI smoke pending a ComfyUI host
