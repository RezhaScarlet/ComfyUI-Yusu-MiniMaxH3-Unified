# Phase 2 — Frontend UI strings

**Branch:** `i18n/english` · **Scope:** `web/minimax_h3_unified.js`

## Goal

Translate every user-visible frontend string (labels, buttons, tooltips, alerts,
validation and status messages) while keeping the mention feature backward
compatible.

## What changed

| Diff lines | Coverage |
|---|---|
| 186 | ~60 UI strings: drop zones, trim controls, waveform messages, panel headings, help text, audio-reference status, clipboard alerts, upload errors |

Representative translations:

| Before | After |
|---|---|
| `"视频轨道 · 24 FPS"` | `"Video track · 24 FPS"` |
| `"拖拽可替换"` | `"Drag to replace"` |
| `"点击加载波形（避免自动解码占用内存）"` | `"Click to load the waveform (avoids automatic decoding memory use)"` |
| `"节点内槽位"` | `"In-node slots"` |
| `LABELS = { first_frame: "首帧", last_frame: "尾帧" }` | `{ first_frame: "First frame", last_frame: "Last frame" }` |

## Mention aliases (English canonical, Chinese still parsed)

A new alias table keeps the request "add English, keep Chinese":

```js
const MENTION_ALIASES = {
    image: ["@image", "@图片"],
    video: ["@video", "@视频"],
    audio: ["@audio", "@音频"],
};
```

- `promptMentionLabel()` now emits `@image1` / `@video1` / `@audio1`.
- New `promptMentionAliases()` builds all aliases per item.
- `renderPromptEditor()` maps **both** aliases, so legacy `@图片1` text still
  renders as a chip instead of plain text.
- `normalizePromptMentions()` accepts both and is case-insensitive.
- The split regex covers `image|video|audio|图片|视频|音频`.

## Verification

- `node --check web/minimax_h3_unified.js` → **OK**
- Residual CJK: **only the 7 alias lines** (3 table rows + 3 `normalize` rows +
  1 split regex), intentionally preserved.
- CRLF line endings re-normalized after the multi-line inserts (0 bare LF).

## Status: PASSED
