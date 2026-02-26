# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Salesforce DX project for **PROJECT_NAME**. Uses shared tooling from the `.template/` submodule.

## Commands

```bash
npm run sync:update          # Pull latest template configs + apply
npm run source:push          # Deploy to default org
npm run source:pull          # Retrieve from default org
npm run source:validate      # Dry-run deployment
npm run lint                 # ESLint on Aura + LWC JS
npm run prettier             # Format all src/ files
npm run test:unit            # LWC Jest tests
npm run test:unit:coverage   # Jest with coverage report
```

## Project-Specific Notes

<!-- Add project-specific conventions, custom objects, integrations, etc. -->
