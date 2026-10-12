# Angular Layering and Explicit State Scoping

- Status: Active guide
- Audience: Maintainers and contributors of Angular libraries in this repository
- Scope: `libs/<bounded-context>/angular/*`

## Purpose

This guide defines how Angular libraries in this repository are structured and how state is scoped.

It exists to make the Angular side of the repo:

- DDD-aligned without forcing backend-style hexagonal ceremony into the frontend
- consistent across bounded contexts
- compatible with reusable library publishing
- explicit about where state lives and how far it reaches
- aligned with modern Angular practices built around standalone APIs, signals, OnPush, router-driven composition, and Nx-enforced boundaries

## Angular Architecture Position

Angular in this repository is **DDD-aligned and vertically sliced**, but it is **not modeled as a literal frontend hexagon**.

The Angular side uses a pragmatic layered frontend structure:

- `ui`
- `feature`
- `state`
- `data-access`
- `facade`

This layered model is the standard for reusable Angular libraries in the repo.

## Modern Angular Baseline

Angular implementations in this repository should follow these defaults unless there is a strong reason not to:

- standalone components, directives, and pipes
- `ChangeDetectionStrategy.OnPush`
- signals for local reactive state
- `computed` for derived values
- effects only for real side effects, not for core business logic orchestration
- built-in control flow (`@if`, `@for`, `@switch`)
- provider-based composition
- router-driven feature composition and lazy loading where appropriate

### Forms baseline and experimental APIs

The accepted [Modern Angular ADR](../adr/modern-angular-22-signal-forms.md), A07, supersedes the previous Signal Forms exclusion as of 2026-10-11. Use stable Signal Forms for new forms on an approved Angular 22+ runtime. Preserve supported legacy controls and staged releases until their explicit migration gates are met. Other unapproved experimental APIs remain outside the default baseline.

## Layering Model

### `ui`

Use `ui` for presentational components and directives.

Responsibilities:

- render data passed in by consumers
- emit user interaction events
- remain reusable and composition-friendly
- avoid owning feature orchestration or backend integration concerns

Typical contents:

- cards
- tables
- form field wrappers
- presentational dialogs
- generic visual components

`ui` should not:

- call HTTP APIs directly
- own domain orchestration
- import `data-access`
- become a hidden smart layer

### `feature`

Use `feature` for smart composition.

Responsibilities:

- compose `ui` components
- connect routed screens and flows to state
- bind route data, user actions, and view models together
- coordinate feature-local behavior

Typical contents:

- routed screens
- smart shells
- flow coordinators
- route-aware composition components

`feature` is the main consumer of `state` and `ui`.

### `state`

Use `state` for frontend state and feature-facing orchestration.

Responsibilities:

- hold reactive state
- expose readable signals and computed values
- expose command methods
- coordinate view-facing state transitions
- hide internal implementation details from `feature`

Typical contents:

- state services built with signals
- NgRx Signal Store implementations
- provider helpers for scoping
- state-specific selectors/computed projections

`state` should not:

- render UI
- become a transport adapter in disguise
- leak raw HTTP details to `feature`

### `data-access`

Use `data-access` for transport and integration with backend-facing concerns.

Responsibilities:

- HTTP calls
- request/response mapping
- interceptable integration points
- transport-focused helper services

Typical contents:

- API services
- transport DTO mapping helpers
- HTTP adapter utilities

`data-access` should not:

- own UI orchestration
- become the place where feature state is assembled
- be imported directly by `ui`

### `facade`

Use `facade` for ergonomic public consumption.

Responsibilities:

- provide easy-mode entrypoints
- compose internal Angular layers for consumers
- preserve advanced internal layering instead of erasing it

`facade` is assembly, not a replacement for the layering model.

## Dependency Direction

The standard Angular dependency direction is:

- `ui <- feature -> state -> data-access`

Interpretation:

- `feature` consumes `ui`
- `feature` consumes `state`
- `state` may consume `data-access`
- `ui` must stay independent from `data-access`
- `feature` must not bypass `state` by reaching into `data-access` unless there is a very explicit exception

## State Model Selection

Every Angular bounded-context state library must choose **one** implementation style:

- **service + signals**
- **NgRx Signal Store**

Do **not** maintain dual implementations behind runtime configuration by default.

The variation belongs inside the `state` library choice, not in a runtime strategy switch.

### Default Rule

Use **service + signals** unless the state crosses the escalation threshold.

### Tier 1: Choose Service + Signals

Use service + signals when most of the following are true:

- state is owned by one feature flow or one smart area
- state transitions are few and easy to understand
- state is mostly filters, selections, loading flags, small working data, or simple view-model composition
- there is no substantial entity collection management
- there are few derived values
- there is little or no cross-feature coordination
- there is little need for store lifecycle hooks or advanced debugging

### Tier 2: Either Is Acceptable, But Document Why

You are in the middle zone when several of the following apply:

- state is consumed by multiple smart features or routes
- there are several asynchronous loads or mutations
- there is moderate derived-state complexity
- the state includes one or two meaningful collections
- testability and debugging requirements are increasing

In this zone, document why the chosen implementation style is still the right fit.

### Tier 3: Choose NgRx Signal Store

Use NgRx Signal Store when one or more strong triggers apply, especially in combination:

- state is entity-centric
- there are many mutations or meaningful state transitions
- derived state is rich and widely consumed
- state is shared across multiple smart consumers or routes
- stronger devtools and inspection value is worth the added structure
- hooks/resources fit naturally inside the state layer

