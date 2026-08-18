---
name: claurify
description: >-
  Claurify a messy Claude ask: rewrite it with XML structure and success
  criteria, pick the cheapest Claude model that will still do the job, and set
  effort. If the user already named a model, only suggest effort. Ask 2-3
  clarifying questions when the ask is too vague to spend tokens well. Use when
  the user wants to claurify, rewrite, optimize, structure, or engineer a Claude
  prompt; pick a Claude model or effort; prepare a prompt for Claude Code, the
  API, Claude.ai, or Cursor; or mentions prompting best practices, XML tags, or
  Fable/Opus/Sonnet prompting.
---

# Claurify

Turn a messy Claude ask into a spec Claude will follow, then pick the cheapest model and effort that will still do the job.

Default harness: Claude Code. Detect API / Claude.ai / Cursor from context; if the harness changes the rewrite, ask once.

## Workflow

1. **Parse.** Extract the raw prompt, optional model, optional effort, optional harness.
2. **Clarify if needed.** If success criteria, output shape, or hard constraints are missing *and* guessing would change the work, stop. Ask at most 2–3 questions. Do not rewrite until those answers exist. Do not invent a bigger task.
3. **Load references.** Always read [references/universal.md](references/universal.md). Then:
   - Model named → that model's file only, plus [references/model-effort.md](references/model-effort.md) for effort.
   - No model → [references/model-effort.md](references/model-effort.md), then the chosen model's file.
4. **Rewrite.** Apply universal rules, then the model overlay. Do not pad. Do not invent files, numbers, or constraints the user did not give.
5. **Return** the output template. Nothing else.

Model files: [fable-5.md](references/fable-5.md), [opus-5.md](references/opus-5.md), [sonnet-5.md](references/sonnet-5.md), [opus-4-8.md](references/opus-4-8.md), [opus-4-7.md](references/opus-4-7.md), [opus-4-6.md](references/opus-4-6.md). Worked examples: [examples.md](examples.md).

## When to ask

Ask only when a wrong guess would produce materially different work:

- What does done look like? (tests pass, schema, file paths, length)
- What must not change? (scope, style, files off-limits)
- Who is the reader, or which harness will run it?

Do not ask for a model if the user wants you to pick one. Do not ask questions you can answer from the prompt, attached files, or conversation.

Use the platform's question tool when available (`AskUserQuestion` in Claude Code, `AskQuestion` in Cursor). Otherwise ask in the reply and wait.

## Rewrite rules (always)

From Anthropic prompting best practices. Details in [references/universal.md](references/universal.md).

- Lead with a direct action verb and the success criteria.
- Add the *why* only when it changes behavior.
- XML-tag mixed content: `<task>`, `<success>`, `<context>`, `<constraints>`, `<output>`, `<examples>`.
- Long documents at the top, question at the end. Quote-then-answer on big docs.
- Tell Claude what to do, not a list of what not to do. Convert "don't" into the desired behavior, then add one constraint tag if a hard boundary remains.
- Point at files; do not paste them unless the prompt will run somewhere with no filesystem.
- One-sentence domain role when it constrains expertise or tone (`You are a Python coding assistant.`). Official docs still recommend this. Skip empty expert fluff that does not change behavior.
- Prefer "think carefully" over hand-written chain-of-thought. Never tell Fable to transcribe its thinking.
- Match prompt length to the task. Cover the substance; no filler sections.

Apply the loaded model overlay on top of these rules. If a model file contradicts a universal rule, the model file wins for that model.

## Model and effort

If the user named a model, **do not suggest a different one**. Rewrite to that model's overlay and recommend effort only.

If they did not, pick the cheapest model that can do the job. Effort is the cost knob. Full table: [references/model-effort.md](references/model-effort.md).

| Task | Model | Effort |
|---|---|---|
| Extract, classify, format, lookup | `claude-sonnet-5` | `low` |
| Single-file edit, short analysis, email | `claude-sonnet-5` | `medium` |
| Multi-file feature, PRD, research | `claude-sonnet-5` | `high` |
| Judgment-heavy analysis on a multi-file feature | `claude-opus-5` | `medium` |
| Hard coding, review, long-horizon agent | `claude-opus-5` | `high` or `xhigh` |
| Days-long, highly ambiguous, first-shot systems | `claude-fable-5` | `high` (`xhigh` if frontier) |
| User locked to 4.8, coding/agentic | `claude-opus-4-8` | `xhigh` |
| User locked to 4.7, coding/agentic | `claude-opus-4-7` | `xhigh` |
| User locked to 4.6 | `claude-opus-4-6` | `high` |

Suggest Fable only when a weaker model would likely fail or burn more retries. Say that in one sentence in **Why**.

Map aliases (`sonnet`, `opus`, `fable`, `opus 5`, `4.8`, `4.7`, `4.6`) to the IDs above. Unknown model: ask once, or treat as "not provided" and pick.

## Output template

Use this shape. Omit the **Model** heading when the user already named a model. Do not wrap the prompt in a markdown fence unless the user asked for one; they need to copy it.

```markdown
## Prompt
<copy-ready rewritten prompt>

## Model
claude-sonnet-5

## Effort
medium

## Why
One short paragraph: task class, why this model, why this effort, what was added or removed.

## Run
claude --model claude-sonnet-5 --effort medium
```

For API users, put the model and `output_config.effort` in **Run** instead of the `claude` CLI line.

The rewritten prompt is machine instructions. Do not humanize it. Keep **Why** short and concrete.
