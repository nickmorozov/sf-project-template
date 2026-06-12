# npm Scripts

Cheat-sheet of the common scripts. Template-managed scripts (lint, test, prettier, source:push/pull, data:*, promote:*, sync:*) are provided by `sf-template` via `sync.js`; the families below are scaffold-owned.

## Source

`npm run source:push` · `npm run source:pull` · `npm run source:diff`
`npm run source:push:force` · `npm run source:pull:force`
`npm run source:sync` · `npm run source:sync:force`
`npm run source:compile` · `npm run source:validate`

## Package (2GP — deployMode: package)

`npm run package:build:beta` · `npm run package:build` (release)
`npm run package:promote`
`npm run package:install:qa` · `:uat` · `:prod` · `:all`
`npm run package:deploy:patch` · `:minor` · `:major`

## Org

`npm run org:auth` · `npm run org:create` · `npm run org:init` · `npm run org:delete`
`npm run org:open` (scratch) · `:dev` · `:qa` · `:uat` · `:prod`
`npm run access:assign:user` · `:admin` · `:all`
`npm run debug:toggle`
`npm run local:dev:app` · `:site` · `:cmp`

## Pipeline

`npm run promote:dev` · `:qa` · `:uat` · `:main`
`npm run backmerge:from-qa` · `:from-uat` · `:from-main`

## Data (SFDMU)

`npm run data:export` · `npm run data:import` · `npm run data:import:sim`

## Tests

`npm test` (lwc + apex) · `npm run test:all`
`npm run test:lwc` · `:watch` · `:coverage`
`npm run test:apex` · `:suite` · `npm run test:local`

## Maintenance

`npm run sync:update` · `npm run update:latest:check` · `:apply`
`npm run org:config:log:schedule` (`:qa` · `:uat` · `:prod`)
