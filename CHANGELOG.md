# Changelog

## v0.1.0 — 2026-05-27

- Initial release: GitHub Action wrapping `mcp-tool-card-fleet-summary` for PR gating.
- Inputs: `cards-dir` (required), `comment-on-pr` (auto/true/false), `fail-on-high` (default true), `github-token`.
- Outputs: `total-cards`, `high-findings`, `destructive-tools`, `tools-requiring-approval`.
- Vendored 9-code fleet-summary logic — same findings as the standalone library.
- Posts per-PR Markdown comment when run on `pull_request` events with a valid token.
- Fails the run (exit 1) on any high-severity finding by default.
- Composite Node 20 action with `dist/index.js` committed for SHA/tag pinning.
- 3-card fixture corpus (clean read, clean destructive, no-approval destructive).
- Sibling of `agent-card-fleet-summary-action` — pairs the Action family across A2A + MCP.
- Node 20/22 CI (lint, typecheck, coverage, build, `npm audit`), AGPL-3.0-or-later, Dependabot.
