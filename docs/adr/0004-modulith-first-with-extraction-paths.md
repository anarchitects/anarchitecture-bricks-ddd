# ADR-0004: Modulith-First With Extraction Paths

- Status: Accepted
- Date: 2026-04-23
- Owners: Architecture maintainers
- Context: The repository provides reusable bounded-context libraries intended to accelerate application development across multiple deployment topologies

## Context

This repository delivers reusable DDD libraries organized by bounded context, technology family, and layer.

Those libraries are intended to support:

- modular monoliths (moduliths)
- microservices
- micro-frontends

Without an explicit architectural decision, contributors can drift into one of two extremes:

- treating the repo as if it were only for a modulith
- over-optimizing all bounded contexts for distributed deployment from day one

We need an explicit decision that clarifies the default starting posture, the supported topologies, and the role of migration guidance.

## Decision

### Primary product posture

The primary product of this repository is **reusable bounded-context libraries**.

The repository is not tied to a single deployment topology. Instead, its libraries are designed to support multiple topologies while preserving bounded-context identity and architecture discipline.

### Supported topologies

The repository supports:

- moduliths
- microservices
- micro-frontends

### Default starting posture

The default recommendation is:

- **start modulith-first**
- **extract later when justified**

This means teams should usually begin by composing bounded contexts within a modulith unless there is a strong reason to start distributed.

### Extraction posture

Extraction toward microservices or micro-frontends is treated as an **intentional evolution path**, not as the default starting assumption.

When extraction happens, the goal is to change deployment topology and runtime composition with the least possible redesign of:

- bounded-context ownership
- domain meaning
- contract ownership
- internal layering discipline

### Logical vs deployment architecture

The repository distinguishes between:

- **logical architecture**: bounded contexts, shared TS core, technology-specific layering, public package posture
- **deployment architecture**: modulith, microservice, micro-frontend, shell/remote or service-host composition

Logical architecture should remain stable as deployment architecture evolves.

## Consequences

### Positive

- the reusable-library model becomes explicit and topology-agnostic
- teams get a clear default recommendation instead of drifting into premature distribution
- migration becomes a supported architectural concern rather than an afterthought
- bounded contexts can be validated in a modulith before taking on distributed complexity
- backend and frontend extraction paths become easier to reason about

### Trade-offs

- contributors must distinguish more carefully between logical architecture and deployment architecture
- extraction requires explicit migration planning rather than naive copy-and-split approaches
- some consumers may want stronger productized guidance for one topology than the repo intentionally provides

## Rules Derived From This Decision

1. The repository should continue to organize reusable libraries by bounded context, technology family, and layer.
2. The same bounded context should be able to participate in multiple deployment topologies without losing its conceptual identity.
3. Modulith composition is the default recommendation for new systems unless a strong reason justifies starting distributed.
4. Extraction to microservices or micro-frontends should preserve bounded-context ownership and contract ownership where possible.
5. Migration guidance is a first-class part of the repo’s documentation surface.
6. Runtime distribution must not be treated as a substitute for good bounded-context design.

## Documentation Implications

The repository should provide guidance in three categories:

- implementation guidance
- topology guidance
- migration guidance

The topology and migration guides document how the same reusable libraries are composed in moduliths, microservices, and micro-frontends, and how to evolve safely between them.

## Related

- [Guide: Topology Guide: Moduliths, Microservices, and Micro-Frontends](../guides/topology-modulith-microservices-microfrontends.md)
- [Guide: Migration Paths](../guides/migration-paths.md)
- [ADR-0003: Angular Layering And Explicit State Scoping](./0003-angular-layering-and-explicit-state-scoping.md)
- [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](./0002-technology-prefixed-package-families-with-shared-ts-core.md)
- [ADR-0001: Hexagonal Architecture With DDD Boundaries](./0001-hexagonal-architecture-with-ddd-boundaries.md)
