---
name: linksee-memory
description: |
  The bridge to the agent's "past self". Before any new task, file edit, decision, or right after a failure, recall past caveats (pain records) / learnings (growth log) / implementation history from linksee-memory.
  This is the only way to solve Claude Code's "memory amnesia every session" problem. The "never repeat the same mistake" mechanism that Mem0 / Letta / Zep can't provide.
  ALWAYS use this skill at the following moments:
  1. Task start / new task begins
  2. Before editing a file
  3. The moment an error/failure happens
  4. The moment something succeeds or is learned
  5. When the user says "before", "earlier", "last time", "remember?", "remember this"
  6. When asked "why did we do that", "when was this decided"
  7. Returning from another project / switching sessions
  Triggers (EN): remember/recall/forget/memory/before/earlier/last time/previously/remember when/same as before/history/use linksee/linksee
  Triggers (JP): 記憶/覚えて/忘れて/過去/前回/前に/そういえば/覚えてる/リンクシー
  Error keywords: failed/broken/stuck/error/bug/doesn't work/not working/same error again
---

# Linksee Memory — 3 Tools, Cross-Agent Brain

## Core Principle

**This skill is the only way to persist agent growth across sessions.**
Claude Code forgets everything when a session ends. linksee-memory is the "memory that doesn't disappear" device.

Writes are handled automatically by the Stop hook. **Reads require the agent to actively pull** — this skill instills that habit.

---

## The 3 Tools

### `remember` — Save / Update / Delete

```
mcp__linksee__remember({
  entity_name: "project-name",
  entity_kind: "project",
  layer: "caveat",      // goal/context/emotion/implementation/caveat/learning
  content: '{"title":"...","what":"...","why":"..."}',
  importance: 0.8        // >=0.9 to pin
})
```

**Modes:**
- **Create** (default): entity_name + entity_kind + layer + content
- **Update**: provide `memory_id` + fields to change
- **Delete**: set `forget: true` + `memory_id`

**When to call:**
- Error/failure occurs -> layer: "caveat" (auto-protected, never forgotten)
- Decision made -> layer: "learning"
- Goal set/updated -> layer: "goal"
- User says "remember this" / "覚えておいて"

### `recall` — Search / File History / Overview

```
mcp__linksee__recall({ query: "KanseiLink OAuth", max_tokens: 2000 })
```

**Three modes (auto-detected):**
- **Search**: provide `query` -> memories ranked by relevance + heat
- **File history**: provide `path` -> complete edit history with user-intent
- **Overview**: omit all params -> entity list sorted by momentum

**When to call:**
- BEFORE starting any task (check past caveats/decisions)
- User mentions "before" / "前に" / "last time"
- Error occurs (check if seen before)
- Making a decision (check for prior decisions)

### `read_smart` — Token-Saving File Reader

```
mcp__linksee__read_smart({ path: "/absolute/path/to/file.ts" })
```

Use INSTEAD of Read for any file you may have read before.
- First read: full content
- Re-read unchanged: ~50 tokens (99% savings)
- Re-read modified: only changed chunks (50-90% savings)

---

## 6 Layers

| Layer | When | Protected |
|---|---|---|
| `goal` | User states a clear goal | No |
| `context` | Background on when/why | No |
| `emotion` | User's temperature/tone | No |
| `implementation` | Code written, configured | No |
| `caveat` | Lessons never to repeat | **Yes** |
| `learning` | New insight, belief updated | No |

Layer aliases work: `warnings`->`caveat`, `decisions`->`learning`, `how`->`implementation`, `why`->`goal`

---

## Structured Content (v2)

Every `content` field should be JSON with these axes:

```json
{
  "title": "one-line summary (future agents skim this)",
  "altitude": "implementation",
  "type": "outcome",
  "state": "done",
  "what": "the actual content (5W1H extracted, NOT raw chat)",
  "why": "why this matters",
  "affects": ["src/file.ts"],
  "next_action": null
}
```

**altitude**: mission / strategy / architecture / implementation
**type**: question / comparison / decision / work / outcome / learning / note
**state**: open / decided / in_progress / done / stalled / parked / superseded

---

## Key Scenarios

### Task Start
```
recall({ query: "<project + topic keywords>", max_tokens: 2000 })
```
Check returned caveats/learnings before starting work.

### File Edit
```
recall({ path: "server.ts" })
```
Check edit history and past intent before touching a file.

### Error Occurs
```
recall({ query: "<error keywords>", layer: "caveat" })
```
Then immediately record the new failure:
```
remember({ entity_name: "...", layer: "caveat", content: '{"title":"...","what":"..."}', importance: 0.8 })
```

### Decision Made
```
remember({ entity_name: "...", layer: "learning", content: '{"title":"switched to Sonnet","type":"decision","what":"...","why":"..."}' })
```

---

## Hard Rules

**DO:**
1. Always `recall` before starting a new task
2. Before touching a file, check its history via `recall({ path: "..." })`
3. Use `read_smart` instead of `Read` for large files
4. Record `caveat` immediately when errors occur
5. Record `learning` when something new is understood

**DON'T:**
1. Start work without recalling first
2. Solve an error without recording it
3. Use `Read` everywhere instead of `read_smart`
4. Write caveats in a flippant tone
5. Save raw chat as memory content — extract the semantic insight

---

*linksee-memory MCP v0.7.0 — 3-tool unified surface*
*Auto-write via Stop hook, explicit read via recall.*
*MIT License — Synapse Arrows PTE. LTD.*
