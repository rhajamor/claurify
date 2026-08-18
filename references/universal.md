# Universal prompting (all current Claude models)

Authoritative: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices), [Best practices for prompt engineering (2026)](https://claude.com/blog/best-practices-for-prompt-engineering).

Apply these unless a model overlay says otherwise.

## Clarity

Claude follows what you wrote, not what you meant. A colleague with no project context should be able to follow the prompt.

- Direct action verbs: Write, Fix, Extract, Rename, Analyze.
- State the output shape and the success test.
- If you want above-and-beyond, say so. Latest models will not infer it from a vague ask.
- Sequential steps as a numbered list only when order or completeness matters.

## Motivation

A one-line *why* beats a blanket NEVER. Claude generalizes from the reason.

Less: `NEVER use ellipses`
More: `This will be read by a TTS engine, so write ellipses as the word "pause" or omit them.`

## Examples

3–5 examples when format, tone, or edge cases are the point. Wrap in `<examples><example>`. Make them relevant, diverse, and free of patterns you do not want copied.

## XML

Use tags when the prompt mixes instructions, context, examples, and variable input.

```xml
<task>...</task>
<success>...</success>
<context>...</context>
<constraints>...</constraints>
<output>...</output>
<examples>
  <example>...</example>
</examples>
```

Consistent, descriptive names. Nest documents:

```xml
<documents>
  <document index="1">
    <source>notes.txt</source>
    <document_content>...</document_content>
  </document>
</documents>
```

## Role

One sentence, only if it constrains domain or tone (`You are a Python coding assistant.`). Skip generic expert theater.

## Long context (20k+ tokens)

- Documents first, query last (up to ~30% better on multidocument tasks).
- Ask Claude to quote relevant spans before answering.

## Format

- Say what to do, not what to avoid. "Write flowing prose paragraphs" beats "do not use markdown."
- Match the prompt's formatting to the desired output.
- For math in plain text, say so; otherwise Claude may emit LaTeX.
- Prefills are gone on 4.6+. Use structured outputs, XML wrappers, or "respond with no preamble."

## Tools and action

- "Change X" / "Make these edits" if you want implementation. "Suggest" yields suggestions.
- Independent tool calls in parallel; sequential only when a later call needs an earlier result.
- Opus 4.6+ overtriggers if you shout `CRITICAL: You MUST use this tool`. Ordinary "Use this tool when..." is enough.

## Thinking

- Adaptive thinking + `effort` is the control. Do not hand-write chain-of-thought unless thinking is off.
- Opus 5: keep thinking on; lower effort instead of disabling. Disabled thinking can leak tool calls as text or internal XML tags.
- Do not add "double-check" / "include a verification step" on Opus 5; it already verifies and those lines cause over-verification.
- On older models, "Before you finish, verify against [criteria]" is still useful.

## Agentic

- Point at files. Persist progress in `progress.txt` / `tests.json` for multi-window work.
- Confirm before irreversible actions (force-push, delete, shared infra).
- Cap subagents: only independent, sizeable, parallel work. Not for a handful of tool calls, not to verify your own work.
- Scope: only the requested change. No drive-by refactors, extra abstractions, or comments on untouched code.
- "Never speculate about code you have not opened."
- Frontend: a concrete palette and type, or "propose 4 directions then implement the pick." Generic "make it clean" just swaps one default for another.

## Effort vs verbosity

`effort` controls thinking and tool spend, not visible length. To shorten user-facing text, prompt for conciseness. See [model-effort.md](model-effort.md).
