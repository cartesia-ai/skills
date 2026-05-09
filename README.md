<p align="center">
  <img src="assets/skills_banner.png" alt="Cartesia Skills" width="100%">
</p>

# Cartesia Agent Skills

[![Install via skills.sh](https://img.shields.io/badge/skills.sh-install-green)](https://skills.sh/cartesia-ai/skills)

**Agent Skills** are a first-class developer surface for [Cartesia](https://cartesia.ai/)—the same tier as our [documentation](https://docs.cartesia.ai), [SDKs](https://github.com/cartesia-ai/cartesia-python), and [MCP server](https://github.com/cartesia-ai/cartesia-mcp). Each skill is a versioned `SKILL.md` (plus optional `references/`) so **coding agents** integrate TTS, STT, voices, and **Cartesia Line** consistently, with correct **auth**, **`Cartesia-Version`**, and product boundaries.

This repository follows the [Agent Skills](https://agentskills.io/home) convention. Each skill lives under `skills/<area>/<skill-name>/`.

## Install

```bash
npx skills add cartesia-ai/skills
```

Select **cartesia-api** and/or **line-voice-agent** when prompted.

**Docs:** [Agent skills](https://docs.cartesia.ai/tools/ai/agent-skills) on the Cartesia documentation site.

## Available skills

| Skill | Path | Use when |
|-------|------|----------|
| **cartesia-api** | [`skills/developer/cartesia-api`](./skills/developer/cartesia-api) | **Application code**: REST/WebSocket, Sonic TTS, Ink STT, voices, SDKs, optional MCP. |
| **line-voice-agent** | [`skills/line/line-voice-agent`](./skills/line/line-voice-agent) | **Cartesia Line**: CLI, `cartesia deploy`, `VoiceAgentApp`, telephony, multi-agent tools. |

## First-class surfaces (for LLMs and humans)

| Surface | Role |
|---------|------|
| **This repo** | Curated agent instructions + cross-links; **source of truth** for skill wording. |
| **[docs.cartesia.ai](https://docs.cartesia.ai)** | Full API reference, guides, OpenAPI/AsyncAPI. |
| **[llms.txt](https://docs.cartesia.ai/llms.txt)** / **[llms-full.txt](https://docs.cartesia.ai/llms-full.txt)** | Machine-readable doc indexes for RAG and fetches. |
| **SDKs** | [Python](https://github.com/cartesia-ai/cartesia-python), [JS/TS](https://github.com/cartesia-ai/cartesia-js) for production integrations. |
| **[MCP](https://docs.cartesia.ai/tools/ai/mcp)** | Optional IDE tooling; not a substitute for SDKs in apps. |

## Configuration (Line)

Voice agents on Cartesia Line need a **Cartesia** key and at least one **LLM** provider key:

```bash
export ANTHROPIC_API_KEY="your-anthropic-key"  # or OPENAI_API_KEY, GEMINI_API_KEY, etc.
```

Cartesia API keys: [play.cartesia.ai/keys](https://play.cartesia.ai/keys).

## Contributing

See [CONTRIBUTING.md](./CONTRIBUTING.md).

## License

See [LICENSE](./LICENSE).
