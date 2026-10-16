cn online workflow: http://runninghub.cn/post/2086063010979864577/?inviteCode=rh-v1525

ai online workflow: https://www.runninghub.ai/zh-cn/post/2086007179703197697/?inviteCode=rh-v1525

Tutorials on Bilibili / YouTube: 在下鱼酥


# ComfyUI-Yusu-MiniMaxH3-Unified

A unified MiniMax H3 workflow for text-to-video, first/last-frame-to-video and
omni-modal reference video, with in-node image, video and audio upload plus
precise trimming.

> This plugin is a wrapper around ComfyUI's official `MiniMaxH3ImageToVideo` and
> `MiniMaxH3ReferenceToVideo` nodes. It only handles input organization, media
> processing and interface mapping; it does not modify the MiniMax H3 inference
> core.

## 2026-10-09 update

- Removed the 15-second cap on a single reference audio segment. Both uploaded
  audio and external `AUDIO` keep the full selection; at least 2 seconds is
  still required, and the reference video limits are unchanged.

## 2026-09-08 update

- Aligned with the new ComfyUI MiniMax H3 official node interfaces. Text-to-video,
  first/last-frame-to-video and omni-modal reference now all call the official
  nodes with keyword arguments, avoiding input misalignment when the official
  parameter order changes.
- Fixed the omni-reference mode bug where a string was passed as width/height,
  triggering `unsupported operand type(s) for //: 'str' and 'int'`.
- Normalized width, height and the dynamic-mode duration input to numbers, with
  clear error messages for invalid width/height.
- Added regression tests for the three modes' call signatures, width/height
  types, frame counts and first/last-frame forwarding.

## 2026-08-10 update

- Uploads use the local ComfyUI standard `/upload/image` asset endpoint.
- The waveform canvas no longer forwards click events to the trim timeline,
  fixing the bug where clicking "load waveform" moved the trim handles.
- When the host has not expanded the official Autogrow media group, only the
  first sub-interface is restored to a connectable `IMAGE` / `AUDIO` socket;
  already-expanded local sockets are left unchanged.
- The media panel width follows the node width, so the host no longer mounts it
  at an old width with the content squeezed to the left.
- Each reference audio segment is independently limited to 2–15 seconds; the
  total duration of multiple segments is no longer merged into a 15-second cap.
- After updating the plugin you must fully restart ComfyUI; if the frontend and
  backend versions are out of sync, uploads show an explicit restart prompt.

## 2026-08-09 update

- Uses the local ComfyUI official media path resolution.
- The automatic audio-duration mode keeps a valid fallback `duration`.
- The trim timeline adds canvas event isolation and pointer capture.
- Removed uncalled backend functions, single-call wrappers and old trim styles,
  keeping the existing behavior unchanged.

## Features

- One node supports three modes:
  - `text_to_video`: text to video
  - `first_last_frame`: first/last frame to video
  - `omni_reference`: image, video and audio omni-modal reference
- The `clip`, `vae` and `audio_vae` inputs are always shown.
- First/last-frame and the various reference media sockets are shown dynamically
  by mode.
- Upload, preview, replace and trim media directly inside the node; the
  omni-reference panel supports `Ctrl+V` pasting of images, video and audio and
  automatically fills the first empty slot.
- Maximum supported:
  - 9 reference images
  - 3 reference videos
  - 3 paired video audio tracks
  - 3 standalone reference audio tracks
- External ComfyUI `IMAGE` / `AUDIO` sockets can override the in-node files.
- Supports automatic duration calculation from the reference audio.
- `duration` takes seconds and is automatically converted and aligned to the H3
  time grid.
- Provides a standard `AUDIO` output for previewing or saving the reference
  audio actually used.
- Image cards use a unified title, preview and button height, so landscape and
  portrait images stay aligned.
- No telemetry, no external API, no background polling, no idle GPU work.

## Requirements

- ComfyUI installed and running normally.
- The ComfyUI version must include the official MiniMax H3 nodes:
  - `MiniMaxH3ImageToVideo`
  - `MiniMaxH3ReferenceToVideo`
- The matching MiniMax H3 model, CLIP and VAE are prepared; an Audio VAE is also
  required when using reference audio.
- The plugin has no separate `requirements.txt`; it uses the PyTorch, Pillow,
  PyAV, NumPy and aiohttp from the ComfyUI environment.
- When `ffprobe` is installed on the system it is preferred for media probing;
  if unavailable it falls back to PyAV.

## Installation

### Option 1: Git clone

```bash
cd ComfyUI/custom_nodes
git clone https://github.com/yusu-02/ComfyUI-Yusu-MiniMaxH3-Unified.git
```

