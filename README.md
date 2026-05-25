# LALAL.AI (lalal-ai)

LALAL.AI is an AI-powered audio separation and voice technology platform operated by OmniSale GmbH. It uses proprietary neural networks (Andromeda, Perseus, Orion, Phoenix, Lyra, Lynx) to isolate vocals, instruments, drums, bass, guitars, piano, synth, strings, and wind from mixed audio, clean voice recordings, and clone or convert voices with consent. The company ships a web app, native desktop, iOS and Android apps, a VST plug-in for DAWs, and a production REST API (v1) for embedding stem separation and voice cloning into third-party SaaS, media, and post-production pipelines.

**URL:** [Visit APIs.json](https://raw.githubusercontent.com/api-evangelist/lalal-ai/refs/heads/main/apis.yml)

**Run:** [Capabilities Using Naftiko](https://github.com/naftiko/fleet?utm_source=api-evangelist&utm_medium=readme&utm_campaign=company-api-evangelist&utm_content=repo)

## Tags

- AI, Artificial Intelligence, Audio, Audio Processing, Stem Separation, Vocal Removal, Voice Cleaning, Voice Cloning, Voice Changer, Music, Machine Learning, DSP

## Timestamps

- **Created:** 2026-05-25
- **Modified:** 2026-05-25

## Neural Splitters

| Splitter | Notes |
|---|---|
| `andromeda` | Sixth-generation engine (2025). Default for vocal/instrumental and voice/noise. |
| `perseus` | Transformer model with Clear Cut and Deep Extraction modes; covers vocals, drum, bass, piano, electric guitar, acoustic guitar. |
| `orion` | Mid-generation network for vocal/instrumental. |
| `phoenix` | Only splitter supporting `synthesizer`, `strings`, and `wind` stems. |
| `lyra` | Proprietary dereverb and Demuser-stem network. |
| `lynx` | Additional splitter exposed in API v1. |
| `auto` | Server selects the latest splitter compatible with the requested stems. |

## Supported Stems

`vocals`, `voice`, `drum`, `bass`, `piano`, `electric_guitar`, `acoustic_guitar`, `synthesizer`, `strings`, `wind`, `music`

## Output Formats

`mp3`, `wav`, `flac`, `aac`, `ogg` (defaults to source format when omitted)

## APIs

### LALAL.AI API
Production v1 REST API for audio upload, single and batch stem separation, multistem extraction, demuser, voice cleaning, voice change against legal artist or custom Voice Packs, voice pack listing, task polling, cancellation, deletion, and minutes-left introspection. Auth is `X-License-Key` on every request. Async job model — POST returns a `task_id`, then poll `POST /api/v1/check/` for state (`success` / `progress` / `error` / `cancelled`) and signed `stem_track` / `back_track` download URLs.

**Human URL:** [https://www.lalal.ai/api/](https://www.lalal.ai/api/)

- [Documentation — API portal](https://www.lalal.ai/api/)
- [Documentation — API v1 Swagger](https://www.lalal.ai/api/v1/docs/)
- [OpenAPI Source — official JSON](https://www.lalal.ai/api/v1/openapi.json)
- [Blog — Meet LALAL.AI API v1](https://www.lalal.ai/blog/meet-lalal-ai-api-v1/)
- [GitHub — Official Python examples](https://github.com/OmniSaleGmbH/lalalai)
- [OpenAPI](openapi/lalal-ai-api-openapi.yml)
- [JSON Schema — Split Task](json-schema/lalal-ai-split-task-schema.json)
- [JSON Schema — Upload](json-schema/lalal-ai-upload-schema.json)
- [JSON Schema — Voice Pack](json-schema/lalal-ai-voice-pack-schema.json)
- [JSON-LD](json-ld/lalal-ai-context.jsonld)
- [Example — Upload](examples/lalal-ai-upload-example.json)
- [Example — Stem Separator Split](examples/lalal-ai-split-stem-separator-example.json)
- [Example — Multistem](examples/lalal-ai-multistem-example.json)
- [Example — Check Task](examples/lalal-ai-check-example.json)
- [Example — Change Voice](examples/lalal-ai-change-voice-example.json)
- [Naftiko Capability — Stem Separation](capabilities/stem-separation.yaml)
- [Naftiko Capability — Multi-Stem & Batch](capabilities/multi-stem-batch.yaml)
- [Naftiko Capability — Voice Cleaning](capabilities/voice-cleaning.yaml)
- [Naftiko Capability — Voice Cloning & Change](capabilities/voice-cloning.yaml)
- [Spectral Rules](rules/lalal-ai-rules.yml)
- [Vocabulary](vocabulary/lalal-ai-vocabulary.yml)

## API v1 Endpoints

| Method | Path | Purpose |
|---|---|---|
| POST | `/api/v1/upload/` | Upload a source file, receive a `source_id`. |
| POST | `/api/v1/split/stem_separator/` | Single-stem extraction task. |
| POST | `/api/v1/split/demuser/` | Isolate the `music` stem from a mix. |
| POST | `/api/v1/split/voice_clean/` | Remove background music / noise from voice. |
| POST | `/api/v1/split/multistem/` | Extract up to six stems in one task. |
| POST | `/api/v1/split/batch/stem_separator/` | Stem separation across multiple sources. |
| POST | `/api/v1/split/batch/demuser/` | Batch demuser. |
| POST | `/api/v1/split/batch/voice_clean/` | Batch voice cleaning. |
| POST | `/api/v1/change_voice/` | Apply a legal or custom Voice Pack to a source. |
| POST | `/api/v1/voice_packs/list/` | List Voice Packs available to the license. |
| POST | `/api/v1/check/` | Poll a task for progress and download URLs. |
| POST | `/api/v1/cancel/` | Cancel a specific running task. |
| POST | `/api/v1/cancel/all/` | Cancel all running tasks. |
| POST | `/api/v1/delete/` | Delete an uploaded source file. |
| POST | `/api/v1/limits/minutes_left/` | Inspect remaining processing minutes. |

## Pricing Snapshot

| Plan | Price | Fast Queue Minutes | API Access |
|---|---|---|---|
| Starter | Free | — (10 lifetime minutes, Relaxed only) | No |
| Lite | $7.50/mo or $90/yr | 90 / month | No |
| Pro | $15/mo or $180/yr | 250 / month | **Yes** |
| Top-Up Master | $50 one-time | +750 | — |
| Top-Up Premium | $190 one-time | +3,000 | — |
| Top-Up Enterprise | $300 one-time | +5,000 | — |
| Custom Enterprise | Contact `support@lalal.ai` | Custom | Custom |

Minutes consumed = `file_length_seconds * number_of_stems / 60`. See [plans/lalal-ai-plans-pricing.yml](plans/lalal-ai-plans-pricing.yml), [rate-limits/lalal-ai-rate-limits.yml](rate-limits/lalal-ai-rate-limits.yml), and [finops/lalal-ai-finops.yml](finops/lalal-ai-finops.yml) for the full machine-readable records.

## Common Resources

- [Portal](https://www.lalal.ai)
- [Documentation](https://www.lalal.ai/api/)
- [API v1 Docs](https://www.lalal.ai/api/v1/docs/)
- [Getting Started](https://www.lalal.ai/api/help/)
- [Pricing](https://www.lalal.ai/pricing/)
- [Blog](https://www.lalal.ai/blog/)
- [Changelog](https://www.lalal.ai/changelog/)
- [Support](https://www.lalal.ai/support/)
- [Terms of Service](https://www.lalal.ai/terms-of-service/)
- [Privacy Policy](https://www.lalal.ai/privacy-policy/)
- [GitHub Organization (OmniSaleGmbH)](https://github.com/OmniSaleGmbH)
- [GitHub Repository — Official Examples](https://github.com/OmniSaleGmbH/lalalai)
- Email: `support@lalal.ai`
- [Plans](plans/lalal-ai-plans-pricing.yml)
- [Rate Limits](rate-limits/lalal-ai-rate-limits.yml)
- [FinOps](finops/lalal-ai-finops.yml)

## Maintainers

| Name | Email | Website |
|---|---|---|
| Kin Lane | info@apievangelist.com | https://apievangelist.com |
