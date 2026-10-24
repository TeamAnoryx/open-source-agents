# Pinpoint MCP Server

> A local-only MCP server that lets AI agents pin, bookmark, search and navigate back to specific messages in long agent-human conversations. Zero network calls.

> **Status: 🚧 Work in progress (Draft PR).** Built at GDG Cloud Bengaluru FORK. BUILD. MERGE., 24 Oct 2026. Only the rows marked Done below are built and tested; the rest are not yet implemented. No output is shown for anything that has not run.

Adapted from [TeamAnoryx/ai-pinpoint](https://github.com/TeamAnoryx/ai-pinpoint) (MIT © 2026 Anoryx Tech Solutions).

---

## Overview

### Problem Statement & Intended Users
- **Problem**: In long agent sessions the messages that matter get buried. There is no standard way for an agent to bookmark a message and return to it later with its surrounding context.
- **Intended Users**: Developers building agents, and anyone using an MCP client (Claude Desktop, Claude Code, Gemini CLI, or any other MCP host).

### Key Features
- Record conversation turns, then pin or bookmark any message by id, ordinal, role, or text query.
- List, search (keyword + trigram), and navigate to a pin with surrounding context.
- Update labels, notes, tags and order; unpin with explicit confirmation.
- Export and import pins as a native JSON bundle (merge, or replace with confirmation).
- Secret redaction before storage, prompt-injection flagging, untrusted-content wrapping, path-safe transfer, and an append-only audit log that never stores message text.
- Not in this release: `ai-pinpoint-export` bundle import, `delete_session`, `update_pin` move, rate limiting (G8), coverage gate. See `docs/AUDIT.md` Known gaps.

### Implementation Status

| Item | Status |
| :--- | :--- |
| Project scaffold (package.json, eslint config, .gitignore, .env.example) | Done |
| Documentation set (README, docs/, SECURITY, DECISIONS) | In progress |
| Config, constants, logger, ids | Done |
| Text primitives (hash, normalise, trigram, clamp) | Done |
| Schema validators and JSON store (atomic write, quarantine) | Done |
| Identity resolution (id → hash → fuzzy) and search | Done |
| Guardrails (redaction, injection flag, path safety; rate limit cut) | Done |
| Audit log | Done |
| MCP tools (13 kept: start_session, record_message, pin_message, bookmark_message, list_pins, search_pins, navigate_to_pin, update_pin, unpin, list_sessions, export_pins, import_pins, health) | In progress |
| Entry point (`src/index.js`) and server (`src/server.js`) | In progress |
| Transfer (`src/transfer.js`) | In progress |
| Offline verifier (`scripts/verify-offline.js`) | In progress |
| Secret scan (`scripts/secret-scan.js`) | In progress |
| Smoke test over stdio (`scripts/smoke.js`) | In progress |
| Cut: `delete_session`, ai-pinpoint bundle import, rate limiter (G8), `update_pin` moveTo, coverage gate | Deferred |

---

## Technical Stack

- **Language & Runtime**: JavaScript (ES modules), Node.js 20+
- **Core Library**: Model Context Protocol official SDK `@modelcontextprotocol/sdk` 1.32.2 (stdio transport)
- **Schema for tool inputs**: `zod` 4.x (SDK peer dependency)
- **Storage**: JSON file with atomic rename. No native modules.
- **Tests**: built-in `node:test` and `node:assert/strict`
- **Lint**: ESLint 10 with `@eslint/js` and `globals`
- **Models**: none. This server does not call any model.
- **External APIs**: none.

---

## Architecture & Data Flow

```
MCP client (Claude Desktop / Claude Code / agent)
        │  stdio (JSON-RPC, stdout = protocol)
        ▼
src/index.js  ──▶  src/server.js  (McpServer, 13 tools)
                        │
                        ├─▶ guardrails.js  (redaction, injection flag, path safety, rate limit)
                        ├─▶ schema.js      (hand validators at storage boundary)
                        ├─▶ identity.js    (id → hash → fuzzy → not_found)
                        ├─▶ search.js      (keyword + trigram ranking)
                        ├─▶ transfer.js    (export / import, ai-pinpoint bundles)
                        ├─▶ store.js  ──▶  store.json   (mutex, tmp write, rename)
                        │                  quarantine.json, store.corrupt-*.json
                        └─▶ audit.js  ──▶  audit.jsonl  (args hash only, no text)
```

Identity resolution order for `navigate_to_pin`: exact `messageId` → `textHash` (nearest ordinal on duplicates) → trigram fuzzy match on the snippet (repairs the pin) → `not_found` (the pin is kept).

---

## Prerequisites

Before running this project, ensure you have:
- Node.js 20 or newer (Node 24 is the version used in this build session)
- npm (ships with Node)
- Windows, macOS or Linux. Windows is the primary target.

---

## Installation & Setup

1. **Navigate to the project directory and install:**

   PowerShell:
   ```powershell
   cd tools\pinpoint-mcp-server
   npm ci
   Copy-Item .env.example .env
   ```

   bash:
   ```bash
   cd tools/pinpoint-mcp-server
   npm ci
   cp .env.example .env
   ```

2. **Configure environment variables** (optional; every variable has a default). See the table below.

---

## Configuration & Environment Variables

| Variable Name | Required | Default | Description |
| :--- | :--- | :--- | :--- |
| `PINPOINT_DATA_DIR` | No | `<server folder>/.data` | Directory for `store.json`, `quarantine.json`, `audit.jsonl`, `exports/`, `imports/`. Must not be a filesystem root. |
| `PINPOINT_LOG_LEVEL` | No | `info` | `debug` / `info` / `warn` / `error`. Logs go to stderr only. |
| `PINPOINT_AUDIT` | No | `on` | `on` / `off`. Audit log of tool calls (args hash only). |
| `PINPOINT_REDACT` | No | `on` | `on` / `off`. Secret redaction before storage. |
| `PINPOINT_RATE_LIMIT_PER_MIN` | No | `120` | Maximum tool calls per minute for the process. |

---

## How to Run

```bash
# Starts the stdio MCP server. It waits silently on stdin. That is correct for stdio MCP.
npm start
```

Claude Desktop config (Windows: `%APPDATA%\Claude\claude_desktop_config.json`):

```json
{
  "mcpServers": {
    "pinpoint": {
      "command": "node",
      "args": ["<absolute path>\\tools\\pinpoint-mcp-server\\src\\index.js"]
    }
  }
}
```

*Claude Code and MCP Inspector commands are pending verification against the installed CLI and npm package before this PR leaves draft.*

---

## Example Input & Output

*Pending — will be replaced with real output from `npm run smoke` before this PR leaves draft.*

---

## How to Test

```bash
npm test                 # unit + integration tests (node:test)
npm run test:coverage    # with coverage
npm run verify           # lint, coverage, offline verifier, secret scan, npm audit, smoke
```

*Results pending. No test has been run against the final code yet.*

---

## Known Limitations

- Single-process writer. Two server processes on the same data directory can race.
- JSON store is not suited to very large histories (`MAX_STORE_BYTES` = 50 MB).
- Redaction is pattern-based. It can miss secrets and can over-redact.
- Injection detection is heuristic. It flags, it does not block.
- ai-pinpoint imports carry no message bodies, so navigation returns `snippet_only` plus the original thread URL.
- No encryption at rest. The data directory is protected only by OS file permissions.
- Tested platform: pending. The exact OS and Node version will be stated once tests run.

---

## Security & Privacy Considerations

- **Secrets Handling**: No secrets are needed. The server reads only its own env variables.
- **Data Privacy**: The server makes zero network calls. Stored conversation text stays in `PINPOINT_DATA_DIR` on local disk.
- **Safety**: The server runs no shell commands and spawns no child processes. Destructive tools (`unpin`, `import_pins` with `replace`) require `confirm: true`. Stored text is returned only under `untrustedContent`.
- Full threat model: [docs/SECURITY.md](docs/SECURITY.md).

---

## Future Improvements

- [ ] SQLite or FTS backend (needs a native-build decision on Windows).
- [ ] Encryption at rest.
- [ ] MCP resources for pins.
- [ ] Streamable HTTP transport with authentication.
- [ ] Live bridge to the ai-pinpoint extension.

---

## License

MIT, per the repository license. Code adapted from ai-pinpoint keeps Anoryx's MIT notice (see the credit line above and file headers).
