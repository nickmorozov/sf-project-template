# CI/CD Pipeline

GitHub Actions workflows live in `.github/workflows/` (they cannot run from the `.template/` submodule). The pipeline is **config-driven**: `config/project.config.json` `deployMode` selects the deploy engine.

## Pipeline

Default branch flow (the `pipeline` array in `config/project.config.json`): `dev → qa → uat → main`.

| Branch | Workflow | Source mode | Package mode |
| --- | --- | --- | --- |
| any PR → dev/qa/uat/main | `validate-pr.yml` | LWC Jest + aer + delta dry-run | same |
| push `dev` | `deploy-dev.yml` | delta deploy to DEV | beta build (canary, no install) |
| push `qa` | `deploy-qa.yml` | delta deploy to QA | beta build + install QA |
| push `uat` | `deploy-uat.yml` | delta deploy to UAT | beta build + install UAT |
| push `main` | `deploy-prod.yml` | delta deploy to PROD | release build + promote + install PROD |

After a successful prod deploy, `deploy-prod.yml` forward-merges `main → qa → dev`.

Reusable engines: `sf-deploy-source.yml` (sfdx-git-delta), `sf-deploy-package.yml` (2GP). Reusable gates/notifiers: `admin-guard.yml`, `slack-notify.yml`.

Add-ons: `code-analyzer.yml` (PR static analysis), `stale-branches.yml` (weekday cron), `sf-backup.yml` + `backup-sandboxes.yml` (weekday cron sandbox backups), `claude.yml` + `claude-code-review.yml`.

## Required secrets & variables

Set **repository** secrets:

| Secret | Used by | Notes |
| --- | --- | --- |
| `AER_LICENSE_KEY` | `validate-pr` | octoberswimmer aer license (Apex tests without an org) |
| `SLACK_BOT_TOKEN` | `slack-notify` | Slack app bot token (`chat:write`, `pins:read/write`, `channels:history`) |
| `SLACK_CHANNEL_ID` | `slack-notify` | target channel ID |
| `CLAUDE_CODE_OAUTH_TOKEN` | `claude*` | Claude Code GitHub app token |

Set **per-environment** secrets (GitHub → Settings → Environments → `dev`/`qa`/`uat`/`prod`):

| Secret | Mode | Notes |
| --- | --- | --- |
| `SFDX_AUTH_URL` | source | sfdx auth URL for that env's org |
| `DEVHUB_AUTH_URL` | package | sfdx auth URL for the Dev Hub (same value across envs) |
| `ORG_AUTH_URL` | package | sfdx auth URL for that env's target org |

Generate an auth URL: `sf org display --verbose --target-org <alias>` → copy `Sfdx Auth Url`.

Optional repository/environment **variables** (flags): `SKIP_DEPLOY`, `SKIP_DATA`, `SKIP_NOTIFY`, `SKIP_VALIDATION`, `FULL_DEPLOY`, `FULL_DATA`, `DELETE_OLD_DATA`, `SF_WAIT_TIMEOUT`.

## Config files

- `config/repo-admins.json` — GitHub logins allowed to push directly to protected branches (admin-guard) and tagged for review (slack-notify). Seed it with your team.
- `config/slack-users.json` — GitHub login → Slack member ID map (so notifications @-mention the right person).

## Branch protection

Require `validate-pr` to pass before merging into `dev`/`qa`/`uat`/`main`. The `admin-guard` job emulates protection on direct pushes (allows repo-admins or merged-PR commits).
