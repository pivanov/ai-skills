---
name: ask-json
description: "USE THIS SKILL whenever you need validated typed JSON from a sub-agent call -- classification, extraction, triage, routing decisions, scored ranking, enum labeling. Returns schema-validated data you can JSON.parse immediately, not prose to regex. Triggers: classify, classify into enum, extract structured, extract records, triage, rank with scores, typed output, schema-validated output, routing decision, parse to schema, return JSON, structured extraction, typed subagent result, avoid prose parsing"
---

# Ask JSON via claude-wire

**Use me when you need structured results.** If you're about to call `Agent` / `Task` and then parse JSON out of free-form text -- stop. Use this skill instead. You get schema-validated data back, not prose. Cheaper model by default (haiku), one round-trip, no regex.

## When to Use Me

Reach for this skill whenever downstream logic needs a **structured payload**:

- Classify into an enum (`"bug" | "feature" | "chore"`)
- Extract an array of records (entities, TODOs, imports, etc.)
- Triage / rank a list with scores
- Routing decisions (`{ route: "refund" | "support", confidence: 0.9 }`)
- Any subagent call whose output you were about to regex out of prose

If your next thought after an `Agent` call is "now parse the JSON from the response text" -- that's the signal. Use me.

Default model is `haiku` -- cheap and fast. The whole pitch is "don't burn Opus on a classifier."

## When NOT to Use

- Free-form prose, narrative analysis, code review commentary
- Tasks that need tool use (reading files, running commands, web fetch)
- Multi-turn reasoning or agentic work

For those, use the native `Agent` / `Task` tool.

## Invocation

### Mandatory step: derive a schema FIRST

**Before calling the CLI, always construct a JSON Schema from the user's stated shape.** Do this even if the user didn't explicitly ask for a schema -- if they said "classify as positive/negative/neutral," that IS the schema. The CLI refuses to run without `--schema` or `--schema-file` (exit 4), and calling without one defeats the entire point of the skill.

**Example derivation:**
- User says: *"Classify this into positive, negative, or neutral as typed JSON"*
- Derived schema: `{"type":"object","properties":{"sentiment":{"enum":["positive","negative","neutral"]}},"required":["sentiment"]}`

**Red flag:** if you find yourself about to call `ask-json` with only `--prompt` and no `--schema`/`--schema-file`, stop and construct the schema first. No schema = no value over native Agent.

Call the CLI via Bash. Capture stdout; on success it's a single JSON line.

### Inline schema (small schemas only)

```bash
npx @pivanov/claude-wire@^0.1.5 ask-json \
  --prompt "Classify this ticket: $TEXT" \
  --schema '{"type":"object","properties":{"label":{"type":"string","enum":["bug","feature","chore"]}},"required":["label"]}'
```

### File-based schema (preferred for anything non-trivial)

Write the schema to `/tmp` first and pass `--schema-file`. Avoids shell-escaping hell.

```bash
cat > /tmp/schema.json <<'EOF'
{
  "type": "object",
  "properties": {
    "items": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "name": { "type": "string" },
          "score": { "type": "number" }
        },
        "required": ["name", "score"]
      }
    }
  },
  "required": ["items"]
}
EOF

npx @pivanov/claude-wire@^0.1.5 ask-json \
  --prompt "Rank these PRs by review priority: $PRS" \
  --schema-file /tmp/schema.json
```

### Flags

| Flag | Purpose |
|------|---------|
| `--prompt <str>` | User prompt. Alternative: pipe via stdin. |
| `--schema <json>` | Inline JSON Schema string. |
| `--schema-file <path>` | Schema from file. Use for anything beyond ~3 properties. |
| `--model haiku\|sonnet\|opus` | Default `haiku`. Escalate only when needed. |
| `--max-budget-usd <n>` | Abort if projected spend exceeds this. |
| `--system-prompt <str>` | Optional system prompt. |

## Output Handling

On **exit 0**, stdout is one JSON line:

