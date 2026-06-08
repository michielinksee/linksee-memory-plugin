---
name: linksee-memory
description: |
  The bridge to the agent's "past self" AND the integrity checker for product decisions.
  7 MCP tools: remember/recall/read_smart (memory) + drift_status/check_decision/declare_anchor/resolve_drift (drift detection).
  This is the only way to solve Claude Code's "memory amnesia every session" problem AND catch when reality silently diverges from decisions.

  ALWAYS use this skill at the following moments:
  1. Task start / new task begins — recall past caveats, decisions, learnings
  2. Before editing a file — check file edit history via recall
  3. The moment an error/failure happens — remember as caveat
  4. The moment something succeeds or is learned — remember as learning
  5. When the user says "before", "earlier", "last time", "remember?", "remember this"
  6. When asked "why did we do that", "when was this decided"
  7. Returning from another project / switching sessions
  8. When asked about product health / what's drifting / what needs attention — drift_status
  9. When a decision is made — declare_anchor
  10. When drift is identified or resolved — resolve_drift

  Triggers (EN): remember/recall/forget/memory/before/earlier/last time/previously/remember when/same as before/history/use linksee/linksee/drift/drifting/what's broken/decision/anchor/decided
  Triggers (JP): 記憶/覚えて/忘れて/過去/前回/前に/そういえば/覚えてる/リンクシー/ドリフト/方針/決定/アンカー
  Error keywords: failed/broken/stuck/error/bug/doesn't work/not working/same error again
  Decision keywords: decided/let's go with/approved/settled on/pivot/strategy/switch to/anchor this
---

# Linksee Memory — 7 Tools, Cross-Agent Brain + Drift Detection

## Core Principle

**This skill bridges two gaps no other tool fills:**

1. **Memory gap**: Claude Code forgets everything when a session ends. linksee-memory is the "memory that doesn't disappear" device.
2. **Drift gap**: Decisions get made but nothing checks if reality still matches. The drift detector catches silent divergence.

Writes are handled automatically by the Stop hook. **Reads and drift checks require the agent to actively pull** — this skill instills that habit.

---

## The 7 Tools

### Memory tools (cross-agent brain)

| Tool | When to use |
|---|---|
| `remember` | Save / update / delete memories. Caveat layer is auto-protected from forgetting. Importance >= 0.9 pins forever. |
| `recall` | 3 modes: search (query), file history (path), entity overview (no params). CALL BEFORE every new task. |
| `read_smart` | Token-saving file reads. Use INSTEAD of Read for all files. 50-99% savings on re-reads. |

### Drift tools (decision integrity)

| Tool | When to use |
|---|---|
| `drift_status` | "What needs my attention?" — truth map with drift/review/held/aligned states. Call at session start. |
| `check_decision` | Deep-dive into one anchor — premises, edges, candidates. Call before resolving. |
| `declare_anchor` | User makes a decision/constraint/prohibition → declare it. declare-don't-mine: only explicit human declaration. |
| `resolve_drift` | Close the loop: fix / supersede / acknowledge / dismiss. |

---

## The 6-layer memory structure

| Layer | When to use | Example |
|---|---|---|
| `goal` | User states a clear goal | "want to integrate with freee" |
| `context` | Background on when/why | "meeting with company X on Wednesday" |
| `emotion` | User's tone signal | "tired", "excited", "stressed" |
| `implementation` | Code written, what worked/didn't | "OAuth flow works" / "stopped with auth_expired" |
| `caveat` | **Lessons to never repeat** (auto-protected) | "freee OAuth expires in 24h" |
| `learning` | Insight gained, belief updated | "AST chunking beats line diff" |

---

## 4-species drift model

| Species | Display format | Example |
|---|---|---|
| `hypothesis` | Decision Cards — journal format | "We chose Stripe over Square for SG tax handling" |
| `constraint` | Rules — pass/fail checklist | "Never expose API keys in client code" |
| `commitment` | Heartbeats — cadence monitoring | "We ship weekly" |
| `source_of_truth` | Reference — stable anchors | "Single source of truth for pricing is Notion" |

---

## Execution flow — canonical moments

### 1. Task Start — recall + drift_status

```
recall({ query: "<task keywords>", max_tokens: 2000 })
drift_status()  // optional: check what's drifting
```

### 2. Before editing a file

```
recall({ path: "server.ts" })  // file edit history with user-intent
```

### 3. Reading files — always use read_smart

```
read_smart({ path: "/absolute/path/to/file.ts" })
```

### 4. Error occurs — record caveat immediately

```
remember({
  entity_name: "<project>", entity_kind: "project",
  layer: "caveat",
  content: "<what failed + workaround + root cause>",
  importance: 0.8
})
```

### 5. Decision made — declare anchor

```
declare_anchor({
  kind: "decision",
  statement: "We use Stripe for SG payments",
  rationale: "Better tax handling than Square",
  domain: "monetization",
  decision_mode: "hypothesis",
  confidence: 0.85
})
```

### 6. Drift found — resolve it

```
resolve_drift({
  anchor_id: 42,
  action: "fix",
  rationale: "Updated payment integration to match intent"
})
```

---

## Hard rules

### Do
1. At any new task start, always call `recall` first
2. Before touching a file, verify history via `recall({ path: ... })`
3. Prefer `read_smart` over `Read` for all files
4. When an error occurs, record a `caveat` immediately
5. When a decision is made, consider `declare_anchor`
6. Check `drift_status` at session start for any attention-needed items
7. Use keywords + layer filter in recall queries, not natural language

### Don't
1. Start a task without recalling first
2. Solve an error without recording — future agents will hit the same failure
3. Use `Read` everywhere instead of `read_smart`
4. Pattern-extract anchors from code (declare-don't-mine)
5. Recall the same entity twice in one session without new context

---

## Privacy

- **Local-first**: SQLite DB at `~/.linksee-memory/memory.db`
- **No external transmission**: telemetry is opt-in, OFF by default
- **Backup**: simple file copy

---

*This skill runs on top of linksee-memory MCP v0.8.0+.*
*Auto-write via Stop hook, explicit read via recall, drift via 4 dedicated tools.*
*Listed in MCP Official Registry, PulseMCP, mcpservers.org, Glama.*
*MIT License — Synapse Arrows PTE. LTD.*
