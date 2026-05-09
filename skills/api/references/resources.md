# Cartesia API — resources for agents

## Documentation (human + LLM)

| Resource | URL |
|----------|-----|
| Docs home | https://docs.cartesia.ai |
| API conventions (auth, versioning, errors) | https://docs.cartesia.ai/use-the-api/api-conventions |
| API errors reference | https://docs.cartesia.ai/use-the-api/api-errors |
| `llms.txt` (concise index) | https://docs.cartesia.ai/llms.txt |
| `llms-full.txt` (broader index) | https://docs.cartesia.ai/llms-full.txt |
| Agent skills (install) | https://docs.cartesia.ai/tools/ai/agent-skills |
| MCP | https://docs.cartesia.ai/tools/ai/mcp |
| Playground / API keys | https://play.cartesia.ai/keys |
| Access tokens (client-safe) | https://docs.cartesia.ai/api-reference/auth/access-token |

## SDKs (first choice for app code)

| SDK | Repository |
|-----|------------|
| Python | https://github.com/cartesia-ai/cartesia-python |
| JavaScript / TypeScript | https://github.com/cartesia-ai/cartesia-js |

## Optional tooling

| Tool | URL |
|------|-----|
| MCP server | https://github.com/cartesia-ai/cartesia-mcp |
| Short curl capsule | https://cartesia.sh/openclaw.md |

## Agent Skills spec

| Spec | URL |
|------|-----|
| Agent Skills | https://agentskills.io/home |

## Version header values (check OpenAPI for current enum)

Documented `Cartesia-Version` dates include: `2024-06-10`, `2024-11-13`, `2025-04-16`, `2026-03-01`. New integrations should pick the latest documented date and test against it.
