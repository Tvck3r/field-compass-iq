# Architecture Decision Records (ADRs)

This directory contains Architecture Decision Records (ADRs) for the Field-Compass-IQ project. ADRs document important architectural decisions made during the project's development.

## What is an ADR?

An Architecture Decision Record (ADR) is a document that captures an important architectural decision made along with its context and consequences. ADRs help teams understand the reasoning behind architectural choices and provide a historical record of decisions.

## ADR Format

Each ADR follows this structure:

```markdown
# ADR-XXXX: [Title of Decision]

**Status:** [Proposed | Accepted | Deprecated | Superseded by ADR-YYYY]
**Date:** YYYY-MM-DD
**Deciders:** [List of people involved in the decision]

## Context

What is the issue that we're seeing that is motivating this decision or change?

## Decision

What is the change that we're proposing or have agreed to implement?

## Consequences

What becomes easier or more difficult to do and any risks introduced by this change?

### Positive
- List positive consequences

### Negative
- List negative consequences

### Neutral
- List neutral consequences

## Alternatives Considered

What other options were considered and why were they not chosen?

## Implementation Notes

Any specific implementation details or considerations.
```

## ADR Numbering

ADRs are numbered sequentially starting from 0001. Use four digits with leading zeros (e.g., ADR-0001, ADR-0002).

## Current ADRs

| ADR | Title | Status | Date |
|-----|-------|--------|------|
| [0001](./0001-database-per-tenant.md) | Database-per-Tenant Architecture | Accepted | 2025-06-21 |
| [0002](./0002-multi-cloud-terraform.md) | Multi-Cloud Infrastructure with Terraform | Accepted | 2025-06-21 |
| [0003](./0003-cqrs-mediatr-pattern.md) | CQRS with MediatR Pattern | Accepted | 2025-06-21 |
| [0004](./0004-api-versioning-strategy.md) | API Versioning Strategy | Accepted | 2025-06-21 |
| [0005](./0005-frontend-theming-architecture.md) | Dynamic Frontend Theming Architecture | Accepted | 2025-06-21 |

## Creating a New ADR

1. Copy the template from `template.md`
2. Number it sequentially (next available number)
3. Fill in all sections
4. Create a pull request for review
5. Update this README with the new ADR entry

## ADR Lifecycle

- **Proposed**: ADR is draft and under discussion
- **Accepted**: ADR is approved and should be implemented
- **Deprecated**: ADR is no longer relevant but kept for historical reasons
- **Superseded**: ADR has been replaced by a newer ADR (reference the new one)