### Option 2: Manual install

1. Download the repository ZIP.
2. After extracting, make sure the directory structure is:

```text
ComfyUI/
└── custom_nodes/
    └── ComfyUI-Yusu-MiniMaxH3-Unified/
        ├── __init__.py
        ├── nodes.py
        ├── media.py
        └── web/
```

3. Restart ComfyUI.
4. In the browser press `Ctrl+F5` to force-refresh the frontend cache.
5. Look in the node menu for:

```text
Yusu / MiniMax / H3 / Yusu MiniMax H3 Unified
```

When upgrading from an older version, fully delete the old plugin directory
first, then copy the new version, to avoid leftover old JavaScript or Python
files.

## Important: how to connect the model

This node has **no `MODEL` input or output**.

The model loader should connect directly to the downstream official sampling
chain, for example the guider, scheduler or sampler; this node only outputs
conditioning and the AV latent.

```text
Model loader ──────────────→ Guider / Scheduler / Sampler
CLIP ────────→ Unified.clip
VAE ─────────→ Unified.vae
Unified.positive ──────────→ Guider.conditioning
Unified.av_latent ─────────→ Sampler.latent_image
```

The mode must match the correct model family:

- `text_to_video`, `first_last_frame`: use the matching FL2VA model.
- `omni_reference`: use the matching Ref2VA model.

## Node interface

### Always-shown inputs

| Socket | Type | Description |
|---|---|---|
| `clip` | `CLIP` | Used for prompt encoding |
| `vae` | `VAE` | Used for image/video latent processing |
| `audio_vae` | `VAE` | Always shown; only required when reference audio is present |
| `prompt` | `STRING` | Supports external text nodes and dynamic prompts |
| `width` | `INT` | Output width, must be a multiple of 32 |
| `height` | `INT` | Output height, must be a multiple of 32 |
| `duration` | `FLOAT` | Target generation seconds, 0 allowed, no plugin upper limit |
| Auto length from audio | `BOOLEAN` | In reference mode, derive the duration from the longest valid audio |

### Mode-dependent inputs

#### `text_to_video`

Adds no media sockets; only uses the prompt, width, height and `duration`.

#### `first_last_frame`

| Socket | Type | Description |
|---|---|---|
| `first_frame` | `IMAGE` | Optional first frame; the external socket takes priority over an in-node upload |
| `last_frame` | `IMAGE` | Optional last frame; the external socket takes priority over an in-node upload |

At least one of the first or last frame is required.

#### `omni_reference`

| Socket | Type | Count | Description |
|---|---|---:|---|
| `ref_image_0...` | `IMAGE` | 9 | Reference images |
| `ref_video_0...` | `IMAGE` batch | 3 | 24 FPS reference video frame batches |
| `ref_video_audio_0...` | `AUDIO` | 3 | Audio tracks paired with the same-index reference videos |
| `ref_audio_0...` | `AUDIO` | 3 | Standalone reference audio |
| `ref_image_size` | `COMBO` | 1 | `match` or `max` |

### Outputs

| Socket | Type | Description |
|---|---|---|
| `positive` | `CONDITIONING` | Connect to the guider's conditioning input |
| `av_latent` | `LATENT` | Joint video/audio latent, connect to the sampling chain |
| `audio` | `AUDIO` | Outputs the longest trimmed reference audio actually used; empty when there is no audio |

The `audio` output is reference audio, not the final video soundtrack produced by
the model.

## The three modes

### 1. Text to video

Select:

```text
mode = text_to_video
```

Only needs:

- `clip`
- `vae`
- prompt
- width, height
- `duration`

Reference media uploaded inside the node is ignored.

### 2. First/last frame to video

Select:

```text
mode = first_last_frame
```

Provide the first frame, the last frame, or both. You can:

- connect an external `IMAGE`;
- upload an image inside the node panel.

When the same slot has both an external connection and an in-node image, the
external connection takes priority.

### 3. Omni-modal reference video

Select:

```text
mode = omni_reference
```

Can be combined:

- reference images
- reference videos
- audio embedded in the video
- audio paired with the video
- standalone reference audio

`audio_vae` must be connected whenever any reference audio is present.

## In-node media and external socket mapping

In-node slots are numbered from 1, while ComfyUI external sockets are numbered
from 0:

```text
In-node reference image 1   ↔ ref_image_0
In-node reference video 1   ↔ ref_video_0
Video 1 paired audio        ↔ ref_video_audio_0
In-node standalone audio 1  ↔ ref_audio_0
```

An external socket overrides the in-node file in the same slot and does not add
the reference twice.

Video audio priority:

