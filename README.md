# linksee-memory (Claude Code Plugin)

> Cross-agent memory + drift detection — 7 tools, 6-layer WHY structure, 4-species decision tracking. **Local-first, private, single SQLite file.**

This is the Claude Code Plugin bundle. It ships:

- **MCP server** (`linksee-memory`) — 7 tools across two domains: memory (3) + drift (4)
- **Skill** (`linksee-memory`) — auto-invokes memory and drift operations based on user intent

## The problem this solves

**Memory:** Every Claude Code session starts from zero. If you fixed a bug yesterday, today's agent doesn't remember *why* or *how*. The agent walks into the same wall again and again. Mem0 / Letta / Zep try to fix this with flat fact lists — but the agent sees "edited file X 30 times" and has no idea why. **linksee-memory keeps the WHY.**

**Drift:** You decided "we ship weekly" last month. You chose "Stripe over Square for SG tax handling" three weeks ago. But nothing checks whether reality still matches those decisions. **Drift detection catches when your product silently diverges from what you decided.** Memory is the entry point. Drift detection is the real value.

## Install

```bash
claude plugin add -- linksee-memory
```

Or add `Use Linksee Memory` to your system prompt for any MCP-compatible agent.

### Local testing

```bash
git clone https://github.com/michielinksee/linksee-memory-plugin.git
claude --plugin-dir ./linksee-memory-plugin
```

## 7 tools (v0.8.0)

### Memory tools (cross-agent brain)

| Tool | What it does |
|---|---|
| `remember` | Save / update / delete memories. Auto-classifies into 6 layers. Importance >= 0.9 pins the memory forever. |
| `recall` | Search memories by keyword/FTS5, get file edit history with user-intent context, or list entities by momentum. |
| `read_smart` | Token-saving file reader with AST-aware diff caching (50-99% savings on re-reads). |

### Drift tools (decision integrity)

| Tool | What it does |
|---|---|
| `drift_status` | Check what's drifting right now — truth map with 4 states (drift/review/held/aligned) and 4 species (hypothesis/constraint/commitment/source_of_truth). |
| `check_decision` | Deep-dive into a specific anchor — its state, premises, drift edges, and pending candidates. |
| `declare_anchor` | Declare a decision, constraint, or prohibition as a truth-map anchor. declare-don't-mine: only from explicit human declaration. |
| `resolve_drift` | Record a resolution: fix / supersede / acknowledge / dismiss. The human feedback loop that closes the drift cycle. |

Previous versions exposed 3 tools — v0.8.0 adds 4 drift tools for decision integrity monitoring.

## Auto-triggers

After install, Claude Code auto-triggers operations on phrases like:

**Memory triggers:**
- "how did I solve this before?" / "remember this" / "same error again"
- "before" / "earlier" / "last time" / "前に" / "覚えておいて" / "また同じエラー"

**Drift triggers:**
- "what's drifting?" / "what needs attention?" / "product health"
- "anchor this" / "record this decision" / "we decided..."
- "that's fixed" / "ignore that" / "changed direction"

## Three pillars

1. **Token savings** via `read_smart` — sha256 + AST/heading/indent chunking. Re-reads return only diffs. **86% saved on a typical TS file edit, 99% on unchanged re-reads.**
2. **Cross-agent portability** — one SQLite file at `~/.linksee-memory/memory.db`, not a cloud service. Same brain for Claude Code, Cursor, GPT, Codex, Gemini.
3. **WHY-first structured memory** — 6 layers: goal / context / emotion / implementation / caveat / learning. The `caveat` layer (pain lessons) is auto-protected from forgetting.

**Plus: Drift detection** — declare decisions as anchors, let the detector check committed reality against intent. Four resolution actions (fix/supersede/acknowledge/dismiss) close the loop.

## 6-layer memory structure

```
goal           <- what the user is working toward
context        <- why this, why now
emotion        <- user tone signals (frustration, excitement)
implementation <- how it was done (+ what failed)
caveat         <- "never do this again" (auto-protected)
learning       <- patterns distilled from experience
```

## 4-species drift model

```
hypothesis      <- Decision Cards — decision journal format
constraint      <- Rules — pass/fail checklist
commitment      <- Heartbeats — cadence monitoring
source_of_truth <- Reference — stable anchors
```

## Comparison

| | Mem0 / Letta / Zep | Claude built-in memory | **linksee-memory** |
|---|---|---|---|
| Cross-agent | cloud-dependent | Claude only | single local file |
| 6-layer WHY structure | flat | flat md | goal/context/emotion/impl/caveat/learning |
| File diff cache (AST) | no | no | 50-99% token savings |
| Per-edit user-intent | no | no | unique |
| Drift detection | no | no | 4-species + truth map |
| Decision tracking | no | no | declare/check/resolve cycle |
| Ebbinghaus forgetting | partial | no | caveat protected |
| Local-first / private | no | yes | yes |

## Inside this plugin

```
linksee-memory-plugin/
  .claude-plugin/
    plugin.json                 # plugin manifest (v0.8.0)
  .mcp.json                    # launches linksee-memory via npx
  skills/
    cross-session-memory/
      SKILL.md                 # auto-invocation rules
  README.md
  LICENSE
```

## Privacy

- **Local-first**: SQLite DB at `~/.linksee-memory/memory.db`. Nothing leaves your machine.
- **Opt-in telemetry only**: anonymous usage counts can be enabled with `LINKSEE_TELEMETRY=basic`. No content is ever sent.
- Full privacy contract: [linksee-memory#telemetry](https://github.com/michielinksee/linksee-memory#telemetry-opt-in-off-by-default)

## As featured on

- **Zenn**: 73+ likes, 165+ Hatena Bookmark users (May 2026)
- **MCP Official Registry**, PulseMCP, mcpservers.org, Glama
- **npm**: 23 published versions, active since April 2026

## Related

- **Core MCP server**: [npm: linksee-memory](https://www.npmjs.com/package/linksee-memory) (v0.8.0)
- **Dashboard**: [linksee-dashboard](https://github.com/michielinksee/linksee-dashboard) — 8-view visual interface for drift anchors
- **Landing page**: [linksee-site.vercel.app](https://linksee-site.vercel.app)
- **Pair with**: [kansei-link plugin](https://github.com/michielinksee/kansei-link-plugin) — external SaaS discovery to pair with your personal memory

## License

MIT — Synapse Arrows PTE. LTD.
