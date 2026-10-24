# Requirements — pinpoint-agent

Status legend: all items **Planned** unless noted.

## Functional Requirements

| ID | Requirement |
| :--- | :--- |
| FR-A1 | CLI REPL starts a session and records every human message and final assistant reply through the MCP server. |
| FR-A2 | "Pin that" / "pin your last answer" calls `pin_message` with `last: "assistant"`. |
| FR-A3 | A bookmark requires a label; the agent asks for one if missing. |
| FR-A4 | "What did I pin about X" calls `search_pins`. |
| FR-A5 | "Take me to pin N" lists pins, finds the target, and prints a navigation view with context. |
| FR-A6 | Unpin and replace-import ask `Confirm ...? (y/N)` before sending `confirm: true`. |
| FR-A7 | `--once`, `--session`, `--mock`, `--server` flags work as specified. |
| FR-A8 | `/pins`, `/sessions`, `/help`, `/exit` are handled locally without the model. |

## Non-Functional Requirements

| ID | Requirement | Status |
| :--- | :--- | :--- |
| NFR-A1 | Node.js 20 or newer, cross-platform. | Planned |
| NFR-A2 | Runs fully offline in mock mode (no key, no network). | Planned |
| NFR-A3 | Per-model-call timeout of 30 s. | Planned |
| NFR-A4 | Gemini mode latency and cost are not measured. | Not measured. Not claimed. |

## Security Requirements

| ID | Requirement | Maps to |
| :--- | :--- | :--- |
| SR-A1 | Human input capped at 8,000 chars; control chars stripped; empty input ignored. | G-A1 |
| SR-A2 | Tool allowlist; `record_message`, `start_session`, `delete_session` never offered to the model. | G-A2 |
| SR-A3 | `sessionId` overwritten by the agent on every call. | G-A3 |
| SR-A4 | Tool-call cap per human turn. | G-A4 |
| SR-A5 | Destructive calls gated; auto-denied in `--once`. | G-A5 |
| SR-A6 | Tool results truncated to 8,000 chars; `untrustedContent` wrapper kept. | G-A6 |
| SR-A7 | API key from env only; redacted in errors and logs; not passed to the server. | G-A7 |
| SR-A8 | No shell execution; no filesystem access beyond `.env`. | G-A8 |
| SR-A9 | Model call timeout. | G-A9 |