```text
External ref_video_audio_N
        ↓
In-node "Video N paired audio"
        ↓
Audio embedded in the reference video
```

`ref_video_audio_N` must be used together with the same-index `ref_video_N`. An
orphan paired audio errors out before decoding, avoiding pointless CPU and
memory use.

## Prompt tags

Omni-reference mode shows the actual tag mapping in the node panel, for example:

```text
<Picture 1> ← ref_image_0
<Audio 1>   ← ref_audio_0
<Video 1>   ← ref_video_0
```

The prompt must explicitly use the tags it wants to reference, for example:

```text
Use the timbre and speaking style of <Audio 1>
to make the person in <Picture 1> speak naturally.
```

The in-node prompt editor also offers `@` mentions: typing `@` inserts
`@image1` / `@video1` / `@audio1`, which are converted to `<Picture 1>` /
`<Video 1>` / `<Audio 1>` at execution time. Legacy `@图片1` / `@视频1` /
`@音频1` aliases are still accepted so older saved workflows keep working.

Notes:

- External sockets are numbered from 0, prompt tags from 1.
- Video paired audio participates in the tag ordering.
- Do not guess `<Audio n>` from a socket suffix; trust the mapping shown in the
  node panel.
- When the prompt comes from an external text node, the plugin tries to read the
  upstream text; when it is dynamically generated and unreadable on the frontend
  it is left to the backend to evaluate against the final prompt, instead of a
  false missing-tag error.

## Duration calculation

### Manual duration

`duration` is in seconds and is shown as:

```text
FLOAT (duration)
```

Calculation flow:

```text
target seconds
→ round(seconds × 24 FPS)
→ align to the H3 17k+5 frame grid
→ create the AV latent
```

For example:

```text
5.000 seconds
→ 120 frames
→ aligned to 124 frames
→ actually about 5.167 seconds
```

Entering `0` seconds does not produce a true zero-frame latent; it gives the
smallest 5 frames the H3 time structure allows, about `0.208` seconds.

The plugin does not cap the maximum output duration. Very long generations
increase RAM, VRAM, sampling time and output file size linearly; excessively
large values may cause out-of-memory or driver crashes.

### Auto length from audio

Only effective in `omni_reference` mode.

After enabling "Auto length from audio":

- the `duration` control is greyed out and locked;
- the longest valid trimmed reference audio is used;
- it is converted at 24 FPS and aligned to the H3 frame grid automatically;
- when there is no valid reference audio it falls back to the saved manual
  seconds.

Text-to-video, first/last-frame and ordinary silent references never require an
extra audio-duration node.

## Reference media limits

The plugin validates reference media as follows:

- Each reference video: 2–15 seconds.
- Total duration of all reference videos: no more than 15 seconds.
- Each reference audio: at least 2 seconds, no upper duration limit.
- Multiple reference audio segments are validated separately and are not capped
  in total.
- The frontend trim handles allow any selection; the current single-segment
  selection is validated at execution time and errors out if out of range.
- Generation width/height must be multiples of 32.
- A single uploaded file is at most 512 MiB.
- Images are at most 100,000,000 pixels.
- Reference audio sample rate is at most 384 kHz.

These are reference media limits and are separate from the uncapped output
`duration`.

## Audio processing

- The trim range uses a half-open interval `[in, out)`.
- Mono is automatically duplicated to stereo.
- Only the first two channels are kept from multichannel audio.
- The audio waveform is clamped to `[-1, 1]`.
- Empty audio, pure silence, NaN and infinite values error out directly.
- Long in-node audio only decodes the trimmed range, without loading the whole
  file first.
- When the video and audio start times differ, the audio is corrected along the
  video timeline and padded with silence where needed.
- The frontend waveform does not auto-decode; it loads after you click the
  waveform area.

## Video processing

- Decoded video is uniformly converted to packed RGB24.
- Rotation metadata and non-square pixel aspect ratios are applied.
- Online sampling is 24 FPS.
- The decode stage adapts directly to the H3 reference canvas.
- Only the frame prefix actually needed by the conditioning is materialized,
  reducing memory pressure for high frame rates and long videos.

## Upload file location

Local ComfyUI stores in-node uploaded files by default in:

```text
ComfyUI/input/minimax_h3_unified/
```

The "Delete" button in the node only removes the current slot reference; it does
not delete the file on disk, so it cannot break other workflows that reference
the same asset. Files that are no longer used must be cleaned up manually.

The upload process checks:

- extension
- MIME type
- real image format
- video/audio streams
- file size
- path safety

When parsing fails or the content is invalid, the slot state is not written.

## Performance design

