---
name: Batch-separate many tracks with LALAL.AI
description: >-
  Process a library of files through LALAL.AI in bulk — upload each source, submit up to 100 split jobs in
  one call, and reconcile a partially-successful batch response against a single polling loop.
api: openapi/lalal-ai-batch-stem-separation-api-openapi.yml
operations:
  - upload_upload
  - batch_split_split_batch_enhanced
  - batch_split_split_batch_demuser
  - batch_split_split_batch_voice_clean
  - check_check
  - limits_get_minutes_left
generated: '2026-08-13'
method: generated
source: >-
  Grounded in openapi/ operationIds verified against https://www.lalal.ai/api/v1/openapi.json, plus
  conventions/lalal-ai-conventions.yml and errors/lalal-ai-problem-types.yml.
---

# Batch-separate many tracks

Base URL `https://www.lalal.ai`. All POST. Header `X-License-Key: <license key>` on every request.

Batch processing is **plan-gated**. If it is not enabled on the license, every item comes back with code
`batch_not_avaliable` and you must fall back to the single-file `split_split_enhanced` operation.

## 1. Budget the run first

**`limits_get_minutes_left`** — `POST /api/v1/limits/minutes_left/`

Cost is **Σ (duration of each file × number of stems requested for it)**. There is no dry-run that prices
a batch without doing the work, and there is no partial refund. Compute the total from the `duration`
values the uploads return and compare it to `minutes_left` **before** submitting.

## 2. Upload every source

**`upload_upload`** — `POST /api/v1/upload/`, once per file. Raw bytes in the body,
`Content-Disposition: attachment; filename=<filename>` header required. Collect each `id` (referred to as
`source_id` everywhere downstream) and each `duration`.

Uploads are independent — parallelise them, but keep the 24-hour retention window in mind: the whole batch
must finish and be downloaded inside it.

## 3. Submit the batch

Pick the operation that matches the work:

| operationId | path | use for |
|---|---|---|
| `batch_split_split_batch_enhanced` | `/api/v1/split/batch/stem_separator/` | isolating a named stem |
| `batch_split_split_batch_demuser` | `/api/v1/split/batch/demuser/` | splitting music from non-music |
| `batch_split_split_batch_voice_clean` | `/api/v1/split/batch/voice_clean/` | cleaning spoken-word audio |

```json
{
  "items": [
    {
      "source_id": "<source_id 1>",
      "presets": { "stem": "vocals", "splitter": "auto", "extraction_level": "clear_cut" },
      "idempotency_key": "<uuid4 1>"
    },
    {
      "source_id": "<source_id 2>",
      "presets": { "stem": "drum" },
      "idempotency_key": "<uuid4 2>"
    }
  ]
}
```

- `items` holds **1 to 100** entries (`minItems: 1`, `maxItems: 100`). Chunk anything larger.
- Each item is the same shape as a single-file split, including its **own** `idempotency_key`. Give every
  item a distinct uuid4 and persist the mapping — that is what makes a retry of the whole batch safe.

## 4. Reconcile the response — it is not all-or-nothing

The response is `BatchTasksResponse`: `results[]`, **ordered to match your `items[]`**, discriminated on
`status`:

- `status: "success"` → `SuccessfulTaskStart` with `task_id` and `source_id`
- `status: "error"` → `FailedTaskStart` with `error`, `code` and `source_id`

> **HTTP 200 does not mean the batch started.** Individual items fail inside a 200 response. Walk
> `results[]` and partition it before you do anything else. Treating a 200 as "all accepted" silently
> drops files.

Retry only the failed items, and only where the code is retryable (`internal_error`). Reuse the **same**
`idempotency_key` for each retried item. Items that failed with `exceeded_duration`, `exceeded_size`,
`max_duration` or `batch_not_avaliable` are terminal — retrying wastes calls and, once minutes run out,
every subsequent item fails the same way.

## 5. Poll all tasks in one loop

**`check_check`** — `POST /api/v1/check/` with `{"task_ids": [...]}`, **up to 200 ids per call**. Poll the
whole batch in one request rather than one request per task; this is the operation that declares `429` and
it returns no `Retry-After` or `RateLimit-*` header, so a per-task loop will hit the ceiling with no
guidance on how long to wait.

Each element carries its own state. A task in state `error` arrives inside an HTTP 200 — read the body
state, not the status code. Drop each id from the poll set as it reaches `success`, `error` or
`cancelled`, and widen the interval as the set shrinks.

## 6. Download and clean up

On `success`, fetch each `tracks[].url` signed link. Then **`delete_delete`**
(`POST /api/v1/delete/` with `{"source_id"}`) per source if you want the material gone before the
automatic 24-hour deletion.

To abort a run, cancel by id with **`cancel_cancel`** (`POST /api/v1/cancel/`, `task_ids[]`). Reserve
**`cancel_cancel_all`** (`POST /api/v1/cancel/all/`) for an operator-initiated stop — it cancels every
running task on the license key, not just this batch.

Log the `x-request-id` response header per call; on a 100-item batch it is the only thing that lets
support trace which submission you mean.
