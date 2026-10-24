# Audit — pinpoint-mcp-server

Status: **build in progress.** Unit-level checks that exist have run and pass. The MCP tool layer, entry point, smoke test and offline/secret verifiers are **not implemented yet**; their rows say so.

Environment (recorded 24 Oct 2026): Windows 11 Pro 10.0.26200, Node v24.13.1, npm 11.8.0. Dependency versions are the ones pinned in `package-lock.json`.

## 1. Traceability Matrix

Status key: **PASS** = test IDs exist and pass (unit level). **PARTIAL** = some cited tests pass, the rest are not implemented. **NOT IMPLEMENTED** = no code or test yet.

| Requirement | Guardrail | Test ID(s) | Status |
| :--- | :--- | :--- | :--- |
| FR-1 record turns | G1, G2 | T-TOOL-2 | NOT IMPLEMENTED |
| FR-2 pin by selector | G1 | T-ID-5 (pass), T-TOOL-2 | PARTIAL |
| FR-3 bookmark needs label | G1 | T-TOOL-2 | NOT IMPLEMENTED |
| FR-4 list pins | — | T-TOOL-2 | NOT IMPLEMENTED |
| FR-5 search ranking | — | T-SRCH-1 | PASS |
| FR-6 navigate with context | — | T-ID-1..4 (pass), T-TOOL-2 | PARTIAL |
| FR-7 update / reorder | G1 | T-TOOL-2 | NOT IMPLEMENTED |
| FR-8 unpin with confirm | G7 | T-TOOL-2, T-TOOL-3 | NOT IMPLEMENTED |
| FR-9 export / import | G6, G12 | T-TRF-1, T-TRF-2, T-TRF-3 | NOT IMPLEMENTED |
| FR-10 health | — | T-TOOL-1 | NOT IMPLEMENTED |
| FR-11 idempotent pin | — | T-TOOL-5 | NOT IMPLEMENTED |
| SR-1 validation | G1 | T-SCH-1 | PASS |
| SR-2 clamps / control chars | G2 | T-TXT-4, T-GR-2 | PASS |
| SR-3 capacity limits | G3 | T-STO-5 | PASS |
| SR-4 redaction | G4 | T-GR-1 | PASS |
| SR-5 injection flag | G5 | T-GR-2 | PASS |
| SR-6 path safety | G6 | T-GR-3 | PASS |
| SR-7 confirm | G7 | T-TRF-4, T-TOOL-2 | NOT IMPLEMENTED |
| SR-8 rate limit | G8 | T-GR-4 | NOT IMPLEMENTED (cut, see Known gaps) |
| SR-9 offline | G9 | T-OFF-1 | NOT IMPLEMENTED |
| SR-10 stdout hygiene | G10 | T-OFF-2 | NOT IMPLEMENTED |
| SR-11 error containment | G11 | T-TOOL-3 | NOT IMPLEMENTED |
| SR-12 quarantine | G12 | T-STO-3, T-STO-4 | PASS |
| SR-13 audit excludes text | §6.8 | T-AUD-1, T-AUD-2 | PASS |

Additional unit tests passing (not mapped to a requirement row above): T-ID-1..4, T-STO-1, T-STO-2, T-STO-6, T-STO-7, T-TXT-1..3, T-GR-5, read-failure regression test in `store.test.js`, data-dir-under-file test in `config.test.js`.

## 2. Check Results

| Check | Command | Result |
| :--- | :--- | :--- |
| S1 Lint | `npm run lint` | PASS (exit 0, 0 warnings) |
| S2 Tests + coverage | `npm run test:coverage` | PASS: 36/36 tests. Line 93.73%, branch 75.81%, function 91.30% |
| S3 Offline | `npm run verify:offline` | NOT IMPLEMENTED (`scripts/verify-offline.js` not written) |
| S4 Secret scan | `npm run scan:secrets` | NOT IMPLEMENTED (`scripts/secret-scan.js` not written) |
| S5 Dependency audit | `npm audit --omit=dev --audit-level=high` | PASS: found 0 vulnerabilities |
| S6 Tracked-file hygiene | `git ls-files` for node_modules, .env, .data, .jsonl, store json | PASS: no matches |
| S7 Scope | `git diff --name-only upstream/main...HEAD` | PASS for this branch: only the two project folders (scaffold commit `8b41604` plus this branch's commits) |
| S8 Smoke | `npm run smoke` | NOT IMPLEMENTED (`scripts/smoke.js` and the 13 tools not written) |
| S9 Demo (agent) | `npm run demo` in agent folder | NOT IMPLEMENTED (agent `src/` not written) |

## 3. Known gaps

Built and tested so far: config, constants, logger, ids, text primitives, schema validators, JSON store (atomic write, quarantine), identity resolution, search, guardrails (redaction, injection flag, control-char strip, path-safe file names, untrusted wrapper), audit log.

Not built yet: the 13 MCP tools, `src/server.js`, `src/index.js`, `src/transfer.js`, smoke test, offline verifier, secret scanner, Stdio end-to-end test.

Cut from this release (recorded as Deferred in the README):
- `update_pin` `moveTo` (label/note/tags only kept).
- `ai-pinpoint-export` bundle import (native bundle only kept).
- `delete_session` tool.
- Rate limiter (G8, SR-8) and its test T-GR-4. The DoS row in SECURITY.md is only partly covered (size and capacity limits remain).
- Coverage threshold as a gate (actual numbers reported above only).
- Gemini live testing (agent side; mock mode is the deliverable).

Tool count: the enumerated keep list has 13 tools (`start_session`, `record_message`, `pin_message`, `bookmark_message`, `list_pins`, `search_pins`, `navigate_to_pin`, `update_pin`, `unpin`, `list_sessions`, `export_pins`, `import_pins`, `health`). The original brief said 12; the list has 13. Docs use 13.