- A prompt change only refreshes the audio tag status; it does not rebuild the
  whole media panel.
- Trim slider state saving is merged into the browser animation frame.
- Images use lazy loading and asynchronous decoding.
- Video and audio previews only preload metadata.
- The waveform cache only stores downsampled peaks, not a full `AudioBuffer`.
- The waveform cache holds at most 12 items.
- Audio larger than 64 MiB or longer than 5 minutes is not fully decoded into a
  waveform in the browser.
- Media probing runs in a thread pool and does not block the ComfyUI web server.
- No background polling, no resident worker thread and no idle GPU work.

## Notes on mode switching

`clip`, `vae` and `audio_vae` are always kept.

The following sockets are created or removed dynamically by mode:

- `first_frame`
- `last_frame`
- `ref_image_*`
- `ref_video_*`
- `ref_video_audio_*`
- `ref_audio_*`

When ComfyUI removes a dynamic socket it may also disconnect that socket's
external link. So after switching modes, dynamic media sockets may need to be
reconnected; in-node media state is still saved in the workflow.

## FAQ

### I connected audio, why does it say it is not referenced?

Check the following conditions:

1. the mode is `omni_reference`;
2. `audio_vae` is connected;
3. there is at least one reference image or one reference video;
4. the prompt contains the `<Audio n>` tag shown in the panel;
5. the trimmed reference audio is not silent and its duration is within limits.

### Why do the entered seconds and the actual generated duration not match exactly?

The H3 time dimension must satisfy `17k+5`, so the node rounds the frame count up.
The panel shows the target seconds, the effective frames and the actual seconds.

### Why is there no `MODEL` socket?

The model does not need to pass through this node. Connect the model loader
directly to the Guider, Scheduler or Sampler; this node only provides `positive`
and `av_latent`.

### Why did the in-node "Delete" not delete the file on disk?

The same file may be referenced by other workflows. To avoid breaking them, the
plugin only removes the slot reference; the physical file must be cleaned up
manually.

### Why is the audio output not the final generated soundtrack?

The `audio` output returns the audio actually fed into reference encoding. The
final generated soundtrack comes from the sampled and decoded AV latent workflow.

### After replacing the plugin the UI is still the old version, what now?

1. make sure the old plugin directory is fully deleted;
2. restart the ComfyUI backend;
3. press `Ctrl+F5` in the browser;
4. if necessary, clear the browser site cache.

## Code structure

```text
ComfyUI-Yusu-MiniMaxH3-Unified/
├── __init__.py
├── media.py
├── nodes.py
├── web/
│   ├── minimax_h3_unified.js
│   └── minimax_h3_unified.css
└── tests/
    ├── test_plugin.py
    └── test_frontend.mjs
```

- `__init__.py`: plugin entry point and upload route.
- `media.py`: path safety, media probing, trimming, decoding and validation.
- `nodes.py`: node schema, mode routing, slot mapping and official node calls.
- `web/`: node panel, upload interaction, dynamic sockets and styles.
- `tests/`: backend and frontend regression tests, not run automatically by
  ComfyUI.

## Development and testing

Backend tests:

```bash
python -m unittest discover \
  -s custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/tests \
  -v
```

Frontend tests:

```bash
node custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/tests/test_frontend.mjs
```

Syntax check:

```bash
python -m py_compile \
  custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/__init__.py \
  custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/media.py \
  custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/nodes.py

node --check \
  custom_nodes/ComfyUI-Yusu-MiniMaxH3-Unified/web/minimax_h3_unified.js
```

## Known limitations

- The plugin depends on the current ComfyUI MiniMax H3 official node interfaces;
  an incompatible official change may require a synchronized update.
- Dynamic mode sockets may disconnect external links when switching modes.
- High-resolution and long reference videos still form large `IMAGE` batches,
  using a fair amount of RAM.
- There is no plugin cap on very long outputs, but that does not mean the model
  was trained or validated for every length.
- Reference audio entering the conditioning does not mean the model will
  reproduce the content word-for-word or fully clone the timbre.
- Corrupt media, missing timestamps or abnormal encodings may not be decoded
  accurately by PyAV/FFmpeg.

## Security and privacy

- No telemetry is collected.
- No media, prompt or workflow is sent to external servers.
- Frontend network requests only reach the current local ComfyUI standard upload,
  plugin media inspection and `/view` routes.
- Contains no mining, background monitoring, resident polling or automatic test
  execution.

---

Please file reproducible issues via the Issue tracker. Attach:

- ComfyUI version or commit hash
- operating system
- Python, PyTorch, PyAV versions
- the mode used
- the full error log
- a minimal reproducible workflow
