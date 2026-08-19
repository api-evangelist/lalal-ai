---
name: Apply a voice pack with LALAL.AI Voice Changer
description: >-
  List the voice packs a license can use, then convert a recorded voice to one of them with control over
  accent intensity and tonality. The one LALAL.AI flow with a consent obligation attached.
api: openapi/lalal-ai-voice-change-api-openapi.yml
operations:
  - voice_packs_list_packs
  - upload_upload
  - change_voice_change_voice
  - check_check
  - delete_delete
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/ operationIds verified against https://www.lalal.ai/api/v1/openapi.json, plus
  conventions/lalal-ai-conventions.yml, errors/lalal-ai-problem-types.yml and
  https://www.lalal.ai/terms-of-service/.
---

# Apply a voice pack

Base URL `https://www.lalal.ai`. All POST. `X-License-Key: <license key>` on every request.

## Consent comes first

This is voice conversion. Before running it, confirm you have the right to both the **source voice** and
the **target voice pack**:

- Provider-licensed packs — `ALEX_KAYE`, `STASIA_FAYE`, `NICOLAAS_HAAS`, `NIK_ZEL`, `OLIA_CHEBO`,
  `YVAR_DE_GROOT`, `VETRANA` — are described in the contract as "legal packs, provided by us" and are
  intended for commercial use. Free "inspired" voices are intended for non-commercial use.
  https://www.lalal.ai/terms-of-service/ governs which is which; check it, do not assume.
- Custom packs come from the Voice Cloner, trained on recordings the account owner supplied. **The API
  cannot train one** — there is no training operation in v1. A pack exists or it does not.

If you are driving this operation from an agent, gate it behind an explicit human authorisation step. It
is the only capability in this API that produces a synthetic likeness of a person.

## 1. List available packs

**`voice_packs_list_packs`** — `POST /api/v1/voice_packs/list/`

Returns `VoicePackListResponse`: `packs[]` of `VoicePackInfo` `{name, pack_id, created}`. `created` is a
Unix epoch integer.

This returns the packs available to **this license**. The provider-licensed packs use symbolic ids
(`ALEX_KAYE` and the rest); user-trained packs use a uuid4 `pack_id`. Custom packs occupy subscription
Voice Pack Slots (1 on Lite, 3 on Pro), so this list is short and its contents change when a slot is
reused.

## 2. Upload the source recording

**`upload_upload`** — `POST /api/v1/upload/`. Raw bytes in the body, header
`Content-Disposition: attachment; filename=<filename>` (RFC 6266). Returns `{id, name, size, duration,
expires}`; `id` becomes `source_id`. The file is deleted after 24 hours.

Check **`limits_get_minutes_left`** first — conversion consumes processing minutes like any other job.

## 3. Convert

**`change_voice_change_voice`** — `POST /api/v1/change_voice/`

```json
{
  "source_id": "<source_id>",
  "presets": {
    "voice_pack_id": "ALEX_KAYE",
    "accent": 0.33,
    "tonality_reference": "source_file",
    "dereverb_enabled": false,
    "encoder_format": null
  },
  "idempotency_key": "<uuid4>"
}
```

- `voice_pack_id` is the only required preset — a symbolic legal-pack id or a uuid4 from step 1.
- `accent` is a float `0.0`–`1.0`, **default `1.0`**. `0.0` preserves the source speaker's accent; `1.0`
  applies the voice pack's accent fully. The default is the maximum, so if you want the source accent
  preserved you must lower it explicitly.
- `tonality_reference`: `source_file` (default) or `voice_pack` — which recording sets the pitch/tonality.
- `dereverb_enabled` (default `false`) removes room echo before conversion; it makes the result cleaner and
  slightly less natural.
- `encoder_format`: `mp3` | `wav` | `flac` | `aac` | `ogg`, or `null` to match the source.
- `idempotency_key` — always send a uuid4. Conversion is one of the more expensive jobs per minute; a
  retried submission without it starts a second conversion and bills twice.

Returns `Task`: `{task_id}`.

## 4. Poll

**`check_check`** — `POST /api/v1/check/` with `{"task_ids": ["<task_id>"]}` (max 200). States:
`progress` (0–100), `success`, `error`, `cancelled`.

The check result for this task discriminates on `task_type: "voice_convert"` and echoes back
`VoiceChangePresetsV1`, so you can confirm which pack and accent actually ran — useful when reconciling
output you did not submit yourself.

**A failed conversion returns HTTP 200 with state `error`.** Read the body state, not the status code.
`429` on this operation carries no `Retry-After` header; back off exponentially.

## 5. Collect and clean up

On `success`, fetch the signed `tracks[].url` links. Then **`delete_delete`**
(`POST /api/v1/delete/` with `{"source_id"}`) — for voice material, delete rather than waiting out the
24-hour window. `cancel_cancel` (`POST /api/v1/cancel/`, `task_ids[]`) aborts a conversion in flight.

## Error handling

Match on `code`. `internal_error` → retry with the **same** `idempotency_key`. `idempotency_key_used` →
the conversion already started; poll for it instead of resubmitting. `exceeded_duration`, `exceeded_size`,
`max_duration` → terminal license limits. A `422` names the offending field in `detail[].loc` — an invalid
`voice_pack_id` or an `accent` outside `0.0`–`1.0` lands here.

Missing credential → **403** with `{"detail": "Provide license key in 'X-License-Key' header"}`. Log the
`x-request-id` response header.
