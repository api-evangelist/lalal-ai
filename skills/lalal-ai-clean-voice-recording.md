---
name: Clean a voice recording with LALAL.AI
description: >-
  Strip background music, noise and reverb from spoken-word audio — podcasts, interviews, dialogue tracks —
  using the Voice Clean and Demuser operations, and choose correctly between the two.
api: openapi/lalal-ai-stem-separation-api-openapi.yml
operations:
  - upload_upload
  - split_split_voice_clean
  - split_split_demuser
  - check_check
  - delete_delete
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/ operationIds verified against https://www.lalal.ai/api/v1/openapi.json, plus
  conventions/lalal-ai-conventions.yml and errors/lalal-ai-problem-types.yml.
---

# Clean a voice recording

Base URL `https://www.lalal.ai`. All POST. `X-License-Key: <license key>` on every request.

## Choose the right operation first

Two operations look interchangeable and are not:

| operationId | path | extracts | use when |
|---|---|---|---|
| `split_split_voice_clean` | `/api/v1/split/voice_clean/` | the `voice` stem | the goal is a clean human voice — remove music, noise, hum, plosives |
| `split_split_demuser` | `/api/v1/split/demuser/` | the `music` stem | the goal is to divide music from everything non-music (dialogue, ambience, effects) |

Voice Clean keeps the **voice**. Demuser keeps the **music**. Picking the wrong one produces a technically
successful job with exactly the wrong output — and it still bills the minutes.

## 1. Upload

**`upload_upload`** — `POST /api/v1/upload/`. Raw file bytes in the body, header
`Content-Disposition: attachment; filename=<filename>` (RFC 6266). Returns `{id, name, size, duration,
expires}`. `id` becomes `source_id` downstream. Video files are accepted; the source is deleted after 24
hours.

Check **`limits_get_minutes_left`** first — cost is duration × stems, and a voice clean is one stem.

## 2a. Voice Clean

**`split_split_voice_clean`** — `POST /api/v1/split/voice_clean/`

```json
{
  "source_id": "<source_id>",
  "presets": {
    "stem": "voice",
    "noise_cancelling_level": 1,
    "dereverb_enabled": true,
    "splitter": "auto",
    "encoder_format": "wav"
  },
  "idempotency_key": "<uuid4>"
}
```

- `presets.stem` is required and its only legal value is the constant **`"voice"`**.
- `noise_cancelling_level` is `0` (mild), `1` (normal) or `2` (aggressive). **The spec default is `0`** —
  if you want normal or aggressive filtering you must say so; the docs describe `1` as normal but the
  contract will not apply it for you.
- `dereverb_enabled` is valid here (it applies to the `vocals` and `voice` stems) and removes room echo at
  some cost to naturalness. Leave it `false` when the recording should keep its space.
- `encoder_format`: `mp3` | `wav` | `flac` | `aac` | `ogg`, or `null` to match the source. For anything
  going on to transcription or further processing, choose `wav` or `flac` and avoid a second lossy pass.
- The `lynx` model is the current voice-oriented network (changelog 2026-05-07). `splitter: "auto"` selects
  the newest available model for the stem; only pin a specific `SplitterType` when you have a reason to.

## 2b. Demuser

**`split_split_demuser`** — `POST /api/v1/split/demuser/`

```json
{
  "source_id": "<source_id>",
  "presets": { "stem": "music", "splitter": "auto", "encoder_format": null },
  "idempotency_key": "<uuid4>"
}
```

`presets.stem` is required and its only legal value is the constant **`"music"`**. Demuser has no
`noise_cancelling_level`.

Both operations return `Task`: `{task_id}`. Always send `idempotency_key` — a uuid4 you generate — so a
retried submission cannot start a second job and bill the minutes twice.

## 3. Poll

**`check_check`** — `POST /api/v1/check/` with `{"task_ids": ["<task_id>"]}` (max 200 ids). States are
`progress` (`progress` 0–100), `success`, `error`, `cancelled`.

**A failed job returns HTTP 200 with state `error`.** Read the body state. `429` is declared on this
operation and carries no `Retry-After` or `RateLimit-*` header — back off exponentially.

## 4. Collect the output

On `success`, `tracks[]` holds `SplitTrack` entries with `type` `stem` or `back` and a signed `url`:

- After **Voice Clean**, the `stem` track is the cleaned voice and the `back` track is the removed noise
  and music.
- After **Demuser**, the `stem` track is the isolated music and the `back` track is everything non-music.

Fetch each `url` directly; there is no download operation. Then **`delete_delete`**
(`POST /api/v1/delete/`, `{"source_id"}`) if the material should not sit on LALAL.AI servers for the full
24 hours — relevant for interview and client audio.

## Error handling

Match on `code`, never on `detail`. `internal_error` is the only retryable code, and retry it with the
**same** `idempotency_key`. `idempotency_key_used` means the task already started — poll for it rather than
resubmitting. `exceeded_duration`, `exceeded_size` and `max_duration` are terminal license limits.

A missing key returns **403** (not 401) with `{"detail": "Provide license key in 'X-License-Key' header"}`.
Log the `x-request-id` response header on every call.