## State Escalation Scorecard

Score each category from `0` to `2`.

### Scope breadth

- `0`: single feature or screen
- `1`: a few related smart consumers
- `2`: broad bounded-context frontend state

### Entity complexity

- `0`: little or no collections
- `1`: small collections
- `2`: important collections, maps, or normalization

### Mutation complexity

- `0`: few simple commands
- `1`: moderate orchestration
- `2`: many meaningful transitions or mutations

### Derived state complexity

- `0`: few computed values
- `1`: moderate computed surface
- `2`: rich derived-state model

### Async orchestration

- `0`: one or two simple loads
- `1`: several reads or writes
- `2`: meaningful async coordination

### Debugging and tooling need

- `0`: basic debugging is enough
- `1`: moderate need
- `2`: stronger inspection or devtools value pays off

### Decision Threshold

- `0–4`: use **service + signals**
- `5–8`: either is acceptable, but document the rationale
- `9–12`: use **NgRx Signal Store**
- if **scope breadth = 2** and one other category is also `2`, prefer **NgRx Signal Store**
- if **entity complexity = 2** and one other category is also `2`, prefer **NgRx Signal Store**

## Explicit State Scoping

State in this repository must be **scoped intentionally**.

Do not make domain state global by accident.

### Rule

Do **not** use `providedIn: 'root'` for domain state by default.

Instead, provide state intentionally using provider helpers or explicit provider registration at the correct scope.

### Supported Scopes

#### Component-local scope

Use when state only belongs to a single component instance or a tightly local interaction.

Typical examples:

- local selector state
- view-only toggles
- transient modal state

#### Route-local scope

Use when state belongs to a routed screen or flow and needs a clear provider visibility boundary. Router reuse and cached route injectors mean that navigation away does not guarantee service destruction; test lifetime and provide explicit reset/disposal where needed.

This is the preferred default for most smart feature state.

Typical examples:

- search/filter/result state for one screen
- edit flow state
- wizard state

#### Feature-local scope

Use when multiple smart components inside one feature need to share the same state instance, but the state should still remain bounded to that feature area.

#### App-wide scope

Use only when the state is truly strategic and shared broadly across the frontend surface.

App-wide scope is an exception, not the default.

Typical examples:

- authenticated user session state
- broad shell-level preferences
- cross-route identity or capability state

### Scoping Checklist

Before finalizing a `state` library, answer these questions:

1. Who owns this state?
2. Which components or routes must share it?
3. When should it be created?
4. When should it be destroyed?
5. Would root scope create stale or hidden coupling?

If the answers do not clearly justify app-wide lifetime, do not use root scope.

## Public API Shape Of State Libraries

Regardless of whether a `state` library uses service + signals or NgRx Signal Store internally, its public API should remain stable and feature-friendly.

Prefer exposing:

- readable signals
- computed view state
- command methods
- provider helpers

Avoid exposing:

- unnecessary internal implementation details
- transport-specific abstractions directly to `feature`
- a runtime engine selection surface

## Forbidden Patterns

The following are not the default pattern in this repository:

- dual state implementations selected via runtime configuration
- `providedIn: 'root'` for domain state without strong justification
- `feature` directly calling `data-access` to bypass `state`
- `ui` components owning backend or orchestration logic
- effect-driven business logic chains
- adopting Angular 22 Signal Forms APIs in a legacy runtime before its platform migration gate

## Implementation Checklist

Before merging Angular library changes, verify:

1. The library follows the `ui` / `feature` / `state` / `data-access` / `facade` model where applicable.
2. The chosen state style is documented: `service+signals` or `signal-store`.
3. The chosen state scope is documented: component, route, feature, or app.
4. The state library does not become global by accident.
5. `feature` consumes `state` instead of bypassing it.
6. `ui` remains presentational.
7. `data-access` remains transport-focused.
8. New forms on an approved Angular 22+ runtime use the accepted Signal Forms baseline; existing legacy compatibility and release gates remain satisfied.

## Recommended State Note For Package READMEs

Use a short note like the following in Angular `state` package READMEs:

```md
State style: service+signals
Scope: route-local feature state
Rationale:

- single feature owner
- no entity normalization
- limited derived state
- low mutation complexity

Escalation trigger:

- if multiple smart consumers need shared coherent state
- or if entity collection logic becomes central
```

Or:

```md
State style: signal-store
Scope: bounded-context shared frontend state
Rationale:

- multiple smart consumers
- entity-centric state
- several mutations and derived projections
- stronger inspection/debugging value
```

## Related

- [ADR-0003: Angular Layering And Explicit State Scoping](../adr/0003-angular-layering-and-explicit-state-scoping.md)
- [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](../adr/0002-technology-prefixed-package-families-with-shared-ts-core.md)
- [ADR-0001: Hexagonal Architecture With DDD Boundaries](../adr/0001-hexagonal-architecture-with-ddd-boundaries.md)

## Accepted Angular 22 Forms amendment

The [Modern Angular ADR](../adr/modern-angular-22-signal-forms.md) was explicitly accepted on 2026-10-11. It replaces only the previous Signal Forms exclusion for new work on an approved Angular 22+ runtime. Layers, state scorecard, OnPush and explicit provider scopes stay in force. Read the [Modern Angular Engineering Guide](modern-angular/README.md) for API evidence, migration gates and test expectations.
