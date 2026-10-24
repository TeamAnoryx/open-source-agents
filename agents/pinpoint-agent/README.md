# Pinpoint Agent

> A CLI chat agent that records every turn into the Pinpoint MCP server and uses its tools when you say things like "pin that" or "take me back to pin 2". Runs on Gemini or a fully offline mock model.

> **Status: 🚧 Work in progress (Draft PR).** Built at GDG Cloud Bengaluru FORK. BUILD. MERGE., 24 Oct 2026. The agent source is not written yet; only scaffold, config and docs exist. No demo output is shown because no demo has run.

Companion to [`tools/pinpoint-mcp-server`](../../tools/pinpoint-mcp-server/). Adapted concept from [TeamAnoryx/ai-pinpoint](https://github.com/TeamAnoryx/ai-pinpoint) (MIT © 2026 Anoryx Tech Solutions).

---

## Overview

### Problem Statement & Intended Users
- **Problem**: Long chats with an agent bury the messages you care about, and the agent has no memory tool to return to them.
- **Intended Users**: Developers prototyping agent UX, and people who want a pin-and-return workflow in a terminal chat.

### Key Features
- Records every human message and every assistant reply through the MCP server. The model never records anything itself.
- Natural-language pin, bookmark, list, search, navigate, update and unpin, routed to MCP tools.
- Navigation view showing the pinned message with context.
- Destructive actions (unpin, replace-import) ask `y/N` in the terminal first.
- Gemini mode (`@google/genai`) or offline mock mode with no API key.

### Implementation Status

| Item | Status |
| :--- | :--- |
| Project scaffold (package.json, eslint config, .gitignore, .env.example) | Done |
| Documentation set (README, docs/) | In progress |
| Config and constants | In progress |
| Guardrails (input cap, tool allowlist, session injection, output wrap, call cap) | In progress |
| MCP client over stdio | In progress |
| Mock intent model (offline) | In progress |
| Gemini adapter (`@google/genai`, manual function calling) | In progress (not started; may be deferred) |
| Agent turn loop and confirmation gate | In progress |
| CLI REPL (`--mock`, `--once`, `--session`, `--server`) | In progress |
| Demo script (`scripts/demo.js`) | In progress |
| Tests (T-AG-1 .. T-AG-9) | In progress (0 tests exist; `npm test` passes vacuously) |

---

## Technical Stack

- **Language & Runtime**: JavaScript (ES modules), Node.js 20+
- **Agent Framework / Core Library**: Model Context Protocol client (`@modelcontextprotocol/sdk` 1.32.2, stdio transport), custom turn loop
- **Models**: Gemini via `@google/genai` 2.31.0 (default `MODEL_NAME=gemini-3.8-flash`; re-check https://ai.google.dev/models before use), or a deterministic regex mock model
- **External APIs / Tools**: Pinpoint MCP server (local, stdio). Google Gemini API only in Gemini mode.

---

## Architecture & Data Flow

```
Human ⇄ CLI REPL (src/index.js)
          │
          ▼
      agent.js  ── turn loop, tool loop, confirmation gate
          │
          ├─▶ llm/gemini.js  (Gemini mode)  ─▶ Google Gemini API
          ├─▶ llm/mock.js    (mock mode, offline)
          │
          ├─▶ guardrails.js  (input cap, allowlist, sessionId injection, output cap)
          │
          └─▶ mcp-client.js ─▶ stdio ─▶ tools/pinpoint-mcp-server/src/index.js
```

The agent records each human turn and each final assistant reply with `record_message` in code. The model is never given `record_message`, `start_session`, or `delete_session`.

---

## Prerequisites

- Node.js 20 or newer (Node 24 is the version used in this build session)
- npm
- The server folder installed first (`tools/pinpoint-mcp-server`, `npm ci`), because the agent spawns the server
- Optional: a Gemini API key from https://aistudio.google.com/apikey (not needed for mock mode)

---

## Installation & Setup

Install order matters: server first, then agent.

PowerShell:
```powershell
cd tools\pinpoint-mcp-server
npm ci
cd ..\..\agents\pinpoint-agent
npm ci
Copy-Item .env.example .env
```

bash:
```bash
cd tools/pinpoint-mcp-server && npm ci && cd ../../agents/pinpoint-agent
npm ci
cp .env.example .env
```

Only edit `.env` to add `GEMINI_API_KEY` if you want Gemini mode. Mock mode needs no key.

---

## Configuration & Environment Variables

| Variable Name | Required | Default | Description |
| :--- | :--- | :--- | :--- |
| `GEMINI_API_KEY` | No (mock mode without it) | *None* | Google AI Studio key. Read from env only. Never logged. Never passed to the server process. |
| `MODEL_NAME` | No | `gemini-3.8-flash` | Gemini model id. Re-check https://ai.google.dev/models before the demo. |
| `PINPOINT_SERVER_PATH` | No | `../../tools/pinpoint-mcp-server/src/index.js` (relative to this folder) | MCP server entry point |
| `PINPOINT_DATA_DIR` | No | server default | Passed through to the server process |
| `AGENT_MAX_TOOL_CALLS` | No | `6` | Maximum tool calls per human turn |
| `AGENT_LOG_LEVEL` | No | `info` | stderr log level |

---

## How to Run

```bash
npm run start:mock        # offline mock model, no key needed
npm start                 # Gemini (needs GEMINI_API_KEY in .env)
npm start -- --once "Explain JWT refresh tokens in 3 lines"
```

Commands inside the REPL: `/pins`, `/sessions`, `/help`, `/exit`.

---

## Example Input & Output

*No transcript yet. The agent source (`src/`) and `scripts/demo.js` are not written, so `npm run demo` cannot run. A transcript will be pasted here only after it runs. Live Gemini: not tested.*

---

## How to Test

```bash
npm test                # node:test suite
npm run test:coverage   # with coverage
npm run verify          # lint, coverage, secret scan, npm audit, demo
```

*Results pending. No test has been run against the final code yet.*

---

## Known Limitations

- The mock model is regex-based. It understands a fixed set of phrasings only.
- Gemini may pick a wrong tool. Guardrails block anything outside the allowlist. A wrong but allowed call is still possible.
- Gemini API usage is billed per Google pricing (https://ai.google.dev/pricing). No cost figures are stated here.
- A live Gemini run is pending. If none is done before this PR leaves draft, this is recorded as "Gemini mode not live-tested".
- Conversation turns sent to Gemini leave the machine. Mock mode does not.

---

## Security & Privacy Considerations

- **Secrets Handling**: `GEMINI_API_KEY` is read from env only, is never logged, and is not passed to the MCP server child process. Errors are rewritten so the key cannot appear in them.
- **Data Privacy**: In Gemini mode, the human's messages and the assistant's replies, plus tool results, are sent to Google's API. In mock mode nothing leaves the machine.
- **Safety**: The agent runs no shell commands and reads no files except `.env`. Model tool calls are checked against an allowlist. `sessionId` is always overwritten by the agent. Destructive calls need a human `y/N` in interactive mode and are auto-denied in `--once` mode. Stored conversation text is wrapped as untrusted data, and the system prompt says it is data, not instructions.

---

## Future Improvements

- [ ] Streaming responses.
- [ ] Additional local model adapters (e.g. Ollama).
- [ ] Richer navigation view with a terminal UI.

---

## License

MIT, per the repository license.
