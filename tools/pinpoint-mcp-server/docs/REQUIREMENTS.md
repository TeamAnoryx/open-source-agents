# Requirements — pinpoint-mcp-server

Status legend: **Planned** (not yet implemented). Nothing here is claimed as done until the audit says so.

## Functional Requirements

| ID | Requirement |
| :--- | :--- |
| FR-1 | Record conversation turns (role, text) into a session, with ordinal and message id. |
| FR-2 | Pin a message selected by `messageId`, `ordinal`, `last` (assistant/user/any), or `textQuery`. Exactly one selector. |
| FR-3 | Bookmark a message; a label is required. |
| FR-4 | List pins, filterable by session, kind, tag, with a limit. |
| FR-5 | Search pins by keyword and trigram similarity; scores below threshold dropped. |
| FR-6 | Navigate to a pin, returning the target and a context window (0–10 messages each side) plus a position string such as "message 7 of 23". |
| FR-7 | Update a pin's label, note, tags, and order (up/down/top/bottom). |
| FR-8 | Unpin only with `confirm: true`. |
| FR-9 | Export pins to a JSON bundle; import from a bundle, including `ai-pinpoint-export` bundles (snippet-only navigation). |
| FR-10 | Health report with counts, last corruption event, redaction and audit state. Never returns message text. |
| FR-11 | Idempotent pin: pinning an already-pinned message returns the existing pin with `alreadyPinned: true`. |

## Non-Functional Requirements

| ID | Requirement | Status |
| :--- | :--- | :--- |
| NFR-1 | Node.js 20 or newer, cross-platform (Windows primary, Linux/macOS). | Planned |
| NFR-2 | No native modules; two runtime dependencies (`@modelcontextprotocol/sdk`, `zod`). | Done (declared in package.json) |
| NFR-3 | Stdio transport only. | Planned |
| NFR-4 | p95 tool latency under 50 ms for 1,000 messages on a dev laptop. | **Not measured yet.** Not claimed. |
| NFR-5 | Coverage of `src/` at least 80% lines. | Not measured yet. Actual number will be reported in AUDIT.md. |

## Security Requirements

| ID | Requirement | Maps to |
| :--- | :--- | :--- |
| SR-1 | Input validated at the tool boundary and at the storage boundary. | G1 |
| SR-2 | Free text clamped; control characters stripped except `\n` and `\t`. | G2 |
| SR-3 | Capacity limits return typed errors, never crash. | G3 |
| SR-4 | Secrets redacted before storage (`[REDACTED:<type>]`). | G4 |
| SR-5 | Prompt-injection phrases flagged (`suspicious: true`), not blocked. | G5 |
| SR-6 | Import/export paths restricted to `DATA_DIR/imports` and `DATA_DIR/exports`; symlinks rejected. | G6 |
| SR-7 | Destructive tools require `confirm: true`. | G7 |
| SR-8 | Rate limit per process. | G8 |
| SR-9 | No forbidden network or process APIs in `src/` (static verifier). | G9 |
| SR-10 | Logger writes stderr only; no `console.log` in `src/`. | G10 |
| SR-11 | Unexpected errors return `INTERNAL` with a correlation id; no stack traces to the client. | G11 |
| SR-12 | Corrupt or invalid records are quarantined, never deleted. | G12 |
| SR-13 | Audit log holds args hash only, never message text, labels, notes, or file contents. | §6.8 |

## Out of Scope

- Browser extension changes.
- Multi-user authentication.
- Remote (HTTP) transport.
- Encryption at rest.
