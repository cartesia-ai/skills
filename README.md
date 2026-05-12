<p align="center">
  <img src="assets/skills_banner.png" alt="Cartesia Skills" width="100%">
</p>

# Cartesia Agent Skills

[![Install via skills.sh](https://img.shields.io/badge/skills.sh-install-green)](https://skills.sh/cartesia-ai/skills)

**Agent Skills** help coding agents ship Cartesia integrations the way we document them: **auth**, **`Cartesia-Version`**, and when to use Line vs raw APIs. Each skill is a versioned `SKILL.md` (plus optional `references/`). Contracts live on [docs.cartesia.ai](https://docs.cartesia.ai); use the [Python](https://github.com/cartesia-ai/cartesia-python) and [JS/TS](https://github.com/cartesia-ai/cartesia-js) SDKs in app code and optional [MCP](https://docs.cartesia.ai/tools/ai/mcp.md) in the IDE if you want it.

This repository follows the [Agent Skills](https://agentskills.io/home) convention. Product skills live under **`skills/api/`** (HTTP/WebSocket + client libraries) and **`skills/line/`** (Cartesia Line).

## Install

```bash
npx skills add cartesia-ai/skills
```

Select **cartesia-api** and/or **line-voice-agent** when prompted.

**Docs:** [Agent skills](https://docs.cartesia.ai/tools/ai/agent-skills) on the Cartesia documentation site.

## Available skills

| Skill | Path | Use when |
|-------|------|----------|
| **cartesia-api** | [`skills/api`](./skills/api) | **Application code**: REST/WebSocket, Sonic TTS, Ink STT, voices, SDKs, optional MCP. |
| **line-voice-agent** | [`skills/line`](./skills/line) | **Cartesia Line**: CLI, `cartesia deploy`, `VoiceAgentApp`, telephony, multi-agent tools. |

## Where to look (for LLMs and humans)

| Surface | Role |
|---------|------|
| **This repo** | Curated `SKILL.md` copy and cross-links (docs + OpenAPI stay the contract). |
| **[docs.cartesia.ai](https://docs.cartesia.ai)** | Full API reference, guides, OpenAPI/AsyncAPI. |
| **[llms.txt](https://docs.cartesia.ai/llms.txt)** / **[llms-full.txt](https://docs.cartesia.ai/llms-full.txt)** | Machine-readable doc indexes for RAG and fetches. |
| **SDKs** | [Python](https://github.com/cartesia-ai/cartesia-python), [JS/TS](https://github.com/cartesia-ai/cartesia-js) for production integrations. |
| **[MCP](https://docs.cartesia.ai/tools/ai/mcp.md)** | Optional IDE tooling; not a substitute for SDKs in apps. |

## Configuration (Line)

Voice agents on Cartesia Line need a **Cartesia** key and at least one **LLM** provider key:

```bash
export ANTHROPIC_API_KEY="your-anthropic-key"  # or OPENAI_API_KEY, GEMINI_API_KEY, etc.
```

Cartesia API keys: [play.cartesia.ai/keys](https://play.cartesia.ai/keys).

## Repository layout

```
skills/
  api/           # cartesia-api: HTTP/WebSocket, SDKs, optional MCP
  line/          # line-voice-agent: Line SDK, CLI, telephony
```

Each area has a `SKILL.md` and optional `references/`. `npx skills add` matches the YAML **`name`** in that file (see table above), not the folder path.
