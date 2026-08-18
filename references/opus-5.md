# Claude Opus 5 overlay

Source: [Prompting Claude Opus 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5).

Strongest on hard multi-file coding, review, and long-horizon agents. Completes full tasks; give the whole spec up front and let it run.

## Strip, do not add

Opus 5 already verifies. These lines waste tokens:

- "Include a final verification step"
- "Use a subagent to verify"
- "Double-check your answer"
- Harness scaffolding that adds a separate verification pass

Remove them when migrating a prompt from 4.6/4.8.

## Add if missing

**Visible length** (effort does not shorten replies)

```text
Keep responses focused, brief, and concise. Keep disclaimers short. Spend most of the response on the main answer. When asked to explain, give a high-level summary unless an in-depth explanation is requested.
```

Near the end of a long system prompt: `<tone_preference>Keep outputs reasonably concise.</tone_preference>`

**Written files:** match length to the task. No filler sections, redundant summaries, or boilerplate.

**Progress narration** (tune down by default for efficiency)

```text
Before your first tool call, say in one sentence what you're about to do. While working, give a brief update only when you find something important or change direction. When you finish, lead with the outcome.
```

**Scope**

```text
Deliver what was asked, at the scope intended. Make routine judgment calls yourself, and check in only when different readings of the request would lead to materially different work. If the request seems mistaken or a better approach exists, say so in a sentence and continue with the task as asked. Finish the whole task, and stop short of actions clearly beyond what was asked.
```

**Subagents** (delegates more readily than prior Opus)

```text
Delegate to a subagent only for large tasks that are genuinely independent and parallelizable. Do not delegate work you can finish yourself in a handful of tool calls, and do not use subagents to verify or double-check your own work. If one subagent can complete the task, use one.
```

Claude Code caps: `CLAUDE_CODE_MAX_SUBAGENT_SPAWN_DEPTH`, `CLAUDE_CODE_MAX_CONCURRENT_SUBAGENTS` (2.1.217+).

**Self-correction narration**

```text
Only correct an earlier statement when the error would change the user's code, conclusions, or decisions. State corrections plainly and briefly. For slips that change nothing, fix them and move on without noting them.
```

## Effort and thinking

Start `high`. Use `low`/`medium` when evals hold. `xhigh` for demanding coding/agents. Effort controls thinking volume, not visible length.

Keep thinking enabled. Disabling thinking (only allowed at `high` or below) can leak tool calls as user-visible text or internal XML tags. If thinking must stay off:

```text
When you use a tool, you may say a brief sentence first. If no tool can express what the user asked for, say so instead of guessing. Do not include internal or system XML tags in your response.
```

Do not name `<thinking>` tags; the general form works better.

Review prompts: ask to report everything with confidence + severity, then filter later. "Only high-severity" is followed literally and drops real bugs.

Vision: give crop/iterative visual tools; that beats thinking-only workarounds from older models.
