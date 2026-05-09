# Contributing

Cartesia **Agent Skills** are a first-class way for coding agents (Cursor, Claude Code, and other [Agent Skills](https://agentskills.io/home) clients) to integrate Cartesia without stale or contradictory snippets.

## Principles

- **Accuracy:** Match [Cartesia documentation](https://docs.cartesia.ai) and OpenAPI. Prefer links over copying endpoint details that change.
- **Auth:** Document **`Authorization: Bearer`** for API keys on servers; **access tokens** for clients; never suggest embedding `sk_car_...` in frontend code.
- **Versioning:** Always mention **`Cartesia-Version`** and point to the docs enum of valid dates.

## Repository layout

```
skills/
  api/                     # REST/WebSocket + Python/JS client libraries (skill name: cartesia-api)
    SKILL.md
    references/
  line/                    # Cartesia Line SDK, CLI, telephony (skill name: line-voice-agent)
    SKILL.md
    references/
```

Install for users: `npx skills add cartesia-ai/skills`

## Changing skills

1. Edit the relevant `SKILL.md` (YAML `name` + `description` drive discovery).
2. Add deep dives under `references/` when the main file gets too long.
3. Update the root **README.md** skill table if you add or rename a skill.
4. Keep **cartesia-api** vs **line-voice-agent** boundaries clear: API + client libs vs deployed Line agents.

Track larger releases with your team (e.g. Linear **Surfaces** / **Cartesia for AIs**).
