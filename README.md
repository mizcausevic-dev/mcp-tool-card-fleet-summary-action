# mcp-tool-card-fleet-summary-action

[![CI](https://github.com/mizcausevic-dev/mcp-tool-card-fleet-summary-action/actions/workflows/ci.yml/badge.svg)](https://github.com/mizcausevic-dev/mcp-tool-card-fleet-summary-action/actions/workflows/ci.yml)
[![License: AGPL-3.0-or-later](https://img.shields.io/badge/License-AGPL--3.0--or--later-blue.svg)](LICENSE)

GitHub Action that walks a directory of **MCP Tool Card** documents, counts by side-effect class and pii_exposure, surfaces governance gaps, posts a Markdown summary as a PR comment, and **fails the build** when any high-severity finding is present.

Wraps [`mcp-tool-card-fleet-summary`](https://github.com/mizcausevic-dev/mcp-tool-card-fleet-summary) — same finding logic, vendored into the action for self-contained execution.

Part of the [Kinetic Gain Suite](https://suite.kineticgain.com/). Sibling of [`agent-card-fleet-summary-action`](https://github.com/mizcausevic-dev/agent-card-fleet-summary-action), [`llm-cost-rollup-action`](https://github.com/mizcausevic-dev/llm-cost-rollup-action), and [`k8s-pre-merge-action`](https://github.com/mizcausevic-dev/k8s-pre-merge-action).

---

## Usage

```yaml
name: MCP governance
on:
  pull_request:
    paths: ["mcp-tools/**"]

jobs:
  fleet-summary:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: mizcausevic-dev/mcp-tool-card-fleet-summary-action@v0.1-shipped
        with:
          cards-dir: mcp-tools/
          fail-on-high: true   # default
```

## Inputs

| input            | required | default       | description |
|---|---|---|---|
| `cards-dir`      | ✓        | —             | Directory containing `*.json` Tool Card documents. |
| `comment-on-pr`  |          | `auto`        | `auto` posts only on `pull_request` events; `true`/`false` force the behavior. |
| `fail-on-high`   |          | `true`        | Fail the run when any high-severity finding is present. |
| `github-token`   |          | `${{ github.token }}` | Token used to post the PR comment. |

## Outputs

| output                     | description |
|---|---|
| `total-cards`              | Number of Tool Cards analyzed. |
| `high-findings`            | Count of high-severity findings. |
| `destructive-tools`        | Number of destructive tools in the fleet. |
| `tools-requiring-approval` | Number of tools declaring `safety.human_approval_required: true`. |

## What it flags

| Code | Severity | Rule |
|---|---|---|
| `destructive-without-human-approval` | 🔴 | `side_effect_class=destructive` but `safety.human_approval_required` is not `true`. |
| `destructive-without-audit-log` | 🔴 | Destructive tool has no `audit.log_uri`. |
| `high-pii-without-rate-limit` | 🔴 | `pii_exposure=high` but `safety.rate_limited` is not `true`. |
| `writes-secrets-without-audit-log` | 🔴 | `secrets_exposure=writes_secret_material` but no `audit.log_uri`. |
| `destructive-without-incident-response-uri` | 🟠 | Destructive tool has no `audit.incident_response_uri`. |
| `no-refusal-modes-on-destructive` | 🟠 | Destructive tool declares no `safety.refusal_modes`. |
| `no-tested-with` | 🟠 on destructive / 🟡 elsewhere | Tool has no `tested_with` evidence. |
| `no-performance-metrics` | ℹ️ | No `performance.p*_latency_ms`. |
| `no-cost-metrics` | ℹ️ | No `cost.per_call_usd`. |

## Composes with

- [**`mcp-tool-card-fleet-summary`**](https://github.com/mizcausevic-dev/mcp-tool-card-fleet-summary) — the library this wraps.
- [**`mcp-tool-card-spec`**](https://github.com/mizcausevic-dev/mcp-tool-card-spec) — the schema this reads.
- [**`mcp-tool-card-stamp`**](https://github.com/mizcausevic-dev/mcp-tool-card-stamp) · [**`mcp-tool-card-diff`**](https://github.com/mizcausevic-dev/mcp-tool-card-diff) · [**`mcp-tool-card-summary`**](https://github.com/mizcausevic-dev/mcp-tool-card-summary) — full Tool Card tool family.
- [**`agent-card-fleet-summary-action`**](https://github.com/mizcausevic-dev/agent-card-fleet-summary-action) — sibling Action for the A2A side.

## License

[AGPL-3.0-or-later](LICENSE)
