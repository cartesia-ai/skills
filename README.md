# Cartesia Skills

Agent skills for Cartesia developer products. These skills follow the Agent Skills specification.

## Available Skills

### line-voice-agent

Build voice agents with the Cartesia Line SDK. Supports 100+ LLM providers via LiteLLM with tool calling, multi-agent handoffs, and real-time interruption handling.

**Capabilities:**

- LLM integration (Anthropic, OpenAI, Gemini, Azure, 100+ via LiteLLM)
- Built-in tools: end_call, transfer_call, send_dtmf, web_search
- Custom tool types: loopback, passthrough, handoff
- Multi-agent coordination and routing
- Background tool execution for complex reasoning
- Real-time interruption handling

## Configuration

Voice agents require a Cartesia API key and at least one LLM provider API key.

```bash
export CARTESIA_API_KEY="your-cartesia-key"
export ANTHROPIC_API_KEY="your-anthropic-key"  # or OPENAI_API_KEY, GEMINI_API_KEY, etc.
```

Get your Cartesia API key from [play.cartesia.ai](https://play.cartesia.ai).
