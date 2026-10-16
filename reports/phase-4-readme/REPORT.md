# Phase 4 — README

**Branch:** `i18n/english` · **Scope:** `README.md`

## Goal

Replace the Chinese README with a full English README (single file, per the
chosen option).

## What changed

| Diff lines | Result |
|---|---|
| 646 | Full English `README.md` covering every original section |

Sections translated 1:1:

- header links (online workflows, tutorial channel) and intro
- update logs (2026-10-09 / 09-08 / 08-10 / 08-09)
- features, requirements, installation (both options)
- "Important: how to connect the model" + ASCII connection diagram
- node interface tables (always-shown inputs / mode inputs / outputs)
- the three modes, in-node↔external socket mapping, audio priority
- prompt tags, duration calculation, reference limits
- audio processing, video processing, upload location, performance design
- mode-switching notes, FAQ, code structure, dev & test, limitations, privacy

Additions required by the i18n change:

- The widget row is now named **“Auto length from audio”.**
- The prompt-tag section documents the `@` mention syntax
  (`@image1`/`@video1`/`@audio1`) and states the legacy `@图片1`/`@视频1`/`@音频1`
  aliases are still accepted.
- Diagram labels (e.g. `模型加载器 ─→ …`, `节点内参考图 1 ↔ ref_image_0`) were
  translated.

## Verification

- UTF-8, CRLF normalized (594 CRLF, 0 bare LF).
- Residual CJK: **3 lines only** — the tutorial channel handle `在下鱼酥` and the
  two lines documenting the `@图片1`/`@视频1`/`@音频1` legacy aliases.

## Status: PASSED
