---
name: Generate an image through the Mindlogic Gateway
description: Submit an image-generation request and, for async providers, poll the operation to completion.
api: openapi/mindlogic-api-gateway-openapi-original.json
operations: [generate_image_images_generate__post, poll_image_status_images_generate__operation_id___get]
generated: '2026-07-20'
method: generated
---

# Generate an image through the Mindlogic Gateway

Base URL `https://factchat-cloud.mindlogic.ai/v1/gateway`. Authenticate with an
API key (`Authorization: Bearer <key>` or `x-api-key: <key>`).

## Steps
1. **Submit** — `POST /images/generate/`
   (`generate_image_images_generate__post`) with body:
   `{ "model": "<image model>", "prompt": "<text>", "number_of_images": 1,
   "aspect_ratio": "1:1", "image_size": "1024x1024", "quality": "standard",
   "input_images": ["<url>"] }` (`input_images` only for edit mode).
2. **Sync vs async** — Google/OpenAI providers return images immediately.
   Fal/Replicate return an `operation_id` with status processing.
3. **Poll** async operations — `GET /images/generate/{operation_id}/`
   (`poll_image_status_images_generate__operation_id___get`), passing the same
   `model` as a query param, until the image is ready.

## Rules
- On `402` check `GET /credits/` and recharge.
- On `429` back off exponentially.
- `400`/`404` usually mean the model name is wrong or not activated — confirm
  against `GET /models/`.
