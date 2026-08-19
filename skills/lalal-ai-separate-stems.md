---
name: Separate a track into stems with LALAL.AI
description: >-
  Upload one audio or video file, start a stem-separation task, poll it to completion, and download the
  isolated stem and its back track. The baseline LALAL.AI flow every other flow is built on.
api: openapi/lalal-ai-stem-separation-api-openapi.yml
operations:
  - upload_upload
  - split_split_enhanced
  - check_check
  - delete_delete
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/ operationIds verified against https://www.lalal.ai/api/v1/openapi.json, plus
  conventions/lalal-ai-conventions.yml and errors/lalal-ai-problem-types.yml.
---

# Separate a track into stems

Base URL `https://www.lalal.ai`. Every operation is a **POST**. Authenticate every request with
`X-License-Key: <license key>`. API access requires a Pro plan or a minute top-up pack.

## Before you start

Call **`limits_get_minutes_left`** (`POST /api/v1/limits/minutes_left/`) and read `minutes_left`.
Minutes are consumed as **file duration × number of stems selected**, so a 5-minute track billed for one
stem costs 5 minutes. If the balance will not cover the job, stop here — starting it anyway returns the
terminal error code `exceeded_duration`.

## 1. Upload the source

**`upload_upload`** — `POST /api/v1/upload/`

- Body is the **raw file bytes**, not multipart and not JSON.
- Header `Content-Disposition: attachment; filename=<filename>` is **required** (RFC 6266).
- Returns `UploadResponse`: `{id, name, size, duration, expires}`.

Keep `id`. Every downstream call names it **`source_id`** — same value, different field name. `expires`
is a Unix epoch UTC timestamp; the file is deleted 24 hours after upload regardless of what you do.

## 2. Start the split

**`split_split_enhanced`** — `POST /api/v1/split/stem_separator/`

```json
{
  "source_id": "<id from step 1>",
  "presets": {
    "stem": "vocals",
    "splitter": "auto",
    "extraction_level": "deep_extraction",
    "dereverb_enabled": false,
    "encoder_format": null
  },
  "idempotency_key": "<uuid4 you generate>"
}
```

- `presets.stem` is the only required preset. Enum: `vocals`, `drum`, `piano`, `bass`, `guitar`,
  `electric_guitar`, `acoustic_guitar`, `synthesizer`, `strings`, `wind`.
- Model constraints from the spec: `synthesizer`, `strings` and `wind` require the `phoenix` splitter;
  `guitar` requires `andromeda`. Leave `splitter: "auto"` and the newest model for that stem is chosen.
- `extraction_level`: `deep_extraction` (default, more detail, more cross-bleed) or `clear_cut` (cleaner
  separation, may suppress detail). The `andromeda` splitter ignores this parameter.
- `dereverb_enabled` applies only to the `vocals` and `voice` stems.
- `encoder_format`: `mp3` | `wav` | `flac` | `aac` | `ogg`, or `null` to match the source format.
- `multivocal: "lead_back"` splits lead from backing vocals, returning them as `vocals@0` and `vocals@1`.

**Always send `idempotency_key`.** It is a uuid4 you generate. It prevents a retried submission from
starting a second job and billing minutes twice. It can be reused until the task starts; after that,
reusing it returns code `idempotency_key_used` — see the retry rule below.

Returns `Task`: `{task_id}`.

## 3. Poll for completion

**`check_check`** — `POST /api/v1/check/`

```json
{ "task_ids": ["<task_id>"] }
```

Up to **200** task ids per call — batch your polling rather than looping per task. Poll on a widening
interval; `429 Too Many Requests` is declared on this operation and **no `Retry-After` or `RateLimit-*`
header is returned**, so back off exponentially with no server hint.

The result carries a state: `progress` (with `progress` 0–100), `success`, `error`, or `cancelled`.

> **The single most important rule in this API: a FAILED job returns HTTP 200.** Read the task state in
> the response body. Status-code-only handling treats every failed separation as a success.

## 4. Download the output

On `success` the result carries `tracks[]`, each a `SplitTrack` with `type` (`stem` or `back`), `label`,
a signed `url`, and optional `name`, `size`, `waveform`, `playlist_file`. Fetch each `url` directly —
there is no download operation. Do it promptly; these are signed links against a source file that is
deleted 24 hours after upload.

## 5. Clean up (optional)

**`delete_delete`** — `POST /api/v1/delete/` with `{"source_id": "<id>"}` removes the source and all of
its resulting tracks immediately. Download first — this is destructive and there is no undo.

To abandon work in flight, use **`cancel_cancel`** (`POST /api/v1/cancel/` with `task_ids[]`). Do **not**
reach for `cancel_cancel_all` (`POST /api/v1/cancel/all/`) — it cancels every running task on the license
key, including work started by other callers sharing that key.

## Error handling

Errors are JSON `{detail, code}` — **match on `code`, never on `detail`**.

| code | retry? | what to do |
|---|---|---|
| `internal_error` | yes | retry with the **same** `idempotency_key` |
| `idempotency_key_used` | no | the task already started — poll `check_check` for it; do **not** resubmit with a fresh key |
| `exceeded_duration` | no | out of minutes; top up |
| `exceeded_size` | no | file too large for this license |
| `max_duration` | no | file too long for this license |
| `batch_not_avaliable` | no | batch not on this plan (the typo is the provider's — match it verbatim) |

A missing `X-License-Key` returns **403**, not 401, with `{"detail": "Provide license key in
'X-License-Key' header"}` and no `WWW-Authenticate` challenge. A `422` carries a validation array in
`detail` as `{loc, msg, type}` items — fix the field at `loc` and never retry unchanged.

Log the **`x-request-id`** response header on every call. It is present on success and failure alike and
is the only correlation handle support@lalal.ai can act on.
