# Claude Opus 4.8 overlay

Source: [Prompting Claude Opus 4.8](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-4-8).

Long-horizon agents, knowledge work, vision, memory. Literal at low/medium effort. Favors reasoning over tool calls unless effort is raised.

## Effort

Start **`xhigh` for coding and agentic**. `high` minimum for other intelligence-sensitive work. `medium`/`low` only after you have measured that quality holds. `max` can overthink.

At `low`/`medium` it stays in scope rather than going beyond. Shallow reasoning on a hard problem → raise effort, don't prompt around it. If stuck at `low`: `This task involves multistep reasoning. Think carefully through the problem before responding.`

Thinking is **off** unless `thinking: {type: "adaptive"}`. Steer adaptive triggering the same way as Sonnet 5. At `xhigh`/`max`, set a large max output (start 64k).

## Tools

Tends to reason instead of calling tools. Raise effort to `high`/`xhigh` when you need search, coding tools, or knowledge-work lookups. Or say when and why to use the tool.

## Subagents

Spawns **fewer** subagents by default. Steer explicitly:

```text
Do not spawn a subagent for work you can complete directly in a single response (e.g. refactoring a function you can already see).
Spawn multiple subagents in the same turn when fanning out across items or reading multiple files.
```

## Literalism

Same as Sonnet 5: no silent generalization, no inferred extra work. Scope instructions explicitly (`Apply this to every section`).

## Verbosity

Calibrates to task complexity. For a fixed short style: `Provide concise, focused responses. Skip non-essential context, and keep examples minimal.`

Tone defaults more direct and opinionated, sparse emoji. Add warmth in the prompt if the product needs it: `Use a warm, collaborative tone. Acknowledge the user's framing before answering.`

Drop "after every 3 tool calls, summarize" scaffolding; describe the update cadence if the default is wrong.

## Frontend

Persistent default: warm cream (`#F4F1EA`), serif display (Georgia/Fraunces/Playfair), terracotta/amber. Fine for editorial; wrong for dashboards, fintech, healthcare, enterprise. Generic "don't use cream / make it minimal" just locks a different palette.

Give a concrete spec, or propose 4 directions then implement the pick. Short anti-slop `<frontend_aesthetics>` block is enough; 4.8 needs less frontend prompting than older models.

## Code review

Same harness trap as Sonnet 5 / Opus 5: conservative review prompts lower *reported* recall. Ask for every finding with confidence + severity, filter downstream.

## Interactive vs autonomous

Uses more tokens in multi-turn interactive coding (reasons after each user turn). For efficiency: `xhigh`/`high`, specify the full task in the first turn, reduce back-and-forth.
