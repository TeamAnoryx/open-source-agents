# Audit — pinpoint-mcp-server

Status: **build in progress. Nothing below has run yet.** Every result is `NOT RUN`.

## 1. Traceability Matrix

| Requirement | Guardrail | Test ID(s) | Status |
| :--- | :--- | :--- | :--- |
| FR-1 record turns | G1, G2 | T-TOOL-2 | NOT RUN |
| FR-2 pin by selector | G1 | T-ID-5, T-TOOL-2 | NOT RUN |
| FR-3 bookmark needs label | G1 | T-TOOL-2 | NOT RUN |
| FR-4 list pins | — | T-TOOL-2 | NOT RUN |
| FR-5 search ranking | — | T-SRCH-1 | NOT RUN |
| FR-6 navigate with context | — | T-ID-1..4, T-TOOL-2 | NOT RUN |
| FR-7 update / reorder | G1 | T-TOOL-2 | NOT RUN |
| FR-8 unpin with confirm | G7 | T-TOOL-2, T-TOOL-3 | NOT RUN |
| FR-9 export / import | G6, G12 | T-TRF-1, T-TRF-2, T-TRF-3 | NOT RUN |
| FR-10 health | — | T-TOOL-1 | NOT RUN |
| FR-11 idempotent pin | — | T-TOOL-5 | NOT RUN |
| SR-1 validation | G1 | T-SCH-1 | NOT RUN |
| SR-2 clamps / control chars | G2 | T-TXT-4, T-GR-5 | NOT RUN |
| SR-3 capacity limits | G3 | T-STO-5 | NOT RUN |
| SR-4 redaction | G4 | T-GR-1 | NOT RUN |
| SR-5 injection flag | G5 | T-GR-2 | NOT RUN |
| SR-6 path safety | G6 | T-GR-3 | NOT RUN |
| SR-7 confirm | G7 | T-TRF-4, T-TOOL-2 | NOT RUN |
| SR-8 rate limit | G8 | T-GR-4 | NOT RUN |
| SR-9 offline | G9 | T-OFF-1 | NOT RUN |
| SR-10 stdout hygiene | G10 | T-OFF-2 | NOT RUN |
| SR-11 error containment | G11 | T-TOOL-3 | NOT RUN |
| SR-12 quarantine | G12 | T-STO-3, T-STO-4 | NOT RUN |
| SR-13 audit excludes text | §6.8 | T-AUD-1, T-AUD-2 | NOT RUN |

## 2. Check Results

| Check | Command | Result |
| :--- | :--- | :--- |
| S1 Lint | `npm run lint` | NOT RUN |
| S2 Tests + coverage | `npm run test:coverage` | NOT RUN |
| S3 Offline | `npm run verify:offline` | NOT RUN |
| S4 Secret scan | `npm run scan:secrets` | NOT RUN |
| S5 Dependency audit | `npm run audit:deps` | NOT RUN |
| S6 Tracked-file hygiene | `git ls-files` review | NOT RUN |
| S7 Scope | `git diff --name-only` review | NOT RUN |
| S8 Smoke | `npm run smoke` | NOT RUN |
| S9 Demo (agent) | `npm run demo` in agent folder | NOT RUN |

Environment: to be recorded at run time.

## 3. Known gaps

Build in progress. Coverage target (80%) not yet measured.
