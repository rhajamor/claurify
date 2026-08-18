# claurify

Turn a messy Claude ask into a spec Claude will follow, then pick the cheapest model and effort that will still do the job. If you already named a model, it only sets effort. If the ask is too vague to spend tokens well, it asks 2–3 questions instead of guessing.

Overlays follow Anthropic's published prompting docs for Fable 5, Opus 5, Sonnet 5, Opus 4.8, 4.7, and 4.6.

## Install

```bash
npx skills add rhajamor/claurify
```

Global (every project on this machine):

```bash
npx skills add rhajamor/claurify -g
```

That is also how the skill shows up on [skills.sh](https://skills.sh): there is no submit form. Installs are the listing.

## Use

Ask Claude Code or Cursor to claurify a prompt, pick a Claude model, or set effort. You get:

- the rewritten prompt (XML when the content is mixed)
- model (omitted if you already named one)
- effort
- a one-line why
- a `claude --model … --effort …` run line

## Layout

```
SKILL.md              workflow, routing table, output template
examples.md           four worked cases, including "ask, do not rewrite"
references/universal.md
references/model-effort.md
references/fable-5.md
references/opus-5.md
references/sonnet-5.md
references/opus-4-8.md
references/opus-4-7.md
references/opus-4-6.md
```

## License

MIT
