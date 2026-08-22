---
description: Build an AI voice agent on Cartesia Line using the Line SDK
argument-hint: [agent-description]
---

# Build a Cartesia Line voice agent

Scaffold a production Line agent from the user's description.

The user invoked this command with: $ARGUMENTS

## Instructions

1. Fetch https://docs.cartesia.ai/line/start-building/quickstart.md and https://docs.cartesia.ai/llms.txt before writing code.
2. Read the `cartesia-line` skill in this plugin (`skills/cartesia-line/SKILL.md`) and the references it links.
3. Do not invent `Cartesia-Version`, Sonic/Ink model IDs, or CLI flags from memory.
4. Use the public CLI (`curl -fsSL https://cartesia.sh | sh`), `cartesia-line`, and `VoiceAgentApp`. Cartesia runs Ink STT and Sonic TTS around the agent.
5. Include `end_call` (or an equivalent) so the agent can hang up.
6. Use a fast conversational model (Haiku / Flash / mini class). Put slow reasoning behind background tools.
7. After scaffolding: how to `cartesia init`, `cartesia deploy`, and `cartesia call`.

## Example usage

```
/build-voice-agent a dentist office that books appointments
/build-voice-agent customer support that can transfer to a human
```
