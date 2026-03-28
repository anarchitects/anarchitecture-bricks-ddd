---
name: ddd-domain-purity
description: Protect the pure TypeScript domain layer in the DDD repo. USE WHEN editing domain libraries, reviewing whether code belongs in domain, moving rules out of infrastructure or presentation, or checking that ORM/HTTP/framework concerns have not leaked into domain.
---

# DDD Domain Purity

Use this skill whenever a change touches a `domain` library.

The domain sits at the center of the hexagon. It must not know that adapters, frameworks, or delivery mechanisms exist.

## Domain Layer Rules

Allowed in domain:

- entities
- value objects
- aggregates
- domain services
- domain events
- invariants
- specifications

Forbidden in domain:

- Nest or Angular imports
- TypeORM decorators or repositories
- HTTP DTOs and route schemas
- persistence adapters
- environment/config reads
- transport serialization concerns

## Decision Rule

Ask:

1. Is this business meaning or transport/persistence/framework mechanics?
2. Would this code still make sense if Nest, Angular, TypeORM, and HTTP disappeared?
3. Does this represent an invariant or business rule, rather than a delivery concern?

If the answer is no, it does not belong in `domain`.

Hexagonal rule:

- the domain may define business policies and abstractions
- the domain must never import primary or secondary adapters
- framework-specific implementations belong outside the hexagon

## Common Refactors

- Move DTO validation schemas to `contracts` or `ts`.
- Move repositories and adapters to `infrastructure-*`.
- Move orchestration and transactions to `application`.
- Move controller/view concerns to `presentation` or Angular layers.

## Review Output

When reviewing or changing domain code, explicitly call out:

- any framework leakage
- any hidden transport coupling
- any missing value object or invariant that should live in domain
