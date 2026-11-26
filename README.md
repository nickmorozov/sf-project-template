# PROJECT_NAME

## Quick Start

```bash
# After creating from template:
./init.zsh <org-url-or-name>

# Examples:
./init.zsh acme                                         # production
./init.zsh https://acme--dev.sandbox.my.salesforce.com  # sandbox
./init.zsh acme--dev                                    # sandbox shorthand
```

The init script will:
1. Replace `PROJECT_NAME` placeholders with your project name
2. Initialize the `.template/` submodule (and nested `sf-data-manager`)
3. Sync configs (ESLint, Prettier, Stylelint, Husky, Jest, etc.)
4. Install dependencies
5. Authenticate your Salesforce org

## Development

```bash
npm run source:push              # Deploy to default org
npm run source:pull              # Retrieve from default org
npm run source:validate          # Dry-run deployment
npm run source:diff              # Preview pending changes
npm run lint                     # ESLint on Aura + LWC JS
npm run lint:slds                # SLDS linter
npm run prettier                 # Format src/ files
npm run test:unit                # LWC Jest tests
npm run test:unit:coverage       # With coverage
```

## Data Management

Export and import Salesforce data via [sf-data-manager](https://github.com/enum-dot-dev/sf-data-manager) (SFDMU wrapper). Configure objects in `config/<name>.yaml`.

```bash
npm run data:export              # Export from SOURCE_ALIAS (default: org-dev)
npm run data:import              # Import to TARGET_ALIAS (default: org-dev)
npm run data:import:sim          # Simulate import (dry-run)
```

## Ongoing Updates

```bash
npm run sync:update              # Pull latest template + apply configs
npm run sync:preview             # Preview changes without applying
```
