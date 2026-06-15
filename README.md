# LALAL.AI (lalal-ai)

LALAL.AI is an AI-powered audio separation and voice technology platform operated by OmniSale GmbH. It uses proprietary neural networks (Andromeda, Perseus, Orion, Phoenix, Lyra, Lynx) to isolate vocals, instruments, drums, bass, guitars, piano, synth, strings, and wind from mixed audio, clean voice recordings, and clone or convert voices with consent. The company ships a web app, native desktop, iOS and Android apps, a VST plug-in for DAWs, and a production REST API (v1) for embedding stem separation and voice cloning into third-party SaaS, media, and post-production pipelines.

**APIs.json:** [https://raw.githubusercontent.com/api-evangelist/lalal-ai/refs/heads/main/apis.yml](https://raw.githubusercontent.com/api-evangelist/lalal-ai/refs/heads/main/apis.yml)

## Scope

- **Position:** Producing

## Tags

- AI
- Artificial Intelligence
- Audio
- Audio Processing
- Stem Separation
- Vocal Removal
- Voice Cleaning
- Voice Cloning
- Voice Changer
- Music
- Machine Learning
- DSP

## Timestamps

- **Created:** 2026-05-25T00:00:00.000Z
- **Modified:** 2026-05-25

## APIs

### LALAL.AI API

LALAL.AI API v1 is a production-grade audio separation, voice cleaning, and voice cloning API powered by proprietary neural networks (Andromeda, Perseus, Orion, Phoenix, Lyra, Lynx). It exposes upload, split, multi-stem, demuser, voice-clean, change-voice, voice-pack listing, task polling, and limits endpoints with both single-task and batch variants. Authentication is via the X-License-Key header. Supported stems include vocals, drums, bass, piano, electric guitar, acoustic guitar, synthesizer, strings, and wind, with output in MP3, WAV, FLAC, AAC, or OGG.

- **Human URL:** [https://www.lalal.ai/api/](https://www.lalal.ai/api/)
- **Base URL:** `https://www.lalal.ai`

#### Tags

- AI
- Audio
- Stem Separation
- Voice Cloning
- Music
- Vocal Removal
- Machine Learning

#### Properties

- [Documentation](https://www.lalal.ai/api/)
- [Documentation](https://www.lalal.ai/api/v1/docs/)
- [Open A P I Source](https://www.lalal.ai/api/v1/openapi.json)
- [Blog](https://www.lalal.ai/blog/meet-lalal-ai-api-v1/)
- [GitHub Repository](https://github.com/OmniSaleGmbH/lalalai)
- [OpenAPI](openapi/lalal-ai-api-openapi.yml) — [OpenAPI Specification](https://spec.openapis.org/oas/latest.html)
- [Postman Collection](collections/lalal-ai-api.postman_collection.json) — [Postman Collection 2.1](https://schema.getpostman.com/json/collection/v2.1.0/collection.json)
- [Open Collection](collections/lalal-ai-api.opencollection.json) — [Open Collection 1.0](https://schema.opencollection.com/opencollection/v1.0.0.json)
- [JSON Schema](json-schema/lalal-ai-split-task-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/lalal-ai-upload-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON Schema](json-schema/lalal-ai-voice-pack-schema.json) — [JSON Schema](https://json-schema.org/specification)
- [JSON-LD](json-ld/lalal-ai-context.jsonld) — [JSON-LD](https://www.w3.org/TR/json-ld11/)
- [Example](examples/lalal-ai-upload-example.json)
- [Example](examples/lalal-ai-split-stem-separator-example.json)
- [Example](examples/lalal-ai-check-example.json)
- [Example](examples/lalal-ai-multistem-example.json)
- [Example](examples/lalal-ai-change-voice-example.json)
- [Spectral Rules](rules/lalal-ai-rules.yml) — [Spectral](https://docs.stoplight.io/docs/spectral)
- [Vocabulary](vocabulary/lalal-ai-vocabulary.yml)

## Common Properties

- [Portal](https://www.lalal.ai)
- [Documentation](https://www.lalal.ai/api/)
- [Documentation](https://www.lalal.ai/api/v1/docs/)
- [Getting Started](https://www.lalal.ai/api/help/)
- [Pricing](https://www.lalal.ai/pricing/)
- [Blog](https://www.lalal.ai/blog/)
- [Changelog](https://www.lalal.ai/changelog/)
- [Support](https://www.lalal.ai/support/)
- [Terms of Service](https://www.lalal.ai/terms-of-service/)
- [Privacy Policy](https://www.lalal.ai/privacy-policy/)
- [GitHub Organization](https://github.com/OmniSaleGmbH)
- [GitHub Repository](https://github.com/OmniSaleGmbH/lalalai)
- [Contact Us](mailto:support@lalal.ai)
- [SDK](https://github.com/OmniSaleGmbH/lalalai/tree/main/api-v1/python)
- [Plans](plans/lalal-ai-plans-pricing.yml)
- [Rate Limits](rate-limits/lalal-ai-rate-limits.yml)
- [Fin Ops](finops/lalal-ai-finops.yml)
- [Features](undefined)

## Maintainers

**FN:** Kin Lane
**Email:** info@apievangelist.com
**URL:** https://apievangelist.com