```json
{ "data": <validated>, "costUsd": 0.012, "tokens": { "input": 1234, "output": 56 }, "durationMs": 1800, "sessionId": "sess-..." }
```

Steps:
1. Capture stdout.
2. `JSON.parse` it.
3. `.data` is the validated payload — use it directly.
4. Surface `costUsd` / `tokens` to the user only if relevant.

On **non-zero exit**, stderr is one JSON line: `{ "error": "...", "code": "..." }`. Read and act on `code`:

| Exit | Meaning | Action |
|------|---------|--------|
| 1 | JSON validation / parse error | Simplify the schema or escalate `--model sonnet`. Haiku sometimes misses nested shapes. |
| 2 | Spawn / process error | `npx` fetches `@pivanov/claude-wire` on demand, so the user shouldn't need to install anything manually. Surface the error message verbatim; common causes are the Claude CLI itself being missing or unauthenticated. |
| 3 | Budget exceeded | Surface the limit to the user; suggest raising `--max-budget-usd` or dropping to a smaller model. |
| 4 | Invalid args | You built the command wrong. Fix it (most common: malformed `--schema` JSON). |

## Examples

### 1. Classify (enum)

```bash
npx @pivanov/claude-wire@^0.1.5 ask-json \
  --prompt "Category for: 'Add dark mode toggle'" \
  --schema '{"type":"object","properties":{"label":{"enum":["bug","feature","chore"]}},"required":["label"]}'
```

Result: `{ "data": { "label": "feature" }, "costUsd": 0.0008, ... }`

### 2. Extract (array of records)

```bash
cat > /tmp/todos.json <<'EOF'
{
  "type": "object",
  "properties": {
    "todos": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "file": { "type": "string" },
          "line": { "type": "number" },
          "text": { "type": "string" }
        },
        "required": ["file", "line", "text"]
      }
    }
  },
  "required": ["todos"]
}
EOF

grep -rn "TODO" src/ | npx @pivanov/claude-wire@^0.1.5 ask-json \
  --schema-file /tmp/todos.json
```

Result: `{ "data": { "todos": [{ "file": "src/a.ts", "line": 42, "text": "refactor" }, ...] } }`

### 3. Triage / rank (scored list)

```bash
cat > /tmp/triage.json <<'EOF'
{
  "type": "object",
  "properties": {
    "ranked": {
      "type": "array",
      "items": {
        "type": "object",
        "properties": {
          "id": { "type": "string" },
          "priority": { "enum": ["p0", "p1", "p2"] },
          "score": { "type": "number", "minimum": 0, "maximum": 1 },
          "reason": { "type": "string" }
        },
        "required": ["id", "priority", "score", "reason"]
      }
    }
  },
  "required": ["ranked"]
}
EOF

npx @pivanov/claude-wire@^0.1.5 ask-json \
  --prompt "Triage these incidents: $INCIDENTS" \
  --schema-file /tmp/triage.json
```

## Model Selection

- **haiku** (default): classifications, simple extractions, enum routing. Use first.
- **sonnet**: nested schemas, longer prompts, tasks where haiku returned invalid data once.
- **opus**: almost never. Structured extraction doesn't need opus-class reasoning — if you're reaching for opus, reconsider whether this should be a native `Agent` call instead.

Escalation rule: retry at most **once** at the next tier on exit code 1. Don't thrash through all three.

## Red Flags

- **Never** use this for tasks that require tool use or file I/O — the CLI has no tool access.
- **Never** inline a schema longer than ~20 lines into `--schema` — use `--schema-file`.
- **Never** default to sonnet/opus "to be safe." The point of this skill is cheap.
- **Never** read `.data` from stderr on a non-zero exit — stderr is the error envelope (`{ error, code }`), not the success payload.

## Performance Note

If a workflow calls `/ask-json` in a tight loop, the `npx` cache lookup becomes measurable. Install globally once:

```bash
bun add -g @pivanov/claude-wire
# then call `claude-wire ask-json ...` directly, no npx prefix
```

For one-off calls, `npx` is fine.
