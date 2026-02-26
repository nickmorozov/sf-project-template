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
1. Replace placeholders with your project name
2. Initialize the `.template/` submodule
3. Sync configs (ESLint, Prettier, Husky, Jest, etc.)
4. Install dependencies
5. Authenticate your Salesforce org

## Ongoing Updates

```bash
npm run sync:update    # Pull latest template + apply configs
```
