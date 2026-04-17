# linksee-memory (Claude Code Plugin)

> The cross-agent brain Claude Code doesn't have. 6-layer structured memory (goal/context/emotion/impl/caveat/learning) + AST-aware file diff cache. **Local-first, private, single SQLite file.**

This is the Claude Code Plugin bundle. It ships:

- **MCP server** (`linksee-memory`) — 6 tools: `remember` / `recall` / `recall_file` / `read_smart` / `forget` / `consolidate`
- **Skill** (`cross-session-memory`) — auto-invokes memory operations based on user intent

## The problem this solves

Every Claude Code session starts from zero. If you fixed a bug yesterday, today's agent doesn't remember *why* or *how*. If you made an architectural decision last week, today's agent doesn't know. The agent walks into the same wall again and again.

Mem0 / Letta / Zep try to fix this with flat fact lists — but the agent sees "edited file X 30 times" and has no idea why. **linksee-memory keeps the WHY.**

## Install

Once listed in the official marketplace:

```bash
/plugin install linksee-memory
```

### Or install locally for testing

```bash
git clone https://github.com/michielinksee/linksee-memory-plugin.git
claude --plugin-dir ./linksee-memory-plugin
```

## What it does

After install, Claude Code auto-triggers memory operations on phrases like:

- "前にこの問題どう解決したっけ" / "how did I solve this before?"
- "また同じエラーだ" / "same error again"
- "覚えておいて: …" / "remember this: …"
- New task start, file edits, decision moments

The agent calls `recall` / `remember` / `read_smart` automatically. Cross-agent memory means **the same brain works across Claude Code, Cursor, ChatGPT Desktop** — one SQLite file at `~/.linksee-memory/memory.db`.

## Three pillars

1. **Token savings** via `read_smart` — sha256 + AST/heading/indent chunking. Re-reads return only diffs. **86% saved on a typical TS file edit, 99% on unchanged re-reads.**
2. **Cross-agent portability** — one SQLite file, not a cloud service. Same brain for every MCP-speaking agent.
3. **WHY-first structured memory** — 6 layers: goal / context / emotion / implementation / caveat / learning. The `caveat` layer (pain lessons) is auto-protected from forgetting. Active goals bypass decay.

## Comparison

| | Mem0 / Letta / Zep | Claude Code auto-memory | **linksee-memory** |
|---|---|---|---|
| Cross-agent | △ (cloud) | ❌ Claude only | ✅ single file |
| 6-layer WHY structure | ❌ flat | ❌ flat md | ✅ |
| File diff cache (AST) | ❌ | ❌ | ✅ 50-99% token savings |
| Per-edit user-intent linkage | ❌ | ❌ | ✅ unique |
| Active forgetting (Ebbinghaus) | △ | ❌ | ✅ caveat protected |
| Local-first / private | ❌ | ✅ | ✅ |

## Inside this plugin

```
linksee-memory-plugin/
├── .claude-plugin/
│   └── plugin.json                 # plugin manifest
├── .mcp.json                       # launches linksee-memory via npx
├── skills/
│   └── cross-session-memory/
│       └── SKILL.md                # auto-invocation rules
└── README.md                       # this file
```

## Pricing

**Free forever.** Local-first, no hosted component, no account, no API key. Runs entirely on your machine.

## Privacy

- **Local-first**: SQLite DB at `~/.linksee-memory/memory.db`. Nothing leaves your machine by default.
- **Opt-in telemetry only**: anonymous usage counts can be enabled with `LINKSEE_TELEMETRY=basic`. **No conversation content, file content, entity names, or project paths are ever sent.**
- Full privacy contract: [github.com/michielinksee/linksee-memory#telemetry-opt-in-off-by-default](https://github.com/michielinksee/linksee-memory#telemetry-opt-in-off-by-default)

## Related

- **Core MCP server** (standalone): [npm: linksee-memory](https://www.npmjs.com/package/linksee-memory)
- **Skill repo** (standalone): [github.com/michielinksee/kansei-linksee-skills](https://github.com/michielinksee/kansei-linksee-skills)
- **Pair with**: [kansei-link plugin](https://github.com/michielinksee/kansei-link-plugin) — external SaaS discovery to pair with your personal memory

## Support

- **Issues**: [github.com/michielinksee/linksee-memory-plugin/issues](https://github.com/michielinksee/linksee-memory-plugin/issues)
- **MCP server issues**: [github.com/michielinksee/linksee-memory/issues](https://github.com/michielinksee/linksee-memory/issues)
- **Company**: Synapse Arrows PTE. LTD. (Singapore)

## License

MIT — Synapse Arrows PTE. LTD.
