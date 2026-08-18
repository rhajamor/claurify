# Examples

## 1. Vague coding, no model given

**User:** `fix the bugs in scripts/stats.py`

**Ask first** (success is missing): What should pass when it's done? Any tests? Anything we must not touch?

After `pytest tests/test_stats.py` must pass, and no refactor:

## Prompt
<task>Fix the bugs in scripts/stats.py so the existing tests pass.</task>
<success>pytest tests/test_stats.py exits 0. Do not add tests. Do not refactor or rename anything that is not required for the fix.</success>
<context>Read scripts/stats.py and tests/test_stats.py before editing. Never speculate about code you have not opened.</context>
<constraints>Stay inside those two files. No new helpers, no extra error handling for cases the tests do not cover.</constraints>

## Model
claude-sonnet-5

## Effort
medium

## Why
Single-file bugfix with a test as the success bar. Sonnet 5 at medium is enough; Fable or Opus 5 would only cost more. Removed "fix the bugs" ambiguity by naming the test file and forbidding a refactor.

## Run
claude --model claude-sonnet-5 --effort medium

---

## 2. User already named a model

**User:** `Use opus 5. Turn these meeting notes into JSON.`

Do not suggest a different model. Effort only.

## Prompt
<task>Extract structured records from the notes in notes/standup.md into JSON.</task>
<success>A single JSON array. Each object has name (string), owner (string), due (YYYY-MM-DD or null), status (one of planned, blocked, done). No extra keys. No commentary outside the JSON.</success>
<context>Read notes/standup.md first. Quote the source span for any field you are unsure about, then emit the JSON.</context>
<output>JSON array only.</output>

## Effort
low

## Why
Mechanical extract into a fixed schema. Opus 5 was requested, so it stays. Low effort: the schema is closed and there is nothing to design. Added the schema, the file pointer, and "JSON only" so it does not wrap the result in prose.

## Run
claude --model claude-opus-5 --effort low

---

## 3. Too vague: ask, do not rewrite yet

**User:** `make the dashboard better`

**Ask (max 3):**
1. Which file or URL is the dashboard?
2. What does "better" mean here: a specific bug, layout, performance, or new widgets?
3. What must not change (data source, auth, visual language)?

Wait. Do not invent a product spec.

---

## 4. Over-scope trap (structured vs original)

**Original:** `review and improve utils/names.py`

**Structured** (user said they only want the public API renamed to snake_case):

<task>Rename the public functions in utils/names.py to snake_case and update callers in this directory.</task>
<success>Public names are snake_case. Existing behavior is unchanged. python -c "import utils.names" still works.</success>
<constraints>Do not add features, types, comments, or extra files. Do not "clean up" private helpers unless a rename requires it.</constraints>

Model: `claude-sonnet-5`. Effort: `medium`. The original would invite a rewrite; the structured prompt names the one change.
