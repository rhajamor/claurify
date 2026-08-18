# Claude Fable 5 overlay

Source: [Prompting Claude Fable 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-fable-5).

Use for long-horizon, ambiguous, or first-shot systems. Not for extract/classify/single-file.

## Strip, do not add

Fable follows short instructions. Skills written for older models are often too prescriptive and can hurt. Remove:

- Step-by-step verification rituals
- "After every N tool calls, summarize"
- "Double-check before responding"
- "Echo / transcribe / show your thinking" (can trigger `reasoning_extraction` refusal)
- Extra scaffolding that exists only because a prior model under-triggered

Keep: success criteria, scope, irreversible-action pause, file pointers.

## Add if missing

**Act, don't survey**

```text
When you have enough information to act, act. Do not re-derive facts already established, re-litigate a decision the user already made, or narrate options you will not pursue. If you are weighing a choice, give a recommendation, not an exhaustive survey.
```

**Scope (especially at high/xhigh)**

```text
Don't add features, refactor, or introduce abstractions beyond what the task requires. Don't design for hypothetical future requirements. Only validate at system boundaries.
```

**Checkpoints**

```text
Pause for the user only when the work genuinely requires them: a destructive or irreversible action, a real scope change, or input only they can provide. If you hit one of these, ask and end the turn.
```

**Ground progress** (long autonomous runs)

```text
Before reporting progress, audit each claim against a tool result from this session. Only report work you can point to evidence for. If tests fail, say so with the output.
```

**Readability of the final message**

```text
Lead with the outcome. First sentence after finishing answers "what happened" or "what did you find." Supporting detail after. Be selective, not telegraphic: no arrow chains, no shorthand the user never saw.
```

**Boundaries when they were thinking out loud**

```text
When the user is describing a problem or asking a question rather than requesting a change, the deliverable is your assessment. Don't apply a fix until they ask.
```

**Subagents:** delegate independent subtasks and keep working. Prefer async over blocking. Do not spawn several when one is enough.

**Memory:** if the harness has a notes dir, one lesson per file, update don't duplicate, delete wrong notes.

**Autonomous pipelines:** proceed on reversible actions that follow from the request. Before ending a turn, if the last paragraph is a plan or a promise, do the work with tool calls.

**Context-budget:** do not surface remaining-token countdowns. If the harness must, add "You have ample context remaining. Do not stop or suggest a new session on account of context limits."

Give the reason for the request (`I'm working on X for Y. They need Z. With that in mind: ...`). Fable uses intent.

## Effort

Default `high`. `xhigh` for capability-sensitive work. `medium`/`low` for routine work; they often beat prior-model `xhigh`. Large `max_tokens` at high/xhigh (thinking + text share the cap).

## Do not

- Instruct it to reproduce internal reasoning in the user-visible reply.
- Suggest Fable for a task Sonnet or Opus 5 will finish cheaper.
