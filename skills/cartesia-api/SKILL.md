---
name: cartesia-api
description: Integrate Cartesia speech APIs (TTS, STT, voices) in application code or coding-agent workflows. Use when the user asks about Cartesia REST/WebSocket APIs, SDKs, API keys, Sonic TTS, Ink STT, voice IDs, access tokens, or embedding voice in an app. For hosted voice agents on the web or phone, use Cartesia Managed Agents (https://docs.cartesia.ai/agents/introduction.md). Use the cartesia-line skill only to maintain an existing Line SDK agent.
compatibility: Requires a Cartesia API key from https://play.cartesia.ai/keys for server-side calls. Client apps must use short-lived access tokens, not raw API keys. Optional hosted MCP is https://mcp.cartesia.ai/mcp (OAuth via the Playground). Local uvx cartesia-mcp is for development.
---

# Cartesia Voice & Speech APIs

Cartesia provides **text-to-speech (Sonic)**, **speech-to-text (Ink)**, **voices** (library, clone, localize), and related **HTTPS** and **WebSocket** APIs. This skill covers **application integration** and **agent-assisted coding**, including **[Managed Agents](#voice-agents-with-managed-agents)** for hosted voice agents. Use **[cartesia-line](../cartesia-line/SKILL.md)** only to maintain an existing Line SDK agent; Line stops running on 2026-11-20.

## Core directives

- **HTTPS only:** All HTTP endpoints use `https://api.cartesia.ai`. WebSockets use `wss://`
  - HTTP may be unsupported; keys used over HTTP can be auto-rotated
- **API version:** Cartesia's API is dated (`Cartesia-Version: YYYY-MM-DD`). Pin **one** version across your services and treat bumping it like a major dependency upgrade — a new version can carry breaking changes — so it's a fixed choice, not a runtime/per-call variable.
  - **With an SDK (recommended):** the SDK already pins a `Cartesia-Version` it was built and tested against. Let it manage that — do **not** override it (e.g. via `default_headers`) with your own date, since the SDK may not work against an arbitrary version. To move to a newer API version, upgrade the SDK.
  - **Calling the API directly (no SDK):** you must send `Cartesia-Version` yourself. Browser WebSockets can't set handshake headers, so pass it as **`?cartesia_version=...`** (query wins if both are present).
  - Optional: `GET https://api.cartesia.ai/` returns `{"ok":true,"version":"..."}` (the gateway's current default), useful when wiring a new client.
- **Server-side auth:** Use **`Authorization: Bearer <api_key>`** with your Cartesia API key (`sk_car_...`).
- **Client apps (browser / mobile):** **Never** embed API keys. Have your backend mint a **short-lived access token** and use `Authorization: Bearer <access_token>`
  - For WebSockets from browsers, pass the token as **`?access_token=<token>`** (headers are not available on WS handshake)
  - The JS/TS SDK (v3+) runs in the browser with an access token — prefer it over hand-rolling the WebSocket (see its Browser Examples below); don't assume the SDK is server-only
  - See [Access Token API](https://docs.cartesia.ai/api-reference/auth/access-token.md)
- **Errors:** For `Cartesia-Version` **2026-03-01** and newer, errors are **structured JSON** (`error_code`, `title`, `message`, `request_id`, optional `doc_url`)
  - Older versions may return legacy plain-text or legacy JSON structures
  - See [API Errors](https://docs.cartesia.ai/use-the-api/api-errors.md) and [API conventions](https://docs.cartesia.ai/use-the-api/api-conventions.md)
- **Source of truth:** Prefer [docs](https://docs.cartesia.ai) since they are updated first before SDKs / plugins / integrations
  - **Fetch docs as Markdown:** append `.md` to any `docs.cartesia.ai` page to get the agent-readable Markdown (e.g. `https://docs.cartesia.ai/api-reference/stt/transcribe.md`); the bare URL is the human HTML page. Prefer the `.md` form when fetching docs programmatically
  - For machine index: [`llms.txt`](https://docs.cartesia.ai/llms.txt) and [`llms-full.txt`](https://docs.cartesia.ai/llms-full.txt)
- **Optional MCP:** hosted server `https://mcp.cartesia.ai/mcp` (OAuth via the Playground) lists voices, runs TTS/STT, clones, and manages pronunciation dictionaries in Cursor / Claude
  - MCP does **not** replace API / SDKs for production
  - Local stdio (`uvx cartesia-mcp`) is for development; see [MCP docs](https://docs.cartesia.ai/tools/ai/mcp.md)

### Text-to-speech with Sonic (generating audio)

- **Choosing a TTS model:** See [TTS models](https://docs.cartesia.ai/build-with-cartesia/tts-models/latest.md) for current model IDs, supported languages, and features. Don't trust a model ID from memory — training data goes stale
  - As of October 2026 the current model is `sonic-3.6` (pinned snapshot `sonic-3.6-2026-08-27`)
  - `sonic-2`, `sonic-turbo` and `sonic-3-2025-10-27` stop working on 2026-10-20 and return `model_sunsetted`; `sonic`, `sonic-english` and `sonic-multilingual` are already retired; `sonic-latest` and `sonic-3-latest` are deprecated aliases. See [API changes](https://docs.cartesia.ai/build-with-cartesia/tts-models/api-changes.md)
- **Voice IDs:** Don't _invent_ a voice ID — a made-up UUID won't resolve. Real voice IDs are **stable**, so hardcoding a real one as a constant is fine and common; copy one from the [playground](https://play.cartesia.ai) or [List Voices](https://docs.cartesia.ai/api-reference/voices/list.md). Use List Voices to _discover/choose_ a voice, not as a required call on every request
- **Choosing an endpoint — bytes vs WebSocket:** if the text is known up front (a fixed string, a button press), use **`POST /tts/bytes`** — it streams audio back as it's generated and is simpler. Reach for the **WebSocket** only when the _input text_ arrives incrementally (e.g. piping an LLM's token stream) — the [continuations](https://docs.cartesia.ai/build-with-cartesia/capability-guides/stream-inputs-using-continuations.md) case. Don't default to WebSockets; compare them in [TTS endpoints](https://docs.cartesia.ai/use-the-api/compare-tts-endpoints.md)
- **Output audio format:** See [TTS output audio format](https://docs.cartesia.ai/build-with-cartesia/capability-guides/tts-output-audio-format.md) for containers/encodings/sample rates (`wav` + `pcm_s16le` @ 44.1 kHz is a safe, self-contained default; `mp3` carries no separate `encoding`)

### Speech-to-text with Ink (transcribing audio)

- **Choosing an STT model:** see [STT models](https://docs.cartesia.ai/build-with-cartesia/stt-models/latest.md) for current models and supported languages
- **Choosing an API endpoint:** see [Compare STT Endpoints](https://docs.cartesia.ai/use-the-api/compare-stt-endpoints.md)
- **Input audio:** see [STT input encodings](https://docs.cartesia.ai/build-with-cartesia/capability-guides/stt-input-encodings.md) for supported formats (self-contained files like `.wav` carry their own header)
- **Before you write any STT code**: read [Common STT Pitfalls](https://docs.cartesia.ai/use-the-api/stt/common-pitfalls.md)
  - Don't assume model or API behavior without grounding it in the docs — small client-side mistakes cause obscure but severe accuracy degradation
  - Common client-side errors:
    - Stripping or inserting whitespace in transcripts (read the model's text **verbatim** — no `.strip()`, no normalization)
    - Improperly concatenating transcript pieces
    - Not following the API spec

### Voice agents with Managed Agents

- **What it is:** Cartesia hosts the whole agent: Ink listens, the LLM you choose replies, Sonic speaks, with turn-taking and interruptions handled. You configure instructions, voice, model and tools. See [Managed Agents](https://docs.cartesia.ai/agents/introduction.md) and [Agent configuration](https://docs.cartesia.ai/agents/configuration.md)
- **Connecting an app:** stream audio and events over the [WebSocket API](https://docs.cartesia.ai/line/integrations/websocket-api.md). From a browser, mint an access token on your server and pass it as `?access_token=`; never ship the API key
- **Phone calls:** attach a [Cartesia number](https://docs.cartesia.ai/line/integrations/telephony/cartesia-numbers.md) or [import a Twilio number](https://docs.cartesia.ai/line/integrations/telephony/twilio/integration.md); see [Outbound calling](https://docs.cartesia.ai/line/integrations/telephony/outbound-dialing.md) and [Batch calling](https://docs.cartesia.ai/line/integrations/telephony/batch-calling.md)
- **Actions during a call:** [webhook tools](https://docs.cartesia.ai/agents/webhook-tools.md) call your HTTPS endpoint, [client tools](https://docs.cartesia.ai/agents/client-tools.md) run in your app over the WebSocket, and [system tools](https://docs.cartesia.ai/agents/system-tools.md) handle transfers and hang-ups

### Cartesia Python SDK

`pip install cartesia`

- [README.md](https://raw.githubusercontent.com/cartesia-ai/cartesia-python/refs/heads/main/README.md)
- [All exported resources](https://raw.githubusercontent.com/cartesia-ai/cartesia-python/refs/heads/main/api.md)
- [Examples](https://raw.githubusercontent.com/cartesia-ai/cartesia-python/refs/heads/main/examples/examples.py)
- [Async Examples](https://raw.githubusercontent.com/cartesia-ai/cartesia-python/refs/heads/main/examples/async_examples.py)

Replace `/heads/main/` with `/tags/vX.X.X/` (e.g. `/tags/v3.2.0/`) to source code specific to your SDK version.

### Cartesia TypeScript / JavaScript SDK

`npm i @cartesia/cartesia-js`

- [README.md](https://raw.githubusercontent.com/cartesia-ai/cartesia-js/refs/heads/main/README.md)
- [All exported resources](https://raw.githubusercontent.com/cartesia-ai/cartesia-js/refs/heads/main/api.md)
- [Node / Bun / Deno Examples](https://raw.githubusercontent.com/cartesia-ai/cartesia-js/refs/heads/main/examples/node_examples.ts)
- [Browser Examples](https://raw.githubusercontent.com/cartesia-ai/cartesia-js/refs/heads/main/examples/browser_examples.ts)

Replace `/heads/main/` with `/tags/vX.X.X/` (e.g. `/tags/v3.2.0/`) to source code specific to your SDK version.

## When to use which path

| Goal                                  | Path                                                                                                                                                  |
| ------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| App or backend calling REST/WebSocket | [Python SDK](https://github.com/cartesia-ai/cartesia-python), [JS/TS SDK](https://github.com/cartesia-ai/cartesia-js), or native API requests / fetch |
| IDE agent with MCP                    | Hosted `https://mcp.cartesia.ai/mcp` + docs fallback                                                                                                  |
| Hosted voice agent (web or phone)     | [Managed Agents](https://docs.cartesia.ai/agents/introduction.md)                                                                                    |
| Existing Line SDK agent               | **[cartesia-line](../cartesia-line/SKILL.md)** (Line stops running on 2026-11-20)                                                                     |
| OpenClaw bootstrap                    | `https://cartesia.sh/openclaw.md` then docs / `llms.txt`                                                                                              |

## Quick start (HTTP TTS, one-shot)

Shape only: confirm field names and enums against the [API reference](https://docs.cartesia.ai/llms.txt) for your `Cartesia-Version`:

```bash
curl -X POST "https://api.cartesia.ai/tts/bytes" \
  -H "Authorization: Bearer $CARTESIA_API_KEY" \
  -H "Cartesia-Version: 2026-08-14" \
  -H "Content-Type: application/json" \
  -d '{
    "model_id": "sonic-3.6",
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

- **Sonic** = TTS model used to generate speech
- **Ink** = STT model used to transcribe audio
- **Managed Agents** = Cartesia hosts the voice agent (STT, LLM, TTS, turn-taking, phone numbers); your app connects over a WebSocket or a phone call
- **Line** = the older SDK where you deploy **your** Python agent; maintenance only, it stops running on 2026-11-20
- **Concurrency / quota:** Handle `429`-class and structured `concurrency_limited` / `quota_exceeded` per [API errors](https://docs.cartesia.ai/use-the-api/api-errors.md).

## Language vs locale vs accent

Do not collapse these:

- **language** — coarse code (`en`, `es`)
- **locale** — language-region bucket (`en-US`, `en-GB`)
- **accent** — a specific voice identity inside a locale

One locale can have many accents. `en-US` includes General American and Southern American. Country cannot pick a unique accent. Prefer catalog IDs from the API over display names when the client will persist or send a value back.

## Related material in this repo

- **Existing Line SDK agents:** [cartesia-line](../cartesia-line/SKILL.md)
- **Link hub:** [references/resources.md](references/resources.md)

## Common mistakes

1. **Overriding the SDK's `Cartesia-Version`**: with an SDK, don't set or override the version — it pins one it's tested against; upgrade the SDK to move versions. Only raw HTTP/WS callers send the date themselves, and then keep one date everywhere (wrong date → subtle breakage or legacy error shapes).
2. **API keys in frontend code**: use short-lived access tokens minted by your backend.
3. **Wrong auth header style**: examples use **`Authorization: Bearer`**; match current docs, not old snippets.
4. **Stale SDK / model code from memory**: training data lags the API. Model IDs drift (e.g. `sonic-2`), and the JS SDK's named `import { CartesiaClient }` is deprecated and won't run in browsers — use `import Cartesia from "@cartesia/cartesia-js"` (or the default import). Confirm models against the models docs and SDK usage against the linked README / examples.
5. **Starting a new voice agent on Line**: use Managed Agents; switch to **cartesia-line** only to maintain an existing Line agent.
