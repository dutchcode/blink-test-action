# Blink Test — GitHub Action

Run a [Blinkof.ai](https://blinkof.ai) blink test on every deploy: a deep QA scan
that acts like a real user — signs up, types hostile input, follows your pay button
to checkout, checks GDPR erasure and mobile layout — and fails the build on critical
findings. Every finding comes with a paste-ready fix prompt for Cursor, Claude Code,
Lovable, Bolt, or v0.

## Usage

```yaml
- name: Blink test
  uses: dutchcode/blink-test-action@v1
  with:
    url: https://yourapp.com
    api-token: ${{ secrets.BLINKOF_API_TOKEN }}
    fail-on: critical   # critical | high | none
```

Typically placed after your deploy step, pointed at the deployed URL (production or
a preview environment).

### Prerequisites

1. Sign in at [blinkof.ai/app](https://blinkof.ai/app) and **verify ownership** of
   the host you'll scan — deep scans only run against hosts your account has proven
   it controls, regardless of plan.
2. Create an API token in the dashboard under **Developers → API tokens for CI**
   (paid plans) and store it as the `BLINKOF_API_TOKEN` repo secret.

### Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `url` | yes | — | Deployed app URL to scan |
| `api-token` | yes | — | Blinkof.ai API token (use a secret) |
| `fail-on` | no | `critical` | Fail on findings at/above this severity: `critical`, `high`, `none` |
| `wait` | no | `true` | Wait for results, or just queue the scan |
| `timeout-minutes` | no | `15` | Max time to wait for the scan |

### Outputs

| Output | Description |
|---|---|
| `scan-id` | The scan id |
| `score` | Health score 0–100 (empty when `wait: false`) |
| `grade` | Letter grade A++–F (empty when `wait: false`) |
| `report-url` | Human-readable report with fix prompts |

The step also writes a summary (score, grade, finding counts, report link) to the
workflow run page.

## Publishing to the GitHub Marketplace (maintainers)

The source of truth for `action.yml` is the Blinkof.ai product repo
(`integrations/github-action/`); changes land there first and are synced here.
Release flow: bump the version tag (`git tag -a v1.x.y && git push --tags`) and
keep the `v1` major tag moving (`git tag -fa v1 && git push -f --tags`).

## More ways to integrate

- REST API: `POST https://blinkof.ai/api/v1/scans` — see [blinkof.ai/auth.md](https://blinkof.ai/auth.md)
- MCP server: `https://blinkof.ai/mcp` (streamable HTTP) — tools `check_agent_ready`,
  `run_blink_test`, `get_scan_report`
- Agent skill: [blinkof.ai/.well-known/agent-skills/index.json](https://blinkof.ai/.well-known/agent-skills/index.json)
