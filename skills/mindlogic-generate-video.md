---
name: Generate a video through the Mindlogic Gateway
description: Start an async video-generation job, poll it to completion, and download the result.
api: openapi/mindlogic-api-gateway-openapi-original.json
operations: [create_video_video_generation__post, get_video_generation_status_video_generation__operation_id___get, download_video_file_video_generation__operation_id__download__get]
generated: '2026-07-20'
method: generated
---

# Generate a video through the Mindlogic Gateway

Base URL `https://factchat-cloud.mindlogic.ai/v1/gateway`. Authenticate with an
API key (`Authorization: Bearer <key>` or `x-api-key: <key>`). Video generation
is always asynchronous.

## Steps
1. **Start** — `POST /video/generation/`
   (`create_video_video_generation__post`) with body:
   `{ "model": "<video model>", "prompt": "<text>", "parameters": {...},
   "input_urls": ["<url>"] }`. Returns
   `{ operation_id, status: "processing", video_model_id }`.
2. **Poll** — `GET /video/generation/{operation_id}/`
   (`get_video_generation_status_video_generation__operation_id___get`) until
   `status` is complete; the response then carries `video_uri` and
   `duration_seconds`.
3. **Download** — `GET /video/generation/{operation_id}/download/`
   (`download_video_file_video_generation__operation_id__download__get`) streams
   the raw `video/mp4` bytes.

## Rules
- On `402` check `GET /credits/` and recharge.
- On `429` back off exponentially.
- Keep the `operation_id` from step 1 — it is required for both polling and
  download.
