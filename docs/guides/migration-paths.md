# Migration Paths Guide

- Status: Active guide
- Audience: Maintainers and application teams using the reusable libraries in this repository
- Scope: Migration from modulith-first composition toward microservices and micro-frontends

## Purpose

This guide explains how to evolve applications built with the reusable libraries in this repository.

The default starting point is a modulith. This guide describes how to migrate safely when independent deployment, stronger runtime isolation, or team autonomy later justify extraction.

It focuses on three migration paths:

- backend modulith to microservice extraction
- frontend modulith to micro-frontend extraction
- in-process bounded-context interaction to explicit runtime contracts

## Core Principle

Migrate **topology**, not **domain meaning**.

The bounded context remains the primary unit of design. Migration should preserve:

- the bounded-context identity
- the owned contracts
- the layer responsibilities
- the public package surfaces where possible

The goal is to change runtime composition with the least possible conceptual redesign.

## General Migration Rules

### 1. Validate boundaries before extraction

Do not extract a bounded context just because the system may grow later.

Extract when the bounded context is already sufficiently clear and stable.

### 2. Preserve the shared TS core

Keep `ts/domain` and `ts/contracts` as the conceptual source of truth for the bounded context whenever possible.

### 3. Make runtime boundaries explicit

When a context leaves the modulith, all previously in-process interactions that cross its runtime boundary must become explicit:

- API calls
- event contracts
- messaging contracts
- shell/remote integration boundaries

### 4. Avoid accidental duplication

Do not copy domain logic or transport contracts into multiple extracted services or frontend deployables unless the duplication is an intentional translation boundary.

### 5. Extract one bounded context at a time

Do not attempt a broad topology migration in one move if a narrow extraction can validate the path first.

## Migration Path 1: Backend Modulith To Microservice

## Starting point

- one Nest host composes multiple bounded contexts in-process
- each bounded context already has its own `ts/*` core and `nest/*` delivery layers

## Target

- one or more bounded contexts become separately deployable backend services

## Recommended sequence

### Step 1: confirm the bounded context boundary

Before extraction, confirm:

- what the context owns
- which contracts it exposes
- which dependencies cross the future runtime boundary
- whether those dependencies already reflect the intended domain ownership

### Step 2: identify the composition root to move

In a modulith, the host assembles multiple contexts in one runtime.

For extraction, identify which composition responsibilities move from the modulith host into the new service host.

### Step 3: replace in-process cross-context calls with explicit runtime contracts

Anything that previously crossed the context boundary in-process must become explicit.

Typical transformations:

- application call -> HTTP client call
- internal event -> integration event/message
- shared in-process orchestration -> service-level coordination

### Step 4: preserve context internals

Inside the extracted service, keep the same internal discipline:

- `ts/domain`
- `ts/contracts`
- `nest/application`
- `nest/presentation`
- `nest/infrastructure-*`
- `nest/facade`

The internal architecture should not be rewritten just because the service becomes independently deployable.

### Step 5: shrink the modulith composition

Once the extracted service is live, remove or reduce the old in-process composition in the modulith host.

The modulith host should now treat the extracted context as external.

## Migration checklist: backend extraction

1. Which use cases now cross a runtime boundary?
2. Which contracts become API or event contracts?
3. Which adapters must move into the new service host?
4. Which in-process assumptions must be removed?
5. Which tests must move from in-process integration tests to contract/integration tests?

## Migration Path 2: Frontend Modulith To Micro-Frontend

## Starting point

- one Angular shell composes multiple bounded-context frontend libraries
- bounded contexts are already organized into `ui`, `feature`, `state`, `data-access`, and `facade`

## Target

- one or more bounded contexts are separately deployed and composed as micro-frontends

## Recommended sequence

### Step 1: confirm frontend bounded-context ownership

Before extraction, confirm:

- which routes or screens belong to the context
- which shell dependencies are real and which are accidental
- whether state is already scoped correctly inside the context

### Step 2: isolate shell responsibilities

The shell should own:

- global navigation
n- top-level composition concerns
- cross-cutting app-wide concerns that are intentionally shared

The bounded context should continue to own:

- its feature composition
- its state
- its UI surface
- its transport/integration logic

### Step 3: reduce accidental app-wide state assumptions

A context that is being extracted into a micro-frontend must not depend on accidental app-global state unless that dependency is deliberate and documented.

This is one reason the repository defaults to explicit state scoping instead of implicit root scope.

### Step 4: move route composition outward

In a frontend modulith, route composition typically lives in one Angular shell.

In a micro-frontend topology, shell/remote composition becomes more explicit. Move only the outer composition boundary, not the internal context layering.

### Step 5: preserve context internals

Inside the extracted frontend context, keep the same internal model:

- `angular/ui`
- `angular/feature`
- `angular/state`
- `angular/data-access`
- `angular/facade`

Do not redesign the internals just because the deployment topology changes.

## Migration checklist: frontend extraction

1. Which routes belong to the bounded context?
2. Which shell dependencies are essential?
3. Which state currently leaks through app-wide scope?
4. Which contracts must become explicit at shell boundaries?
5. Can the context still be understood and tested in isolation?

## Migration Path 3: In-Process Interaction To Explicit Runtime Contracts

This migration path often happens as part of backend or frontend extraction, but it is important enough to call out separately.

## Starting point

- bounded contexts interact within one runtime
- some interactions may still rely on in-process assumptions

## Target

- the same interactions become explicit runtime contracts

## Typical changes

### Backend

- direct application call -> HTTP or messaging integration
- in-memory assumptions -> durable integration semantics
- host wiring -> client adapter configuration

### Frontend

- direct shell-level assumptions -> explicit shell contract
- shared app-wide state assumptions -> scoped or bridged integration
- internal route composition assumptions -> shell/remote routing contract

## Contract migration rule

When moving to runtime boundaries:

- identify the owned contract
- make it explicit
- keep ownership with the bounded context
- avoid letting infrastructure details redefine the business contract

## What Usually Needs To Change

When migrating topology, the following usually changes:

- hosts and composition roots
- transport adapters
- runtime configuration
- operational/deployment setup
- integration tests and contract verification

## What Should Usually Not Change

When migrating topology, the following should usually remain stable:

- domain meaning
- bounded-context ownership
- internal layering discipline
- public language of the context
- reusable library model

## Safe Migration Strategy

Use this sequence by default:

1. start in a modulith
2. keep bounded contexts explicit from day one
3. identify a context whose runtime independence is justified
4. extract only that context
5. verify contracts and operational viability
6. repeat only when further extraction is justified

## Warning Signs

Migration is likely premature when:

- context boundaries are still unclear
- extraction is driven mainly by fashion
- the team has not yet validated ownership and contracts
- the modulith is poorly structured and extraction is being used as a rescue plan

Migration is more likely justified when:

- the context already behaves like an independent subsystem
- deployment cadence differs materially
- operational isolation provides real value
- runtime contracts are already conceptually clear

## Migration Checklist

Before approving a migration path, answer:

1. What exact problem does extraction solve?
2. Which bounded context is being extracted?
3. What stays the same conceptually after extraction?
4. Which interactions become runtime contracts?
5. Which state or shell assumptions must be removed?
6. How will the host or shell composition change?
7. Which tests and verification layers need to change?

## Related

- [Guide: Topology Guide: Moduliths, Microservices, and Micro-Frontends](./topology-modulith-microservices-microfrontends.md)
- [ADR-0004: Modulith-First With Extraction Paths](../adr/0004-modulith-first-with-extraction-paths.md)
