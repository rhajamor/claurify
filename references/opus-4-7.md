# Claude Opus 4.7 overlay

There is no dedicated prompting-claude-opus-4-7 page. Official sources: [Effort (Opus 4.7)](https://platform.claude.com/docs/en/build-with-claude/effort#recommended-effort-levels-for-claude-opus-4-7), [Migration guide, 4.6→4.7 behavior](https://platform.claude.com/docs/en/about-claude/models/migration-guide), [Introducing Claude Opus 4.7](https://www.anthropic.com/news/claude-opus-4-7).

Direct upgrade from 4.6. Re-tune prompts: 4.7 follows them literally where 4.6 was looser.

## Effort

Introduced `xhigh` (between `high` and `max`). Claude Code default for 4.7 is `xhigh`. API default remains `high`; set `xhigh` explicitly.

**Start `xhigh` for coding and agentic.** `high` minimum for other intelligence-sensitive work. `medium` for cost-sensitive. `max` only if evals show headroom at `xhigh` (can overthink).

Respects effort **more strictly than 4.6**, especially at `low`/`medium`: stays in scope rather than doing extra. Shallow reasoning on a hard problem → raise effort, do not prompt around it. If stuck at `low`: `This task involves multistep reasoning. Think carefully through the problem before responding.` Pair `low` with an explicit checklist if the task has multiple sections.

At `xhigh`/`max`, large `max_tokens` (start 64k). Tokenizer is ~1.0–1.35× 4.6 for the same text; old `max_tokens` values can truncate. Thinks more at higher effort on later agentic turns.

## Literalism

Does not silently generalize an instruction from item 1 to item N. Does not infer unstated requests. State scope: `Apply this formatting to every section, not just the first.`

Code-review prompts that say "only high-severity" / "be conservative" will withhold findings. Ask for coverage plus confidence, filter later.

## Tools vs reasoning

Uses tools **less** than 4.6 and reasons more. Better on most work. To get more tool use, raise effort to `high`/`xhigh`, or say when and why to call the tool.

## Subagents

Spawns **fewer** subagents than 4.6 by default. Steer if you need fan-out:

```text
Do not spawn a subagent for work you can complete directly in a single response.
Spawn multiple subagents in the same turn when fanning out across items or reading multiple files.
```

## Verbosity and tone

Calibrates length to task complexity: short on lookups, long on open-ended analysis. For a fixed short style: `Provide concise, focused responses. Skip non-essential context, and keep examples minimal.` Positive examples of brevity beat "don't over-explain."

More direct and opinionated than 4.6, less validation-forward phrasing, fewer emoji. Add warmth in the prompt if the product needs it.

Do not add "keep text between tool calls to ≤N words" as a hard cap. Anthropic found that kind of verbosity clamp hurt coding quality on 4.7.

Drop "after every 3 tool calls, summarize" scaffolding. Describe the update cadence only if the default is wrong.

## Thinking and API

Adaptive thinking only. `thinking: {type: "enabled", budget_tokens: N}` returns 400. Thinking is **off** when you omit the `thinking` field (same as 4.6, unlike Opus 5). Sampling params (`temperature`, `top_p`, `top_k`) return 400. Prefills return 400.

Thinking blocks stream with an empty `thinking` field unless `thinking.display` is `"summarized"`. Default `"omitted"` looks like a pause; restore summarized display if the product shows reasoning progress.

## Vision

First Opus with high-res images (2576px long edge). Coordinates are 1:1 with pixels. Full-res images can use ~3× prior image tokens. Downsample if you do not need the fidelity.

If the user is locked to 4.7, stay on `claude-opus-4-7`. Do not silently upgrade to 4.8 or 5.
