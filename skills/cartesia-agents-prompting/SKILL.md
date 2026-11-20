---
name: cartesia-agents-prompting
description: Write or revise the instructions (system prompt) for a Cartesia voice agent. Use when drafting `config.instructions` for a new Managed Agent, or when an agent says the wrong thing, ignores a rule, rambles, or handles a kind of call badly and the user wants the prompt fixed. Apply the change and test it with the cartesia-agents skill.
---

# Writing voice agent instructions

`config.instructions` is the agent's system prompt. Every word of the agent's reply is spoken aloud, in real time, to someone who cannot scroll back. Write for that.

## Structure

Use the four sections from [Agent configuration](https://docs.cartesia.ai/agents/configuration.md#instructions-and-initial-message): identity and personality, speaking rules, objectives, guardrails. If there is an agent brief, fill them from it: purpose and voice go in identity, call flow and collect go in objectives, out of scope and handoffs go in guardrails.

## What a voice call needs

- **Everything is spoken.** No markdown, lists, emoji, or links. Say how to read numbers, dates, prices, emails, and codes aloud, for example phone numbers digit by digit in groups.
- **Short turns.** One or two sentences, and one question at a time.
- **Read back what matters.** Have the agent repeat names, numbers, times, and addresses and wait for the caller to confirm.
- **Say when to end and when to transfer.** Name the conditions and the system tool to use. Without them, agents linger or hang up early.
- **Say when to use each tool.** Name the tool, the condition, and what to do with the result or a failure. A tool the instructions never mention is rarely called.
- **Cover the awkward turns.** What to say when the caller is silent, unclear, off topic, or asks for something the agent cannot do.
- **Keep it short.** Put short fixed facts in the instructions and long or changing content in the knowledge base. Rules buried in a long prompt are followed less reliably.

## Revising after a test call

- Start from what happened in the call, quoted from `get_agent_call`, and name the rule that should have prevented it.
- Fix the general behavior, not the one phrase. "Confirm any date before booking" beats "say Tuesday the 4th back to the caller".
- Change one thing per version, and put the reason in `version_description`.
- Edit or replace an existing rule before adding a new one. A prompt that grows by one rule per bad call ends up contradicting itself.
- If a rule is already clear and the agent still breaks it, the fix may not be in the prompt: check the model, the tool description, or the turn settings.

Read the current instructions with `get_agent` before rewriting them, and show the user the before and after of anything you change.
