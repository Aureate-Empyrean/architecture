# Architecture Decisions

Lightweight Architecture Decision Records (ADRs) for important architectural choices that need rationale and history.

No ADRs have been written yet. Existing established decisions are recorded in [architecture.md](../architecture.md), [principles.md](../principles.md) and the module/concept documents. Retroactive ADRs may be added when a decision's rationale is worth preserving or is being challenged.

## When to write an ADR

- The choice constrains multiple modules or repositories.
- It would be expensive to reverse.
- Someone will later ask "why did we do it this way?"

Do not write ADRs for local implementation choices inside one repository.

## Format

File name: `NNNN-short-title.md` (zero-padded, sequential, never renumbered).

```markdown
# NNNN. Title

- Status: Proposed | Accepted | Superseded by NNNN | Rejected
- Date: YYYY-MM-DD

## Context
What forces and constraints make this decision necessary.

## Decision
What was decided, stated plainly.

## Alternatives considered
Brief; why they were not chosen.

## Consequences
What becomes easier, what becomes harder, what is now required of other documents/repos.
```

## Rules

- Accepted ADRs are not edited except for status and links. Change a decision by writing a new ADR that supersedes it.
- When an ADR is accepted, update affected documents so they reference it and stay consistent.

## Index

_(none yet)_
