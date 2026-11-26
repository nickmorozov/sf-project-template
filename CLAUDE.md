# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Salesforce DX project for **PROJECT_NAME**. Uses source format (API v62.0) with shared tooling from the `.template/` git submodule ([sf-template](https://github.com/enum-dot-dev/sf-template)).

This repo is a **project template**. New projects are created by cloning this repo and running `./init.zsh <org-url-or-name>`, which replaces `PROJECT_NAME` placeholders, initializes the submodule, syncs configs, and authenticates the org.

## Commands

```bash
# Deploy & retrieve
npm run source:push              # Deploy to default org
npm run source:pull              # Retrieve from default org
npm run source:validate          # Dry-run deployment (check-only)
npm run source:diff              # Preview pending deploy + retrieve changes
npm run source:reset             # Reset source tracking (no prompt)

# Quality
npm run lint                     # ESLint on Aura + LWC JS
npm run lint:slds                # SLDS linter on src/
npm run prettier                 # Format all src/ files
npm run prettier:verify          # Check formatting without writing

# Tests
npm run test:unit                # LWC Jest tests
npm run test:unit:watch          # Watch mode
npm run test:unit:coverage       # With coverage report
npx sfdx-lwc-jest -- --testPathPattern="componentName"  # Single component

# Data management (sf-data-manager)
npm run data:export              # Export from SOURCE_ALIAS (default: org-dev)
npm run data:import              # Import to TARGET_ALIAS (default: org-dev)
npm run data:import:sim          # Simulate import (dry-run + verbose)

# Org management
npm run org:list                 # List all authenticated orgs
npm run org:open                 # Open default org in browser

# Template sync
npm run sync:update              # Pull latest .template/ + apply configs + npm install
npm run sync:preview             # Dry-run — show what would change

# Salesforce CLI (direct)
sf org login web --instance-url <url> --alias <alias> --set-default
sf org create scratch -f config/project-scratch-def.json -a <alias>
```

## Architecture

All Salesforce metadata lives under `src/main/default/` in SFDX source format, organized by metadata type:

- **`classes/`** — Apex classes (`.cls` + `.cls-meta.xml`)
- **`triggers/`** — Apex triggers (`.trigger` + `.trigger-meta.xml`)
- **`lwc/`** — Lightning Web Components (each in its own subfolder)
- **`aura/`** — Aura components (each in its own subfolder)
- **`objects/`** — Custom objects and field definitions
- **`permissionsets/`**, **`layouts/`**, **`flexipages/`**, **`tabs/`**, **`applications/`**, **`staticresources/`**, **`contentassets/`** — other metadata types

### Key files

- **`sfdx-project.json`** — SFDX project config; `src` is the single package directory
- **`config/project-scratch-def.json`** — Scratch org definition (Developer edition, Lightning enabled)
- **`.forceignore`** — Excludes `package.xml`, `jsconfig.json`, `.eslintrc.json`, `__tests__/`, TypeScript files, and more from source tracking
- **`.template/`** — Git submodule providing shared configs via `sync.js`:
  - **`.template/sync.js`** — Copies config files verbatim, merges `package.json` (template `devDependencies` and managed scripts win, project-specific extras preserved), cleans legacy files, fixes `.gitignore`
  - **`.template/sf-data-manager/`** — Nested submodule: SFDMU wrapper for data export/import with YAML-driven config

### Template sync behavior (`sync.js`)

- **Copies verbatim**: `.prettierrc.yml`, `.editorconfig`, `.npmrc`, `.prettierignore`, `.stylelintrc.json`, `eslint.config.mjs`, `jest.config.js`, `.husky/pre-commit`
- **Merges `package.json`**: template `devDependencies` versions win; only template-managed scripts are overwritten (project-specific scripts like `open:*`, `package:*` are preserved); `engines` and `lint-staged` overwritten; `name`, `version`, `workspaces`, `dependencies` preserved
- **Deletes legacy**: `.huskyrc`, `.eslintrc`, `.eslintrc.json`, `aura.eslintrc.json`, `.eslintignore`, `lint-staged.config.js`

### Pre-commit hook pipeline

The `.husky/pre-commit` hook runs three steps:

1. **Branch guard** — blocks direct commits to `main`, `dev`, `qa`, `uat`. Bypass: `--no-verify` or `ADMIN_OVERRIDE=1`
2. **Profile cleanup** — strips `<userPermissions>` blocks from staged `.profile-meta.xml` except: `AuthorApex`, `InstallPackaging`, `InboundMigrationToolsUser`, `ManageAuthProviders`, `ModifyAllData`, `ModifyMetadata`
3. **lint-staged** — `prettier --write` on formattable files, `eslint` on Aura/LWC JS, `sfdx-lwc-jest --bail --findRelatedTests` on LWC tests, `sf scanner run` on Apex files

### ESLint flat config (`eslint.config.mjs`)

ESLint v9 flat config with 8 scoped blocks — when adding rules, use the correct block:

| Block | Files | Notes |
|-------|-------|-------|
| Aura JS | `**/aura/**/*.js` | ES5 only — no ES6+ |
| LWC JS | `**/lwc/**/*.js` | Latest ecmaVersion |
| LWC TypeScript | `**/lwc/**/*.ts` | `no-explicit-any` off |
| LWC JS Tests | `**/lwc/**/*.test.js` | `no-unexpected-wire-adapter-usages` off |
| LWC TS Tests | `**/lwc/**/*.test.ts` | TypeScript test rules |
| Jest Mocks | `**/jest-mocks/**/*.js` | Jest globals |
| SLDS HTML | `**/aura/**/*.cmp`, `**/lwc/**/*.html` | BEM + deprecation rules |

## Code Style

- **Prettier**: 4-space indent, single quotes, 180 char print width, trailing commas (es5), LF line endings
- **Apex**: `apexInsertFinalNewline: false` (matches `sf source retrieve` output)
- **JSON/YAML**: 2-space indent (via `.editorconfig`)
- **ESLint**: v9 flat config only — no legacy `.eslintrc` files
- **Stylelint**: `@salesforce-ux/stylelint-plugin-slds` with 15 SLDS-specific CSS rules

### Dependency version constraints

These peer dependency constraints are critical — violating them breaks `npm install`:

- `@salesforce/eslint-plugin-aura@3.0.0` requires `@eslint/js@^9` (NOT v10+)
- `@salesforce-ux/stylelint-plugin-slds@0.5.3` requires `stylelint@^16.10.0` (NOT v17+)
- `@salesforce/eslint-config-lwc@4.x` requires `@lwc/eslint-plugin-lwc@^3.0.0`
- ESLint v9 flat config (`eslint.config.mjs`) is incompatible with legacy `.eslintrc`

## Conventions

- Apex classes go in `src/main/default/classes/`, triggers in `triggers/`
- Each LWC lives in `src/main/default/lwc/<componentName>/` with `.js`, `.html`, `.js-meta.xml`
- Each Aura component in `src/main/default/aura/<componentName>/`
- LWC tests (`__tests__/`) are colocated inside each LWC folder, excluded from deployment via `.forceignore`
- Protected branches: `main`, `dev`, `qa`, `uat` — use `feature/*` or `hotfix/*` branches
- Commit messages: conventional commits (`fix:`, `feat:`, `refactor:`, `chore:`, etc.)
- Do NOT edit synced config files directly — they will be overwritten by `npm run sync:update`. Modify the `.template/` submodule instead.

## Project-Specific Notes

<!-- Add project-specific conventions, custom objects, integrations, etc. -->
