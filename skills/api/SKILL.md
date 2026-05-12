---
name: cartesia-api
description: Integrate Cartesia speech APIs (TTS, STT, voices) in application code or coding-agent workflows. Use when the user asks about Cartesia REST/WebSocket APIs, SDKs, API keys, Sonic TTS, Ink STT, voice IDs, access tokens, or embedding voice in an app. For Cartesia Line deployed agents, CLI deploy, and telephony, use the line-voice-agent skill instead.
compatibility: Requires a Cartesia API key from https://play.cartesia.ai/keys for server-side calls. Client apps must use short-lived access tokens, not raw API keys. Optional cartesia-mcp requires Python 3.13+.
---

# Cartesia Voice & Speech APIs

Cartesia provides **text-to-speech (Sonic)**, **speech-to-text (Ink)**, **voices** (library, clone, localize), and related **HTTPS** and **WebSocket** APIs. This skill covers **application integration** and **agent-assisted coding**. For **Cartesia Line** (managed voice agents, `cartesia` CLI, telephony, Line SDK), use **[line-voice-agent](../line/SKILL.md)**.

## Core directives

- **HTTPS only:** All HTTP endpoints use `https://api.cartesia.ai`. WebSockets use `wss://`. HTTP may be unsupported; keys used over HTTP can be auto-rotated.
- **Version header (required):** Every request MUST send `Cartesia-Version` with a date (`YYYY-MM-DD`). Use the **same** date across your services so responses and errors stay predictable. Valid dates and what changed between them live in [API conventions](https://docs.cartesia.ai/use-the-api/api-conventions) and [`llms.txt`](https://docs.cartesia.ai/llms.txt) (not every page differs per version). Optional: `GET https://api.cartesia.ai/` returns JSON `{"ok":true,"version":"..."}` with the gateway’s current default—useful when wiring a new client. **Browser WebSockets** cannot set custom headers on the handshake, so use **`?cartesia_version=...`** there; when a header and query are both present on WebSockets that accept both, the **query wins**.
- **Server-side auth:** Use **`Authorization: Bearer <api_key>`** with your Cartesia API key (`sk_car_...`).
- **Client apps (browser / mobile):** **Never** embed API keys. Have your backend mint a **short-lived access token** and use `Authorization: Bearer <access_token>`. For WebSockets from browsers, pass the token as **`?access_token=<token>`** (headers are not available on WS handshake). See [Access Token API](https://docs.cartesia.ai/api-reference/auth/access-token.md).
- **Source of truth:** Prefer [docs](https://docs.cartesia.ai), OpenAPI (`latest.yml` in the docs repo), and AsyncAPI for WebSocket TTS. For machine index: [`llms.txt`](https://docs.cartesia.ai/llms.txt) and [`llms-full.txt`](https://docs.cartesia.ai/llms-full.txt).
- **Choosing a TTS model:** See [TTS models](https://docs.cartesia.ai/build-with-cartesia/tts-models/latest) for current IDs and guidance.
- **Do not invent voice IDs:** Use the [List voices API](https://docs.cartesia.ai/api-reference/voices/list.md) or the SDK; playground defaults in examples are placeholders only.
- **Streaming TTS:** WebSocket TTS has different chunking, buffering, and continuation rules than **`POST /tts/bytes`**—read the WebSocket TTS doc before assuming bytes-stream behavior.
- **Errors:** For `Cartesia-Version` **2026-03-01** and newer, errors are **structured JSON** (`error_code`, `title`, `message`, `request_id`, optional `doc_url`). Older versions may return legacy plain-text or legacy WebSocket envelopes. See [API Errors](https://docs.cartesia.ai/use-the-api/api-errors) and [API conventions](https://docs.cartesia.ai/use-the-api/api-conventions).
- **Optional MCP:** [cartesia-mcp](https://github.com/cartesia-ai/cartesia-mcp) helps in **Cursor / Claude** (files, voice tools); it does not replace REST/SDKs for production. Requires **Python 3.13+**. See [MCP docs](https://docs.cartesia.ai/tools/ai/mcp).

## When to use which path

| Goal | Path |
|------|------|
| App or backend calling REST/WebSocket | [Python SDK](https://github.com/cartesia-ai/cartesia-python), [JS/TS SDK](https://github.com/cartesia-ai/cartesia-js), or `curl` + OpenAPI |
| IDE agent with MCP | `cartesia-mcp` + docs fallback |
| Deployed voice agent, Line, telephony | **[line-voice-agent](../line/SKILL.md)** |
| Quick curl-oriented capsule | `https://cartesia.sh/openclaw.md` (verify against docs for your version) |

## Quick start (HTTP TTS, one-shot)

Shape only—confirm field names and enums against the [API reference](https://docs.cartesia.ai) for your `Cartesia-Version`:

```bash
curl -X POST "https://api.cartesia.ai/tts/bytes" \
  -H "Authorization: Bearer $CARTESIA_API_KEY" \
  -H "Cartesia-Version: 2025-04-16" \
  -H "Content-Type: application/json" \
  -d '{
    "model_id": "sonic-3",
    "transcript": "Hello from Cartesia.",
    "voice": { "id": "f786b574-daa5-4673-aa0c-cbe3e8534c02" },
    "output_format": {
      "container": "wav",
      "encoding": "pcm_s16le",
      "sample_rate": 44100
    },
    "language": "en"
  }' \
  --output /tmp/out.wav
```

## Mental model (for LLMs)

- **Sonic** = TTS; **Ink** = STT (e.g. Ink-Whisper for conversational use cases—see [STT models](https://docs.cartesia.ai/build-with-cartesia/stt-models/latest)).
- **Line** = separate product: you deploy **your** Python agent; Cartesia runs STT/TTS/telephony around it—different from “call TTS API from my server.”
- **Concurrency / quota:** Handle `429`-class and structured `concurrency_limited` / `quota_exceeded` per [API errors](https://docs.cartesia.ai/use-the-api/api-errors).

## Related material in this repo

- **Line voice agents:** [line-voice-agent](../line/SKILL.md)
- **Link hub:** [references/resources.md](references/resources.md)

## Common mistakes

1. **Mixing or guessing `Cartesia-Version`** — pick one date from docs / `llms.txt`, test your paths, and keep it consistent everywhere (wrong date → subtle breakage or legacy error shapes).
2. **API keys in frontend code** — use access tokens from your backend.
3. **Wrong auth header style** — examples use **`Authorization: Bearer`**; match current docs, not old snippets.
4. **Using this skill for `cartesia deploy` / Line** — switch to **line-voice-agent**.
