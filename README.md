# @agiterra/seance-codex

> Borrow AI personae in Codex. Skills + MCP tools on top of [`@agiterra/seance-tools`](https://github.com/agiterra/seance-tools).

The Codex adapter for the seance pattern — feature parity with [`@agiterra/seance-claude-code`](https://github.com/agiterra/seance-claude-code), invoked Codex-style.

```
$register alex agiterra/Alex
$summon alex
```

After `summon`, your Codex session takes on the personai's identity (voice, autonomy rules, vault access) while you continue signing on Wire as yourself.

## Quick setup

> **Codex 0.130 install path.** Codex 0.130 has no `codex plugin install` / `add` command, so `marketplace add` alone doesn't install a third-party plugin. Two steps get you all the way there: install the *skills* via the built-in Skill Installer, then wire the *MCP server* directly in `~/.codex/config.toml`.

### 1. Install the skills

From any shell:

```bash
python3 ~/.codex/skills/.system/skill-installer/scripts/install-skill-from-github.py \
  --method git \
  --repo agiterra/seance-codex \
  --path skills/summon skills/register skills/list skills/return skills/lookup skills/unregister
```

This installs each skill directory into `~/.codex/skills/<name>/`. Codex's user-skill discovery picks them up — no plugin manifest required.

(`--method git` forces the git-sparse-checkout fallback. The default `--method auto` tries a direct download first, which hits SSL cert verify errors on stock macOS Python 3.12.)

### 2. Wire the MCP server in `~/.codex/config.toml`

Clone this repo somewhere local (`~/NewProjects/seance-codex/` is fine), then add:

```toml
[mcp_servers.seance]
command = "bun"
args = ["run", "--cwd", "/Users/YOU/NewProjects/seance-codex", "--shell=bun", "--silent", "start"]
```

The `start` script (`bun install --no-summary && bun run server.ts`) self-installs deps on first run, so you don't need a separate `bun install` step.

### 3. Restart Codex

```
codex --yolo    # or whatever flags you normally use
```

Verify with `/skills` — you should see `summon`, `register`, `list`, `return`, `lookup`, `unregister`.

### Prerequisites
- Bun (https://bun.sh) for the MCP server
- Git for the Skill Installer's sparse-checkout path

### Future install (once `codex plugin install` ships)

When a newer Codex CLI version supports proper third-party plugin install, the marketplace path becomes:

```
codex plugin marketplace add agiterra/seance-codex
# + codex plugin install seance@seance-codex  (when this command exists)
```

That single-step install will replace steps 1 and 2 above. The current two-step path stays valid as long as the files are where they are.

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
