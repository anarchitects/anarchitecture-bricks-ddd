# ADR-0001: Hexagonal Architecture With DDD Boundaries

- Status: Accepted
- Date: 2026-03-28
- Owners: Architecture maintainers
- Context: Nx monorepo organized by bounded context and DDD layers

## Context

The repository already uses a DDD-first structure with bounded contexts and layered libraries. As implementation scale grows, consistency risks increase:

- framework and transport concerns can leak into domain code
- controllers or UI layers can bypass application orchestration
- infrastructure adapters can become coupled to each other directly
- generated or imported code can blur boundaries between domain, contracts, and adapters

We need an explicit architectural decision that keeps DDD intact and clarifies how hexagonal architecture is applied in this monorepo.

## Decision

We adopt hexagonal architecture as the execution model inside each bounded context, with DDD as the modeling strategy.

### Core boundaries

- Inside the hexagon: `domain` and `application`
- Ports: defined in `application` (inbound and outbound)
- Adapters: implemented in `infrastructure-*` and presentation/frontend layers
- Composition roots: facades, bootstrap modules, or app startup wiring

### Dependency direction

- `domain` depends only on language-level and explicitly shared pure TypeScript utilities
- `application` depends on `domain` and declares ports
- `infrastructure-*` depends on `application` and `domain` to implement outbound ports
- `presentation` or frontend feature layers depend on `application` and invoke use cases through inbound ports
- adapter-to-adapter shortcuts are not allowed when they bypass application orchestration

### DDD preservation rules

- domain models business meaning and invariants
- contracts/DTOs model transport/public wire shape
- persistence entities and HTTP/request models must be mapped at boundaries
- no framework, ORM, HTTP DTO, or environment access in `domain`

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

### Trade-offs

- additional mapping code at boundaries (DTO/contract/entity to domain)
- more explicit wiring in composition roots
- slightly higher upfront cost for quick feature spikes

## Implementation Guidance

Use this checklist when adding or changing features:

1. Put business rules and invariants in `domain`.
2. Put use case orchestration and ports in `application`.
3. Put framework/IO integrations in adapters (`infrastructure-*`, presentation/frontend adapters).
4. Add boundary mappers where transport or persistence shapes cross into domain.
5. Wire adapters to ports in facades or other composition roots.

## Compliance Signals

A change is aligned when:

- ports are defined in `application`
- adapters implement those ports in `infrastructure-*`
- controllers/UI do not directly call repositories/SDKs
- domain code has no framework imports

## Related

- [README](../../README.md)
- [AGENTS](../../AGENTS.md)