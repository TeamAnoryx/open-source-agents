# Audit — pinpoint-agent

Status: **build in progress. The agent is not implemented.** `src/` contains only an empty `llm/` folder. `test/` and `scripts/` contain no files. Every requirement below is NOT IMPLEMENTED.

Environment (recorded 24 Oct 2026): Windows 11 Pro 10.0.26200, Node v24.13.1, npm 11.8.0. Pinned SDKs from `package-lock.json`: `@modelcontextprotocol/sdk` 1.32.2, `@google/genai` (range `^2.31.0`; resolved version to be recorded when the adapter is written).

## 1. Traceability Matrix

| Requirement | Guardrail | Test ID(s) | Status |
| :--- | :--- | :--- | :--- |
| FR-A1 record turns | agent code | T-AG-8 | NOT IMPLEMENTED |
| FR-A2 pin last answer | mock intent | T-AG-1, T-AG-8 | NOT IMPLEMENTED |
| FR-A3 bookmark needs label | prompt rule 3 | T-AG-1 | NOT IMPLEMENTED |
| FR-A4 search pins | mock intent | T-AG-1 | NOT IMPLEMENTED |
| FR-A5 navigation view | — | T-AG-8 | NOT IMPLEMENTED |
| FR-A6 confirm gate | G-A5 | T-AG-6 | NOT IMPLEMENTED |
| FR-A7 flags | — | T-AG-8 (scripted) | NOT IMPLEMENTED |
| FR-A8 local commands | — | (manual) | NOT IMPLEMENTED |
| SR-A1 input clamp | G-A1 | T-AG-4 | NOT IMPLEMENTED |
| SR-A2 allowlist | G-A2 | T-AG-2 | NOT IMPLEMENTED |
| SR-A3 sessionId injection | G-A3 | T-AG-3 | NOT IMPLEMENTED |
| SR-A4 call cap | G-A4 | T-AG-5 | NOT IMPLEMENTED |
| SR-A5 destructive gate | G-A5 | T-AG-6 | NOT IMPLEMENTED |
| SR-A6 output truncation | G-A6 | T-AG-7 (planned) | NOT IMPLEMENTED |
| SR-A7 key handling | G-A7 | T-AG-7 | NOT IMPLEMENTED |
| SR-A8 no shell/fs | G-A8 | (static review) | NOT IMPLEMENTED |
| SR-A9 model timeout | G-A9 | (planned) | NOT IMPLEMENTED |
| Injection resistance | prompt rule 5 | T-AG-9 | NOT IMPLEMENTED |

## 2. Check Results

| Check | Command | Result |
| :--- | :--- | :--- |
| S1 Lint | `npm run lint` | Exit 0, but there are no source files to lint yet; this only checks the config |
| S2 Tests + coverage | `npm test` | 0 tests exist. Exit 0 is vacuous and does not evidence anything |
| S3 Offline | — | Not applicable to the agent (server only) |
| S4 Secret scan | `npm run scan:secrets` | NOT IMPLEMENTED (script not in `package.json`) |
| S5 Dependency audit | `npm audit --omit=dev --audit-level=high` | PASS: found 0 vulnerabilities |
| S6 Tracked-file hygiene | `git ls-files` | `node_modules/` is git-ignored by `.gitignore`; `.env` is not tracked (only `.env.example`) |
| S7 Scope | `git diff --name-only upstream/main...HEAD` | Only `agents/pinpoint-agent/` paths on this branch |
| S8 Smoke (server) | — | Not applicable to the agent |
| S9 Demo | `npm run demo` | NOT IMPLEMENTED (`scripts/demo.js` not written) |

Live Gemini run: NOT TESTED.

## 3. Known gaps

Not built: the whole agent (config, logger, MCP client, guardrails, prompts, mock model, agent loop, CLI, demo, all T-AG tests). The Gemini adapter is not started and may be deferred. The `npm test` exit 0 must not be read as evidence until real tests exist.
