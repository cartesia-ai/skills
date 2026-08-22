<p align="center">
  <img src="assets/skills_banner.png" alt="Cartesia Skills" width="100%">
</p>

# Cartesia Agent Skills

[![Install via skills.sh](https://img.shields.io/badge/skills.sh-install-green)](https://skills.sh/cartesia-ai/skills)

**Agent Skills** help coding agents ship Cartesia integrations the way we document them: **auth**, **`Cartesia-Version`**, and when to use Line vs raw APIs. Each skill is a versioned `SKILL.md` (plus optional `references/`). Contracts live on [docs.cartesia.ai](https://docs.cartesia.ai); use the [Python](https://github.com/cartesia-ai/cartesia-python) and [JS/TS](https://github.com/cartesia-ai/cartesia-js) SDKs in app code and optional [MCP](https://docs.cartesia.ai/tools/ai/mcp.md) in the IDE if you want it.

This repository follows the [Agent Skills](https://agentskills.io/home) convention. It is a [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) and a Cursor plugin (`name: cartesia`). Product skills live under **`skills/cartesia-api/`** (HTTP/WebSocket + client libraries) and **`skills/line-voice-agent/`** (Cartesia Line).

## Install

### Any agent (skills.sh)

```bash
npx skills add cartesia-ai/skills
```

Select **cartesia-api** and/or **line-voice-agent** when prompted.

### Claude Code (plugin)

Inside Claude Code, add this repo as a marketplace:

```text
/plugin marketplace add cartesia-ai/skills
```

Then, install the **cartesia-skills** plugin:

```text
/plugin install cartesia-skills@cartesia
```

The plugin vends all skills in this repo.

### Cursor (plugin)

In Cursor chat:

```text
/add-plugin cartesia
```

Or install from the [Cursor Marketplace](https://cursor.com/marketplace) (after listing). The plugin registers hosted MCP at `https://mcp.cartesia.ai/mcp` (sign in via the Playground) and loads the skills, rule, and `/build-voice-agent` command in this repo.

Local test before the marketplace listing:

```bash
ln -s /path/to/skills ~/.cursor/plugins/local/cartesia
```

Then reload the Cursor window. Pasting `https://cartesia.ai` shows **Add Cartesia** / **Try in Chat** only after Cursor lists the plugin and maps Cartesia domains.

### Updating the Claude Code plugin

Update the `cartesia` marketplace:

```text
/plugin marketplace update cartesia
```

Then update the plugin:

```text
/plugin update cartesia-skills
```

Reload plugins:

```text
/reload-plugins
```

## Available skills

| Skill | Path | Use when |
|-------|------|----------|
| **cartesia-api** | [`skills/cartesia-api`](./skills/cartesia-api) | **Application code**: REST/WebSocket, Sonic TTS, Ink STT, voices, SDKs, optional MCP. |
| **line-voice-agent** | [`skills/line-voice-agent`](./skills/line-voice-agent) | **Cartesia Line**: CLI, `cartesia deploy`, `VoiceAgentApp`, telephony, multi-agent tools. |

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

```text
.cursor-plugin/plugin.json   # Cursor Marketplace plugin (name: cartesia)
.claude-plugin/
  marketplace.json           # Claude Code marketplace ("cartesia")
  plugin.json                # cartesia-skills plugin
.mcp.json                    # hosted MCP (https://mcp.cartesia.ai/mcp)
plugin.json                  # Agent Plugins manifest
commands/build-voice-agent.md
rules/cartesia.mdc
skills/
  cartesia-api/              # HTTP/WebSocket, SDKs, optional MCP
  line-voice-agent/          # Line SDK, CLI, telephony
```

Each skill folder has a `SKILL.md` (whose YAML **`name`** matches the folder) and optional `references/`.
