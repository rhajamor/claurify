# Claude Opus 4.6 overlay

Sources: [Prompting best practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices) (Opus 4.6 sections), [Effort](https://platform.claude.com/docs/en/build-with-claude/effort), [Migration guide](https://platform.claude.com/docs/en/about-claude/models/migration-guide).

No dedicated prompting-claude-opus-4-6 page exists. This file is the 4.6-specific material from those docs.

## Exploration and over-prompting

Does more upfront exploration than prior models, especially at higher effort: gathers context and pursues extra research threads without being asked. If an older prompt pushed thoroughness or tool use, dial it back.

- Replace "Default to using [tool]" with `Use [tool] when it would enhance your understanding of the problem.`
- Remove "If in doubt, use [tool]" (overtriggers).
- If it stays aggressive, lower `effort`.

Commit language when it thinks too long:

```text
When you're deciding how to approach a problem, choose an approach and commit to it. Avoid revisiting decisions unless new information directly contradicts your reasoning. If you're weighing two approaches, pick one and see it through.
```

## Tools

More responsive to the system prompt than earlier Opus. Aggressive `CRITICAL: You MUST use this tool` language overtriggers. Ordinary `Use this tool when...` is enough.

## Overeagerness

Creates extra files, extra abstractions, and unrequested flexibility. Add the scope block from [universal.md](universal.md) (only the requested change; no helpers for one-off work; validate only at system boundaries).

## Autonomy

Without guidance it may force-push, delete, or post to shared systems. If the user wants a pause on irreversible work, add:

```text
Take local, reversible actions (edits, tests) without asking. For destructive, hard-to-reverse, or externally visible actions (force-push, rm -rf, commenting on PRs, shared infra), ask first. Do not bypass safety checks (--no-verify) as a shortcut.
```

## Subagents

Strong predilection for subagents, including when grep would do. Cap them:

```text
Use subagents when tasks can run in parallel, need isolated context, or are independent workstreams. For simple tasks, sequential ops, single-file edits, or work that must share state, work directly.
```

## Thinking

Off unless `thinking: {type: "adaptive"}`. Adaptive is recommended for agentic work. `budget_tokens` still works but is deprecated. Prefer `effort` (or `max_tokens` as a hard cap). Prefills on the last assistant turn return 400.

## Effort

Supports `low`, `medium`, `high`, `max`. **No `xhigh`.** Default `high`. Use `medium` to cut exploration. `max` for the hardest 4.6-era work.

If the user is locked to 4.6, stay on `claude-opus-4-6`. Do not silently upgrade to 4.7 or 5.
