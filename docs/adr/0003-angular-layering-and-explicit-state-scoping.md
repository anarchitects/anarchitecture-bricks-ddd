# ADR-0003: Angular Layering And Explicit State Scoping

- Status: Accepted
- Date: 2026-04-23
- Owners: Architecture maintainers
- Context: Angular libraries in this repository need a stable frontend structure and an explicit policy for state lifetime and state implementation style

## Context

The repository already distinguishes Angular layers such as `ui`, `feature`, `state`, `data-access`, and `facade`. However, these layers and their dependency direction need a more explicit architectural decision for three reasons:

- the frontend should remain aligned with DDD and bounded contexts without importing backend-style hexagonal ceremony wholesale
- state tends to become global by accident unless lifetime and scope are decided deliberately
- contributors need an explicit rule for choosing between lightweight service-and-signals state and NgRx Signal Store

We also want the Angular side of the repo to stay aligned with modern Angular practices centered on standalone APIs, signals, OnPush, router-driven composition, and reusable libraries, while excluding experimental APIs from the default baseline for now.

## Decision

### Angular layering model

Angular bounded-context libraries in this repository use the following layered frontend model:

- `ui`
- `feature`
- `state`
- `data-access`
- `facade`

This model is DDD-aligned and vertically sliced, but it is not intended as a literal frontend hexagon.

### Dependency direction

The standard dependency direction is:

- `ui <- feature -> state -> data-access`

Interpretation:

- `feature` composes `ui`
- `feature` consumes `state`
- `state` may use `data-access`
- `ui` must remain independent from transport and backend integration concerns
- `feature` should not bypass `state` by reaching directly into `data-access` unless there is a very explicit exception

### Angular implementation baseline

Angular libraries in the repository should default to:

- standalone APIs
- `ChangeDetectionStrategy.OnPush`
- signals for local reactive state
- `computed` for derived state
- provider-based composition
- router-driven composition and lazy loading where appropriate

Experimental APIs are not part of the default baseline for now.

Specifically, Signal Forms are out of scope until adopted by a later explicit decision.

### State implementation rule

Each Angular `state` library chooses exactly one implementation style:

- **service + signals**
- **NgRx Signal Store**

The repository does not use dual state implementations behind runtime configuration by default.

### Default state choice

Use **service + signals** unless the state crosses the escalation threshold.

### Escalation to NgRx Signal Store

Escalate to **NgRx Signal Store** when state becomes meaningfully more complex, especially when one or more of the following apply:

- entity-centric state becomes important
- there are many meaningful mutations or state transitions
- derived state is rich and widely consumed
- multiple smart consumers or routes need a coherent shared state surface
- stronger inspection and debugging value justifies the added structure

A practical scorecard and detailed thresholds are maintained in the Angular guide.

### Explicit state scoping

State must be scoped intentionally.

Domain state must not become global by accident.

Default rule:

- do **not** use `providedIn: 'root'` for domain state by default

Instead, provide state intentionally at the appropriate scope:

- component-local
- route-local
- feature-local
- app-wide only when clearly justified

Route-local scope is the preferred default for many smart feature states because it gives clear ownership and automatic cleanup when the user leaves the route.

## Consequences

### Positive

- Angular layering becomes explicit and consistent across bounded contexts
- the frontend remains aligned with DDD and vertical slicing without overengineering
- state lifetime is decided intentionally instead of drifting into implicit global scope
- contributors get a clear escalation path from service + signals to Signal Store
- reusable Angular libraries remain easier to understand and compose

### Trade-offs

- contributors must consciously choose and document state style and scope
- some state libraries that are currently ambiguous may need refactoring
- avoiding root scope by default can require additional provider helpers and composition work

## Rules Derived From This Decision

1. Angular libraries should preserve the `ui` / `feature` / `state` / `data-access` / `facade` model where applicable.
2. `ui` stays presentational.
3. `feature` is the main smart composition layer.
4. `state` owns frontend state and feature-facing orchestration.
5. `data-access` owns transport and backend-facing integration concerns.
6. `feature` should not bypass `state` to reach into `data-access` by default.
7. State must be provided intentionally at the correct scope.
8. `providedIn: 'root'` is not the default for domain state.
9. Each state library chooses one implementation style, not a runtime-configurable dual implementation model.
10. Signal Forms are not part of the default Angular baseline in this repository for now.

## Implementation Notes

The detailed operational guidance lives in:

- `docs/guides/angular-layering-and-state.md`

That guide defines the practical responsibilities of each layer, the state escalation scorecard, and the state scoping checklist used in day-to-day implementation work.

## Related

- [Guide: Angular Layering and Explicit State Scoping](../guides/angular-layering-and-state.md)
- [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](./0002-technology-prefixed-package-families-with-shared-ts-core.md)
- [ADR-0001: Hexagonal Architecture With DDD Boundaries](./0001-hexagonal-architecture-with-ddd-boundaries.md)
