# ADR-0001 — Record architecture decisions

**Status:** Accepted
**Date:** _set on first use_

## Context

We need to record the architectural decisions made on this project — the significant ones that shape structure, dependencies, interfaces, or business constraints — so that current and future contributors understand not just *what* was decided but *why*.

## Decision

We will use Architecture Decision Records, as described by Michael Nygard, stored as Markdown files in `docs/adr/`, named `NNNN-kebab-title.md` (zero-padded, sequential). Each ADR has the sections: Context, Decision, Status, Consequences. The `README.md` in this directory is the index.

## Status

Accepted.

## Consequences

- Decisions are discoverable and reviewable in version control alongside the code.
- Superseding a decision means adding a new ADR and updating the old one's Status to `Superseded by ADR-NNNN` — history is preserved, not rewritten.
- The index in `README.md` must be kept in step with the files on disk.
