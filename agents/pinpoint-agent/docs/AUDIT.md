# Audit — pinpoint-agent

Status: **build in progress. Nothing below has run yet.** Every result is `NOT RUN`.

## 1. Traceability Matrix

| Requirement | Guardrail | Test ID(s) | Status |
| :--- | :--- | :--- | :--- |
| FR-A1 record turns | agent code | T-AG-8 | NOT RUN |
| FR-A2 pin last answer | mock intent | T-AG-1, T-AG-8 | NOT RUN |
| FR-A3 bookmark needs label | prompt rule 3 | T-AG-1 | NOT RUN |
| FR-A4 search pins | mock intent | T-AG-1 | NOT RUN |
| FR-A5 navigation view | — | T-AG-8 | NOT RUN |
| FR-A6 confirm gate | G-A5 | T-AG-6 | NOT RUN |
| FR-A7 flags | — | T-AG-8 (scripted) | NOT RUN |
| FR-A8 local commands | — | (manual) | NOT RUN |
| SR-A1 input clamp | G-A1 | T-AG-4 | NOT RUN |
| SR-A2 allowlist | G-A2 | T-AG-2 | NOT RUN |
| SR-A3 sessionId injection | G-A3 | T-AG-3 | NOT RUN |
| SR-A4 call cap | G-A4 | T-AG-5 | NOT RUN |
| SR-A5 destructive gate | G-A5 | T-AG-6 | NOT RUN |
| SR-A6 output truncation | G-A6 | T-AG-7 (planned) | NOT RUN |
| SR-A7 key handling | G-A7 | T-AG-7 | NOT RUN |
| SR-A8 no shell/fs | G-A8 | (static review) | NOT RUN |
| SR-A9 model timeout | G-A9 | (planned) | NOT RUN |
| Injection resistance | prompt rule 5 | T-AG-9 | NOT RUN |

## 2. Check Results

| Check | Command | Result |
| :--- | :--- | :--- |
| S1 Lint | `npm run lint` | NOT RUN |
| S2 Tests + coverage | `npm run test:coverage` | NOT RUN |
| S4 Secret scan | `npm run scan:secrets` | NOT RUN |
| S5 Dependency audit | `npm run audit:deps` | NOT RUN |
| S9 Demo | `npm run demo` | NOT RUN |

Environment: to be recorded at run time (OS, `node -v`, `npm -v`, SDK versions from `package-lock.json`).

## 3. Known gaps

Build in progress. Live Gemini mode: **not tested** so far.
