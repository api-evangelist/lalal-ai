# LALAL.AI (lalal-ai)

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
