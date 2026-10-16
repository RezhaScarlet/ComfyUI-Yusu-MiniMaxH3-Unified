# Phase 5 — Example workflow

**Branch:** `i18n/english` · **Scope:** `workflows/`

## Goal

Rename the workflow file to English and translate only human-readable text,
leaving structural JSON untouched.

## Rename

```
workflows/H3全能工作流.json → workflows/H3_all_in_one_workflow.json
```

Done with `git mv` so history is preserved.

## What changed (84 diff lines)

Translated only `title` / `label` / `matchTitle` values, rgthree note text, group
titles and the example prompt:

| Before | After |
|---|---|
| `"label": "降噪输出"` | `"label": "Denoised output"` |
| `"title": "报错就关了！"` | `"title": "Turn off if it errors!"` |
| `"title": "采样区"` | `"title": "Sampling"` |
| `"title": "sage加速（报错关闭）"` | `"title": "Sage acceleration (turn off if it errors)"` |
| `"matchTitle": "加速"` | `"matchTitle": "Accelerate"` |
| long Chinese example prompt | full English paragraph-by-paragraph translation |

## Deliberately NOT changed (the "frozen" fields)

- node `type` values (e.g. `MiniMaxH3Unified`, `VAE`, `GUIDER`)
- widget/input/output `name` values
- node `id`s and link indices
- the `media_state` widget value (contains real file paths)
- numeric version fields (`minimax_h3_unified_version`, `version`)

## Verification

- `json.loads()` parses the file successfully.
- `MiniMaxH3Unified` node type, `Node name for S&R` and the `media_state` payload
  verified unchanged.
- Node count 25, group count 7 (unchanged).
- Residual CJK: **1 line** — the `Bilibili / YouTube: 在下鱼酥` channel handle.

## Status: PASSED
