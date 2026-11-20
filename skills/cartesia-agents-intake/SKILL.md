---
name: cartesia-agents-intake
description: Work out what a Cartesia voice agent should do before building it, and write it up as an agent brief. Use when the user wants a new voice agent, phone agent, or receptionist but has only a rough idea ("I want an agent that books appointments"), or is brainstorming what an agent could do. Once the brief is agreed, build the agent with the cartesia-agents skill.
---

# Voice agent intake

Most weak voice agents come from a vague goal, not a weak prompt. Before building anything, agree with the user on what the agent is for and how they will know it works, then write that down as an agent brief. The brief drives the agent's instructions, the test calls, and the evaluation metrics.

## How to run the intake

- Ask two or three questions at a time, not a form. Start with purpose and callers; the rest often follows.
- Propose answers instead of asking open questions when you can make a sensible guess: "Most clinics transfer billing questions to the front desk. Should this one?"
- Use what the user already gave you, including documents, a website, or an existing script. Don't ask for it again.
- Stop asking once every section of the brief has an answer or an agreed default. Mark anything still unknown as an open question rather than blocking on it.

## What to find out

**Purpose**
- Who does the agent speak for, and who calls it (or whom does it call)?
- What does a successful call achieve? Name the one outcome that matters most.
- Inbound, outbound, or both? Phone, web, or inside the user's app?

**Scope**
- The main tasks, in the order a call usually goes.
- What the agent must not do or discuss, and what it says when asked.
- When it hands off to a person, how (transfer to which number), and what happens out of hours.
- When it ends the call.

**Information**
- What it must collect from the caller, and in what form (name spelled out, date of birth, order number).
- What it must know: short fixed facts, or longer material such as FAQs and policies.
- What changes per caller and is known before the call starts (name, account, appointment time).

**Systems**
- What it must look up or change during the call, and which system holds it.
- Whether the user's system needs to hear about calls as they happen.

**Voice and manner**
- Persona, tone, and the impression the business wants to leave.
- Language and accent, and whether callers may switch language.

**Constraints**
- Legal or compliance needs: recording notices, disclaimers, identity checks, data the agent must not repeat back.
- Call volume and hours, if the user knows them.

**Success**
- Three to five checks that a reviewer could answer yes or no about any call, such as "Did the agent confirm the appointment time before ending?"
- The calls that would worry the user most.

## Write the brief

Summarize what you heard in two or three sentences and ask whether that is the agent they want. Then write the brief, in the conversation or as a file if the user is working in a repo:

```markdown
# <Agent name> brief

## Purpose
<who it speaks for, who it talks to, the main outcome, channel>

## Call flow
1. <opening>
2. <main tasks in order>
3. <how the call ends>

## Out of scope
- <topic>: <what the agent says instead>

## Handoffs and ending
- Transfer to <number> when <condition>
- End the call when <condition>

## Collect
- <field>: <format and how to confirm it>

## Knowledge
- In the instructions: <short fixed facts>
- In the knowledge base: <documents>
- Per caller: <{{variables}}>

## Integrations
- <tool>: <system, what it reads or changes, when to call it>

## Voice
<persona, tone, language>

## Constraints
- <compliance item>

## Success checks
- <yes-or-no check>

## Test calls
- <scenario>: <what should happen>

## Open questions
- <unknown, and the default used until it is answered>
```

Turn each success check into at least one test call, including the calls the user worries about most. The same checks become evaluation metrics later.

## Next

Once the user agrees the brief, build the agent with the cartesia-agents skill, and write its instructions from the brief with the cartesia-agents-prompting skill.
