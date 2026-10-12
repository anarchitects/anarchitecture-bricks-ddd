# Repository application: anarchitecture-bricks-ddd

Reviewed 2026-10-11 against branch `docs/adr-modern-angular-22`, starting commit `ac5afa9b43b9ae68734fec6201dc576b2da5689f`. This inventory complements the [common guide](README.md); it does not replace reading every ADR in full. Re-enumerate ADRs when the branch changes.

## Actual baseline and scope

Nx and @nx/angular 22.6.3; Yarn 4.13.0; no root @angular/core runtime declaration. Nx 22 is not proof of Angular 22.

Preserve bounded-context/technology/layer structure, framework-free TS domain/contracts, technology-specific application ports and infrastructure adapters. Angular uses ui/feature/state/data-access conventions, not a forced frontend hexagon. Never give stateful domain stores implicit root provisioning.

Use the accepted service-with-signals versus NgRx scorecard: totals 0–4 favor a service; 5–8 need rationale; 9–12 favor a store, with the documented shared-scope/entity overrides. Preserve one state owner. Respect no apps/ additions and use established library/example locations. The accepted Signal Forms amendment below changes the new-work default only on an approved Angular 22+ runtime.

**Accepted scoped supersession (2026-10-11):** A07 supersedes only the Signal Forms exclusion in ADR-0003's modern Angular baseline and decision-summary item 10, for new work on an approved Angular 22+ runtime. It does not supersede its layers, OnPush, state scorecard, explicit provider scopes or exclusion of other unapproved experimental APIs. The companion layering guide records this accepted amendment. Existing runtime upgrades and compatibility releases still require their normal implementation/release gates.

## Complete local ADR inventory

All 6 records below were read. Status labels are preserved; a proposed amendment within an accepted record does not automatically become accepted.

| Record                                                                                                                                               | Status at review                          | Constraint on this guide                                                                                                                              |
| ---------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| [ADR-0001: Hexagonal Architecture With DDD Boundaries](../../adr/0001-hexagonal-architecture-with-ddd-boundaries.md)                                 | Accepted                                  | Domain stays pure; inward dependency direction and bounded-context contracts remain binding.                                                          |
| [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](../../adr/0002-technology-prefixed-package-families-with-shared-ts-core.md)   | Accepted                                  | Technology-prefixed package families share framework-free TS core/contracts.                                                                          |
| [ADR-0003: Angular Layering And Explicit State Scoping](../../adr/0003-angular-layering-and-explicit-state-scoping.md)                               | Accepted                                  | Preserve frontend layers, state scorecard and explicit scopes; only the named Forms exclusion is superseded by the accepted Modern Angular amendment. |
| [ADR-0004: Modulith-First With Extraction Paths](../../adr/0004-modulith-first-with-extraction-paths.md)                                             | Accepted                                  | Default to modulith composition with deliberate later extraction seams.                                                                               |
| [ADR-0005: Align With anarchitecture-bricks-3tier And Accept 3-Tier Migration](../../adr/0005-align-with-bricks-3tier-and-accept-3tier-migration.md) | Accepted                                  | Support 3-Tier migration without erasing the distinct DDD implementation style.                                                                       |
| [ADR: Modern Angular 22 and Signal Forms](../../adr/modern-angular-22-signal-forms.md)                                                               | Accepted (maintainer approval 2026-10-11) | Accepted on 2026-10-11; adds source/API traceability and preserves local release gates.                                                               |

## Application gates

Apply A01–A12 only to the relevant Angular implementation/consumer scope. The [traceability matrix](traceability.md) covers the source chapters independently of whether this repository currently implements a topic. “Not currently applicable” is a scope decision, not permission to skip reading local ADRs.

Before implementation, inspect package-level peers and exports, applicable agent instructions, existing tests and resolved build targets. Before a release, validate the consumer and documented support range; a root dependency alone does not establish every package's compatibility. This change modifies documentation and agent instructions only. No runtime manifest, lockfile, public package API or release artifact is changed.

Read the existing [Angular layering and state guide](../angular-layering-and-state.md) together with this guide; its acceptance gates and scorecard remain authoritative.
