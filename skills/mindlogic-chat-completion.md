---
name: Run a chat completion through the Mindlogic Gateway
description: Authenticate, pick a model, and call the OpenAI-compatible chat completions endpoint on the Mindlogic FactChat API Gateway.
api: openapi/mindlogic-api-gateway-openapi-original.json
operations: [list_models_models__get, chat_completions_chat_completions__post, get_credit_balance_credits__get]
generated: '2026-07-20'
method: generated
---

# Run a chat completion through the Mindlogic Gateway

The FactChat API Gateway is a unified LLM proxy at
`https://factchat-cloud.mindlogic.ai/v1/gateway`. It is 100% OpenAI-SDK
compatible — point an existing OpenAI client at the base URL.

## Auth
Send your Gateway API key on every request as either:
- `Authorization: Bearer <API_KEY>` (default for OpenAI SDK clients), or
- `x-api-key: <API_KEY>` (default for Anthropic SDK clients).

Generate the key in FactChat > API Gateway. Usage is metered in credits.

## Steps
1. **Discover models** — `GET /models/` (`list_models_models__get`) returns the
   accessible models in OpenAI format. Use `GET /models/{model}/` for one model.
2. **Send the completion** — `POST /chat/completions/`
   (`chat_completions_chat_completions__post`) with an OpenAI-style body:
   `{ "model": "<id>", "messages": [{"role":"user","content":"..."}] }`.
   Set `stream: true` for SSE streaming; add `stream_options` to include usage
   in the final chunk.
3. **Check credits** if you get `402 Credit balance exhausted` —
   `GET /credits/` (`get_credit_balance_credits__get`) returns the remaining
   balance; recharge in developer settings.

## Rules
- On `401` verify the header format and that the key is valid.
- On `429 Rate limit exceeded` retry with exponential backoff.
- Reasoning models: some need `max_tokens: 16000+` and `temperature: 1` to
  return non-empty content.
- Errors come back as `{"detail": {"code": <int>, "message": "<type> - ..."}}`.
