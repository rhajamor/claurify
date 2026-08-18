# Model and effort routing

Authoritative: [Effort](https://platform.claude.com/docs/en/build-with-claude/effort). API default is `high` (same as omitting the parameter).

Effort is a behavioral signal, not a token cap. It affects text, tool calls, and thinking. Lower effort means fewer tools and less thinking on easy work; hard problems still get some thinking.

IDs for Claude Code: `claude-fable-5`, `claude-opus-5`, `claude-sonnet-5`, `claude-opus-4-8`, `claude-opus-4-7`, `claude-opus-4-6`.

## Pick a model (only when the user did not)

Cheapest model that can do the job. Spend Fable only when a weaker model would fail or retry more.

| Task class | Model | Effort | Why |
|---|---|---|---|
| Extract, classify, format, lookup | `claude-sonnet-5` | `low` | Literal, fast, scoped |
| Single-file edit, short analysis, email, chat | `claude-sonnet-5` | `medium` | Quality holds; low can underthink |
| Multi-file feature, PRD, research synthesis | `claude-sonnet-5` | `high` | Default; matches Sonnet 4.6 at max |
| Same, but judgment / trade-off heavy | `claude-opus-5` | `medium` | Opus judgment without xhigh spend |
| Hard coding, bug-finding, long-horizon agent | `claude-opus-5` | `high` or `xhigh` | Start high; xhigh for multi-hour / many files |
| Days-long, highly ambiguous, first-shot systems | `claude-fable-5` | `high` (`xhigh` if frontier) | Lower Fable effort often beats prior xhigh |
| User locked to 4.8, coding/agentic | `claude-opus-4-8` | `xhigh` | Official starting point for that work |
| User locked to 4.8, other intel work | `claude-opus-4-8` | `high` | |
| User locked to 4.7, coding/agentic | `claude-opus-4-7` | `xhigh` | Same as 4.8; 4.7 introduced `xhigh` |
| User locked to 4.7, other intel work | `claude-opus-4-7` | `high` | |
| User locked to 4.6 | `claude-opus-4-6` | `high` | No `xhigh` on 4.6. `max` only if evals show headroom |

When the user named a model, skip this table's model column. Recommend effort from that row's task class, using the named model's overlay if it disagrees (e.g. Opus 4.8 coding → `xhigh` even if the table would have picked Opus 5 `high`).

## Effort levels

| Level | Use |
|---|---|
| `low` | Short, scoped, latency-sensitive. Subagents. Mechanical work. |
| `medium` | Cost-sensitive but still needs a real answer. |
| `high` | Default. Complex reasoning, coding, agents. |
| `xhigh` | Hard coding and long-horizon agents. Not on Opus 4.6 (use `max`). |
| `max` | Unconstrained. Diminishing returns; can overthink. |

Opus 5: start `high`; use `low`/`medium` freely where quality holds. Effort does **not** shorten visible replies; add a conciseness line.

Sonnet 5: `medium` ≈ Sonnet 4.6 `high`. `low`/`medium` stay in scope. Raise effort rather than prompting around shallow reasoning.

Fable 5: `high` default. Lower if the task finishes but took too long. `xhigh` for capability-sensitive work.

Opus 4.8 / 4.7: `xhigh` for coding/agentic; `high` minimum for other intel-sensitive work. 4.7 respects low/medium more strictly than 4.6.

Opus 4.6: `high` default. No `xhigh`. `medium` to cut exploration; `max` for the hardest 4.6-era work.

Claude Code: `--model ID --effort LEVEL`. API: `output_config: { "effort": "medium" }`. Hold effort constant inside a cached conversation; changing it breaks the prefix cache.

## Thinking

- Fable 5: thinking always on, adaptive only.
- Opus 5 / Sonnet 5: thinking on by default. Opus 5 cannot disable thinking at `xhigh`/`max`.
- Opus 4.8 / 4.7 / 4.6: thinking off unless `thinking: {type: "adaptive"}`. 4.7+ rejects `budget_tokens`. 4.6 still accepts it (deprecated).
- Prefer thinking-on at lower effort over thinking-off. Opus 5 with thinking disabled can leak tool-call text and internal XML tags.

## Suggest Fable only when

The work is days-long, deeply ambiguous, or a first-shot system that weaker models have failed. State that in **Why**. Do not pick Fable for a single-file fix or a JSON extract.
