# Phase 3 — Test updates

**Branch:** `i18n/english` · **Scope:** `tests/test_plugin.py`, `tests/test_frontend.mjs`

## Goal

Update the assertions that matched Chinese error messages byte-for-byte, and keep
asserting that the Chinese mention aliases still work.

## What changed

| File | Diff lines |
|---|---|
| `tests/test_plugin.py` | 44 |
| `tests/test_frontend.mjs` | 7 |

Assertions retargeted (message text only — behavior unchanged):

| Test line | Before regex | After regex |
|---|---|---|
| 249/251 | `有限数字` | `finite number` |
| 258 | `静音` | `silent` |
| 297 | `不是有效 JSON` | `not valid JSON` |
| 299 | `必须是 JSON 对象` | `must be a JSON object` |
| 301 | `非法媒体路径` | `Invalid media path` |
| 320 | `32 的倍数` | `multiples of 32` |
| 336 | `不少于 2 秒` | `at least 2 seconds` |
| 338 | `2–15` | `between 2 and 15` |
| 340 | `唯一参考` | `only reference` |
| 342/728 | `同编号视频` | `same-index video` |
| 362 | `图片文件已损坏` | `may be corrupt` |
| 755 | `不能小于 0` | `not be less than 0` |
| `test_frontend.mjs:213/218` | `/服务端响应异常/` | `/invalid/` |

The mention test is now **bilingual**: English is asserted as the primary form and
the legacy Chinese alias is asserted separately, guaranteeing the backward
compatibility promised in Phase 1/2.

## Verification

```
python -m unittest discover -s tests   →  Ran 28 tests ... OK
node tests/test_frontend.mjs           →  frontend tests passed
```

Residual CJK: **only the two alias fixtures** (`@图片1 …`), intentionally kept.

## Status: PASSED
