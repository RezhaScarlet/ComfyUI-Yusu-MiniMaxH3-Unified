# Phase 1 — Backend strings & comments

**Branch:** `i18n/english` · **Scope:** `media.py`, `nodes.py`, `__init__.py`

## Goal

Translate every user-facing backend string (error messages, tooltips, display
names, labels) and the remaining Chinese code comments. Keep all identifiers,
socket names and behavior identical.

## What changed

| File | Diff lines | Coverage |
|---|---|---|
| `media.py` | 120 | ~30 `ValueError` messages, default labels (`"参考媒体"`→`"reference media"`, `"参考音频"`→`"reference audio"`), UI-facing format strings |
| `nodes.py` | 82 | node `description`, all `tooltip`s, `display_name`/`label_on`/`label_off`, error messages, 2 comments, mention regex |
| `__init__.py` | 10 | 4 route error messages + `LOGGER.exception` |

Representative translations:

| Before | After |
|---|---|
| `"生成宽高必须为正整数"` | `"Generation width and height must be positive integers"` |
| `"裁剪后的参考音频是静音…"` | `"The trimmed reference audio is silent; the model cannot extract a usable reference"` |
| `display_name="有音频时自动长度"` | `display_name="Auto length from audio"` |
| `label_on="启用"` / `label_off="关闭"` | `"On"` / `"Off"` |
| `"配对音频缺少同编号视频："` | `"Paired audio is missing its same-index video: "` |

## Prompt-mention contract (kept bilingual)

`nodes.normalize_prompt_mentions` now accepts **both** English and Chinese
aliases and is case-insensitive, so old saved prompts still resolve:

```python
for label, tag in (
    ("图片", "Picture"), ("image", "Picture"),
    ("视频", "Video"),  ("video", "Video"),
    ("音频", "Audio"),  ("audio", "Audio"),
):
    value = re.sub(rf"@{label}\s*(\d+)", rf"<{tag} \1>", value, flags=re.IGNORECASE)
```

## Verification

- `python -m py_compile __init__.py media.py nodes.py` → **OK**
- Residual CJK: **only the 3 alias rows above** (intentionally preserved).

## Status: PASSED
