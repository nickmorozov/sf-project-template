# CLAUDE.md

Guidance for Claude Code when working in a project scaffolded from `sf-project-template`.

## What this is

An **org-pipeline** Salesforce DX project: `src/main/default/` metadata deployed through a `dev → qa → uat → main` branch pipeline. Shared tooling comes from the `sf-template` submodule at `.template/` (synced by `sync.js`). CI lives in `.github/workflows/` (Actions cannot run from a submodule).

## Org-neutrality contract — `config/project.config.json`

All project/org specifics live here. Keys: `githubOrg`, `projectName`, `aliasPrefix`, `pipeline`, `packageName`, `namespace`, `devHub`, `slackChannel`, **`deployMode`** (`source` | `package`), `aerNamespace`, `aerSkip`. Bash/CI read values via `node scripts/node/config.js <key>`. After `init.zsh`, set `githubOrg`, `deployMode`, and `slackChannel`.

## Deploy modes

- **`source`** (default): CI deploys metadata via `sfdx-git-delta` deltas (`sf-deploy-source.yml`). Per-env secret: `SFDX_AUTH_URL`.
- **`package`**: CI builds a 2GP version on a Dev Hub and installs it (`sf-deploy-package.yml`). Per-env secrets: `DEVHUB_AUTH_URL`, `ORG_AUTH_URL`.

See `docs/README-CI.md` for the full workflow map and required secrets.

## Common commands

```bash
./init.zsh <org-url-or-alias>   # one-time bootstrap
npm run source:push             # deploy to default org
npm run source:pull             # retrieve
npm run test                    # LWC Jest + Apex
npm run sync:update             # pull latest .template + re-apply
npm run promote:qa              # fast-forward dev → qa (etc.)
```

Script families (scaffold-owned): `package:*`, `ci:package:*`, `org:{auth,create,init,delete,open:*}`, `access:assign:*`, `local:dev:*`, `source:*`, `backmerge:*`. Template-managed scripts arrive via `sync.js`. See `docs/SCRIPTS.md`.

## Conventions

- Branches: `feature/*` (→ dev) or `hotfix/*` (→ any protected). Protected: `dev`, `qa`, `uat`, `main` — derived from `pipeline`. Conventional Commits.
- Configs (`.prettierrc.yml`, `eslint.config.mjs`, `.husky/*`, `jest.config.js`, etc.) are **template-managed** — edit the `.template/` submodule, not project root.
- 4-space indent, single quotes, 180 width, es5 trailing commas, LF. Apex: no final newline.
