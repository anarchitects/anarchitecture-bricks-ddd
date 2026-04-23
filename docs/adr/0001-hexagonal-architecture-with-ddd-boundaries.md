# ADR-0001: Hexagonal Architecture With DDD Boundaries

- Status: Accepted
- Date: 2026-03-28
- Owners: Architecture maintainers
- Context: Nx monorepo organized by bounded context, technology family, and DDD layers

## Context

The repository already uses a DDD-first structure with bounded contexts and layered libraries. As implementation scale grows, consistency risks increase:

- framework and transport concerns can leak into domain code
- controllers or UI layers can bypass application orchestration
- infrastructure adapters can become coupled to each other directly
- generated or imported code can blur boundaries between domain, contracts, and adapters
- multiple technology families can create naming ambiguity if the shared core and technology-specific layers are not clearly separated

We need an explicit architectural decision that keeps DDD intact and clarifies how hexagonal architecture is applied in this monorepo.

## Decision

We adopt hexagonal architecture as the execution model inside each bounded context, with DDD as the modeling strategy.

### Core boundaries

- Inside the hexagon: `ts/domain` and technology-specific `application` packages
- Shared public contracts: `ts/contracts`
- Ports: defined in technology-specific `application` packages (inbound and outbound)
- Adapters: implemented in `infrastructure-*` and presentation/frontend layers
- Composition roots: facades, bootstrap modules, or app startup wiring

### Dependency direction

- `ts/domain` depends only on language-level and explicitly shared pure TypeScript utilities
- technology-specific `application` depends on `ts/domain` and declares ports
- `infrastructure-*` depends on `application` and `ts/domain` to implement outbound ports
- `presentation` or frontend feature layers depend on `application` and invoke use cases through inbound ports
- adapter-to-adapter shortcuts are not allowed when they bypass application orchestration

### DDD preservation rules

- `ts/domain` models business meaning and invariants
- `ts/contracts` models transport/public wire shape
- persistence entities and HTTP/request models must be mapped at boundaries
- no framework, ORM, HTTP DTO, or environment access in `ts/domain`

### Public API posture

- facade packages remain the easy-mode entry point
- layer packages remain available for advanced composition and overrides
- third-party engines stay internal adapter implementations, not the primary product API

## Consequences

### Positive

- stronger enforcement of dependency direction
- predictable integration seams through ports and adapters
- safer replacement of infrastructure engines
- clearer testing strategy by layer and boundary
- clearer separation between the shared TypeScript core and technology-specific package families

### Trade-offs

- additional mapping code at boundaries (DTO/contract/entity to domain)
- more explicit wiring in composition roots
- slightly higher upfront cost for quick feature spikes

## Implementation Guidance

Use this checklist when adding or changing features:

1. Put business rules and invariants in `ts/domain`.
2. Put use case orchestration and ports in technology-specific `application` packages.
3. Put framework/IO integrations in adapters (`infrastructure-*`, presentation/frontend adapters).
4. Add boundary mappers where transport or persistence shapes cross into domain.
5. Wire adapters to ports in facades or other composition roots.

## Compliance Signals

A change is aligned when:

- ports are defined in technology-specific `application` packages
- adapters implement those ports in `infrastructure-*`
- controllers/UI do not directly call repositories/SDKs
- `ts/domain` code has no framework imports

## Related

- [README](../../README.md)
- [AGENTS](../../AGENTS.md)
- [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](./0002-technology-prefixed-package-families-with-shared-ts-core.md)
