# Decisions — pinpoint-mcp-server

| ID | Decision | Reason |
| :--- | :--- | :--- |
| D-1 | JSON file store instead of SQLite. | No native build on Windows; reduces setup risk for contributors. |
| D-2 | Stdio transport only. | Smallest attack surface; matches how MCP hosts launch servers. |
| D-3 | The agent records turns; the model cannot. | Keeps the record trustworthy and out of model control (see agent docs). |
| D-4 | Injection detection flags, does not block. | Blocking would silently drop user content; flagging preserves it and makes it visible. |
| D-5 | One PR contains the server and the agent. | The agent depends on the server. Happy to split into two PRs (tool first) if maintainers prefer. |
| D-6 | JavaScript (ESM), not TypeScript. | Matches the build time box; logic is ported from ai-pinpoint TypeScript with attribution. |
| D-7 | Contribute docs-first as a Draft PR; code follows in later commits on the same branch. | Lets reviewers see the design early. Ready-for-review only after all checks pass. |
