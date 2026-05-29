# linksee-memory (Claude Code Plugin)

> Cross-agent precision memory — 3 tools, 6-layer WHY structure, AST diff cache. **Local-first, private, single SQLite file.**

This is the Claude Code Plugin bundle. It ships:

- **MCP server** (`linksee-memory`) — 3 tools: `remember` / `recall` / `read_smart`
- **Skill** (`cross-session-memory`) — auto-invokes memory operations based on user intent

## The problem this solves

Every Claude Code session starts from zero. If you fixed a bug yesterday, today's agent doesn't remember *why* or *how*. If you made an architectural decision last week, today's agent doesn't know. The agent walks into the same wall again and again.

Mem0 / Letta / Zep try to fix this with flat fact lists — but the agent sees "edited file X 30 times" and has no idea why. **linksee-memory keeps the WHY.**

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

## 3 tools (v0.7.0)

| Tool | What it does |
|---|---|
| `remember` | Save / update / delete memories. Auto-classifies into 6 layers. |
| `recall` | Search memories, get file edit history, or list entities. |
| `read_smart` | Token-saving file reader with AST diff caching (50-99% savings). |

Previous versions exposed 8 tools — v0.7.0 unified them into 3 for cross-LLM consistency. The server handles routing internally.

## Auto-triggers

After install, Claude Code auto-triggers memory operations on phrases like:

- "Use Linksee Memory" (system prompt incantation)
- "how did I solve this before?" / "前にこの問題どう解決したっけ"
- "same error again" / "また同じエラーだ"
- "remember this: ..." / "覚えておいて: ..."
- New task start, file edits, decision moments, errors

## Three pillars

1. **Token savings** via `read_smart` — sha256 + AST/heading/indent chunking. Re-reads return only diffs. **86% saved on a typical TS file edit, 99% on unchanged re-reads.**
2. **Cross-agent portability** — one SQLite file, not a cloud service. Same brain for every MCP-speaking agent.
3. **WHY-first structured memory** — 6 layers: goal / context / emotion / implementation / caveat / learning. The `caveat` layer (pain lessons) is auto-protected from forgetting.

## Comparison

| | Mem0 / Letta / Zep | Claude built-in memory | **linksee-memory** |
|---|---|---|---|
| Cross-agent | cloud-dependent | Claude only | single local file |
| 6-layer WHY structure | flat | flat md | goal/context/emotion/impl/caveat/learning |
| File diff cache (AST) | no | no | 50-99% token savings |
| Per-edit user-intent | no | no | unique |
| Ebbinghaus forgetting | partial | no | caveat protected |
| Local-first / private | no | yes | yes |

## Inside this plugin

```
linksee-memory-plugin/
  .claude-plugin/
    plugin.json                 # plugin manifest
  .mcp.json                    # launches linksee-memory via npx
  skills/
    cross-session-memory/
      SKILL.md                 # auto-invocation rules
  README.md
```

## Privacy

- **Local-first**: SQLite DB at `~/.linksee-memory/memory.db`. Nothing leaves your machine.
- **Opt-in telemetry only**: anonymous usage counts can be enabled with `LINKSEE_TELEMETRY=basic`. No content is ever sent.
- Full privacy contract: [linksee-memory#telemetry](https://github.com/michielinksee/linksee-memory#telemetry-opt-in-off-by-default)

## Related

- **Core MCP server**: [npm: linksee-memory](https://www.npmjs.com/package/linksee-memory)
- **Docs**: [docs.linksee.app](https://docs.linksee.app)
- **Pair with**: [kansei-link plugin](https://github.com/michielinksee/kansei-link-plugin) — external SaaS discovery to pair with your personal memory

## License

MIT — Synapse Arrows PTE. LTD.
