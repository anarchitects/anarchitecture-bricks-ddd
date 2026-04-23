# Topology Guide: Moduliths, Microservices, and Micro-Frontends

- Status: Active guide
- Audience: Maintainers and consumers of the reusable libraries in this repository
- Scope: Application composition across backend and frontend deployment topologies

## Purpose

This repository delivers **DDD reusable libraries** to accelerate application development.

These libraries are intended to support multiple deployment topologies without changing their conceptual identity as bounded contexts.

The supported topologies are:

- **modular monoliths (moduliths)**
- **microservices**
- **micro-frontends**

The purpose of this guide is to explain:

- what stays the same across those topologies
- what changes between those topologies
- which topology is the default starting point
- how composition differs for backend and frontend delivery styles

## Core Position

The primary product of this repository is **reusable bounded-context libraries**, not a single fixed runtime topology.

That means the same bounded context should ideally preserve:

- the same `ts/domain`
- the same `ts/contracts`
- the same layering rules
- the same public package surfaces

What changes between topologies is primarily:

- composition
- deployment
- runtime communication style
- operational complexity

## Default Recommendation

The default recommendation for new systems is:

- **start modulith-first**
- **extract later when justified**

This applies to both backend and frontend unless there is a strong reason to begin distributed from day one.

## Why Modulith-First

A modulith-first approach is the default because it:

- validates bounded-context boundaries before distributing them
- keeps operational complexity lower in early stages
- speeds up delivery and learning
- makes reusable libraries useful immediately
- creates a clearer migration path than premature distribution

## Logical Architecture vs Deployment Architecture

This repository distinguishes between two concerns.

### Logical architecture

Logical architecture is defined by:

- bounded contexts
- `ts/domain`
- `ts/contracts`
- Nest and Angular layer families
- dependency direction
- public package surfaces

Logical architecture should remain stable across topologies.

### Deployment architecture

Deployment architecture decides:

- whether bounded contexts live in one process or many
- whether frontend parts live in one application shell or several deployables
- how contexts communicate at runtime
- how independent deployment and scaling are handled

Deployment architecture may evolve over time.

## Supported Topologies

## 1. Modulith

A modulith uses multiple bounded contexts inside one deployable application per major runtime.

### Backend modulith

Typical example:

- one Nest application composes multiple bounded contexts from `libs/<context>/nest/*`

Characteristics:

- in-process orchestration
- composition through facades and application modules
- lower operational overhead
- easier local development and debugging

### Frontend modulith

Typical example:

- one Angular application composes multiple bounded contexts from `libs/<context>/angular/*`

Characteristics:

- one router tree
- one shell
- lazy-loaded feature entrypoints
- one deployment unit

### When to prefer a modulith

Prefer a modulith when:

- the domain is still being discovered
- teams are still validating context boundaries
- operational simplicity is important
- independent deployment is not yet essential
- development speed matters more than infrastructural independence

## 2. Microservices

A microservice topology deploys one or more bounded contexts as separately deployable backend services.

### Typical shape

- one Nest service per extracted bounded context or service boundary
- shared contracts still come from bounded-context libraries
- composition moves from in-process calls to runtime contracts

### What changes

Compared to a backend modulith:

- composition moves out of process
- service boundaries become runtime boundaries
- communication becomes explicit through API, events, or messaging
- deployment and scaling become independent

### What should remain stable

- the bounded-context identity
- the domain model semantics
- the contract ownership model
- ports/adapters discipline inside each service

### When to prefer extraction

Prefer extraction when one or more of the following become important:

- team autonomy requires independent service delivery
- operational scaling differs strongly by context
- failure isolation becomes valuable
- release cadence differs materially between contexts
- runtime boundaries are already clear and stable

## 3. Micro-Frontends

A micro-frontend topology deploys one or more frontend bounded contexts separately while preserving an intentional shell/composition strategy.

### Typical shape

- one application shell or host
- one or more separately delivered frontend bounded contexts
- integration through route composition, federation, or another chosen delivery mechanism

### What changes

Compared to a frontend modulith:

- deployment is distributed
- composition may happen at runtime
- shell and remote boundaries become explicit
- frontend ownership can become more autonomous

### What should remain stable

- the bounded-context library identity
- the Angular layering model
- the UI/state/feature/data-access responsibilities inside the context
- the contract model used at runtime boundaries

### When to prefer extraction

Prefer micro-frontends when one or more of the following apply:

- multiple teams need independent frontend deployment
- shell/remote separation provides real organizational value
- bounded contexts are stable enough to tolerate runtime integration boundaries
- deployment independence clearly outweighs frontend integration complexity

## What Stays The Same Across Topologies

The following should remain stable regardless of topology.

### Bounded-context ownership

Each bounded context remains the primary unit of ownership.

### Shared TS core

The shared TypeScript core still lives in:

- `ts/domain`
- `ts/contracts`

### Technology-specific layering

Backend and frontend delivery layers still follow their documented standards.

### Public package posture

The repository still provides:

- easy-mode facades
- advanced layer-specific packages

### Architecture rules

The same dependency-direction and boundary rules continue to apply.

## What Changes Across Topologies

The following may change across topologies.

### Composition root

- modulith: composition is mostly in-process
- microservices: composition moves to service boundaries and client integration
- micro-frontends: composition moves to shell/remote boundaries

### Runtime communication

- modulith: in-process calls and shared runtime
- microservices: API/event/message-based communication
- micro-frontends: shell/remote integration and runtime composition

### Operational concerns

- modulith: simpler deployment and operations
- distributed topologies: more operational complexity and stronger platform requirements

## Practical Guidance By Topology

## Backend guidance

### Modulith backend

Recommended default:

- compose multiple bounded contexts in one Nest host
- keep context boundaries explicit through libraries and application ports
- do not flatten the contexts just because they run in one process

### Microservice backend

When extracting:

- preserve the context libraries as the conceptual source of truth
- move service-specific composition into the service host
- make runtime boundaries explicit and contract-owned
- do not duplicate domain meaning across services unless intentionally translated

## Frontend guidance

### Modulith frontend

Recommended default:

- compose bounded contexts in one Angular shell
- use lazy loading and route composition
- keep Angular layering rules inside each context

### Micro-frontend frontend

When extracting:

- preserve the same frontend bounded-context structure
- move shell and remote composition outward
- keep state scoped to the bounded context instead of leaking through the shell
- avoid introducing runtime distribution before ownership and context boundaries are stable

## Anti-Patterns

The following are not the default strategy in this repository:

- starting distributed purely because it sounds more scalable
- treating runtime distribution as a substitute for proper bounded-context design
- flattening bounded contexts inside a modulith until they become one mixed application
- forcing every bounded context into a microservice or micro-frontend regardless of value
- assuming that a microservice or micro-frontend is required for reuse

## Implementation Checklist

Before choosing or documenting a topology, verify:

1. Is the bounded-context model already clear enough?
2. Is independent deployment truly needed now?
3. Would a modulith validate boundaries faster and cheaper?
4. If extracting, which runtime boundaries become explicit?
5. What contracts remain shared and who owns them?
6. Does the chosen topology preserve the reusable-library model instead of bypassing it?

## Related

- [Guide: Migration Paths](./migration-paths.md)
- [ADR-0004: Modulith-First With Extraction Paths](../adr/0004-modulith-first-with-extraction-paths.md)
- [Guide: Angular Layering and Explicit State Scoping](./angular-layering-and-state.md)
