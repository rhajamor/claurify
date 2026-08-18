# Claude Sonnet 5 overlay

Source: [Prompting Claude Sonnet 5](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5).

Default working model. Calibrates reply length to task complexity (short on lookups, longer on analysis). Literal instruction following, especially at `low`/`medium`.

## Literalism

Does not silently generalize an instruction from item 1 to item N. If a rule applies everywhere, say so: `Apply this formatting to every section, not just the first.`

Does not infer unstated requests. If you want implementation, say "change" / "implement", not "could you look at."

## Effort

Default `high`. `medium` ≈ Sonnet 4.6 at `high`. `low`/`medium` stay in scope (good for cost; risk of underthinking on moderately hard work). If reasoning looks shallow, **raise effort** rather than piling on "think harder" prose. If you must stay at `low`:

```text
This task involves multistep reasoning. Think carefully through the problem before responding.
```

`xhigh` for the hardest coding/agentic work. `max` unconstrained.

Adaptive thinking is on by default. `thinking: {type: "enabled", budget_tokens: N}` returns 400. Sampling params (`temperature`, `top_p`, `top_k`) return 400; steer tone in the prompt.

Leave headroom in `max_tokens` at high/xhigh/max. Sonnet 5's tokenizer is ~30% higher than 4.6 for the same text; old `max_tokens` values can truncate.

To reduce extra thinking on fat system prompts:

```text
Thinking adds latency and should only be used when it will meaningfully improve answer quality, typically for problems that require multistep reasoning. When in doubt, respond directly.
```

## Tools

More agentic than 4.6. Reaches for tools and self-check loops more readily. Thinking off → fewer tool calls; if you need tools with thinking off, say so in the system prompt. `high`/`xhigh` use substantially more tools.

## Progress

Interim "after every 3 tool calls, summarize" scaffolding can usually be removed. If cadence is wrong, describe the updates you want with a positive example.

## Code review

"Only report high-severity" / "be conservative" / "don't nitpick" is followed faithfully: it still finds the bugs, then withholds them. For coverage:

```text
Report every issue you find, including ones you are uncertain about or consider low-severity. Do not filter for importance or confidence at this stage. For each finding, include confidence and estimated severity.
```

If you want a single-pass filter, name the bar: "report bugs that cause incorrect behavior, a test failure, or a misleading result; omit pure style/naming."

## Frontend

May lock onto one house style. Generic "don't use purple / make it clean" swaps palettes, it does not create variety.

- Give a concrete tonal system (hex, type, radius, layout), or
- `Before building, propose 4 distinct visual directions (bg / accent / typeface + one-line rationale). Ask which to implement, then build only that.`

AI-slop steer (short):

```text
<frontend_aesthetics>
NEVER use generic AI-generated aesthetics like overused font families (Inter, Roboto, Arial, system fonts), clichéd color schemes (particularly purple gradients), predictable layouts, and cookie-cutter design. Use unique fonts, cohesive colors, and animations for micro-interactions.
</frontend_aesthetics>
```

## Verbosity

If the product needs a fixed length:

```text
Provide concise, focused responses. Skip non-essential context, and keep examples minimal.
```

Positive examples of the desired brevity beat "don't over-explain."
