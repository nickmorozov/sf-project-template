# PROJECT_NAME

Salesforce DX org-pipeline project, scaffolded from [`sf-project-template`](https://github.com/nickmorozov/sf-project-template).

## Quick start

```bash
gh repo create <org>/<name> --template <org>/sf-project-template --private --clone
cd <name>
./init.zsh <org-url-or-alias>     # substitutes PROJECT_NAME, inits .template, syncs, installs, auths
```

Then in `config/project.config.json` set `githubOrg`, `deployMode` (`source` or `package`), and `slackChannel`, and configure CI secrets (see `docs/README-CI.md`).

## Layout

| Path | Purpose |
| --- | --- |
| `src/main/default/` | SFDX-format metadata |
| `.template/` | shared tooling submodule (`sf-template`) |
| `.github/workflows/` | CI pipeline (validate-pr, deploy-*, backups, claude) |
| `config/` | `project.config.json` contract, scratch def, CI registries |
| `docs/` | `README-CI.md`, `SCRIPTS.md`, `MANUAL.md`, `adr/` |

See `CLAUDE.md` and `docs/` for details.
