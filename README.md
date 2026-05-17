# @agiterra/seance-codex

> Borrow AI personae in Codex. Skills + MCP tools on top of [`@agiterra/seance-tools`](https://github.com/agiterra/seance-tools).

The Codex adapter for the seance pattern — feature parity with [`@agiterra/seance-claude-code`](https://github.com/agiterra/seance-claude-code), invoked Codex-style.

```
$register alex workshop162/Alex
$summon alex
```

After `summon`, your Codex session takes on the personai's identity (voice, autonomy rules, vault access) while you continue signing on Wire as yourself.

## Quick setup

**Pre-release direct install** (current — soak-testing before central marketplace publish):

This repo is its own one-plugin marketplace. Add it directly:

```
codex plugin marketplace add agiterra/seance-codex
```

Then enable seance in `~/.codex/config.toml`:

```toml
[plugins."seance@seance-codex"]
enabled = true
```

**Once promoted to `agiterra/claude-marketplace`** (TBD — after v0.1.0 soak):

```
codex plugin marketplace add agiterra/claude-marketplace
# seance entry will be available as seance@agiterra
```

### Prerequisites
- Bun (https://bun.sh) — the plugin bootstraps it via `ensure-bun.sh` if missing
- A registered personai repo

## Skills

Invoked via `$skill` (or `/skills` to pick from a list):

- `$register <name> <repo>` — add a personai to the local cache
- `$list` — show registered personae
- `$summon <name>` — pull latest, take on the persona
- `$return` — drop the persona overlay
- `$lookup <query> [<name>...]` — multi-vault search
- `$unregister <name>` — remove from the registry

## MCP tools

Surfaced under namespace `seance`, identical to the CC adapter:

- `seance_register(name, repo)`
- `seance_list()`
- `seance_borrow(name)`
- `seance_search(query, personae[], top_k?)`
- `seance_unregister(name)`

The MCP server is runtime-portable — same `server.ts` as `seance-claude-code`. Codex sets `CLAUDE_PLUGIN_ROOT` for compat, so the `.mcp.json` spawn command works unchanged.

## Cross-runtime story

A personai borrowed in Codex behaves equivalently to the same personai borrowed in CC — same `CLAUDE.md`, same vault, same recent journal. **Slightly different voice across runtimes** (Codex's model has different judgment / phrasing than Anthropic's) is a feature, not a bug. Same identity contract, different execution. Use for capability-profile fit per task, uncorrelated PR review, and provider-outage resilience.

## License

MIT
