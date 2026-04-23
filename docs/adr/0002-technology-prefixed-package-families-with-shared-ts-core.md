# ADR-0002: Technology-Prefixed Package Families With A Shared TS Core

- Status: Accepted
- Date: 2026-04-23
- Owners: Architecture maintainers
- Context: Bounded contexts in this repository are expected to grow into multiple reusable technology families over time

## Context

The repository already distinguishes between shared business concerns and technology-specific delivery concerns, but the original package taxonomy still mixed two naming models:

- root-level `domain` and `contracts`
- technology-prefixed package families such as `nest/*` and `angular/*`

That is workable for a single dominant backend technology, but it becomes ambiguous once multiple backend/runtime families are expected to coexist in the same bounded context.

Examples of the intended future direction include:

- `nest/application`, `nest/presentation`, `nest/infrastructure-*`, `nest/facade`
- `angular/data-access`, `angular/state`, `angular/feature`, `angular/ui`, `angular/facade`
- future `rails/application`, `rails/presentation`, `rails/infrastructure-*`, `rails/facade`

Without a naming decision, contributors can reasonably ask whether:

- `application` is global or technology-specific
- `domain` and `contracts` are special cases or just historical leftovers
- future technology families should duplicate or reinterpret the existing structure

We need a package taxonomy that makes the shared core explicit while preserving clear parallelism between technology families.

## Decision

We adopt the following package taxonomy for bounded contexts:

- `ts/domain` for the shared pure TypeScript domain model
- `ts/contracts` for shared TypeScript transport and public-contract packages
- technology-prefixed package families for delivery, application, infrastructure, and facade layers

Representative structure:

```text
libs/<bounded-context>/
  ts/
    domain/
    contracts/
  nest/
    application/
    presentation/
    infrastructure-*/
    facade/
  angular/
    data-access/
    state/
    feature/
    ui/
    facade/
  rails/
    application/
    presentation/
    infrastructure-*/
    facade/
```

## Rationale

### Why `ts/domain`

The domain model is shared across multiple TypeScript-based consumers and must remain framework-free. Putting it under `ts/domain` makes that explicit:

- it is language-level, not framework-owned
- it is reusable by multiple TypeScript technology families
- it avoids implying that the domain is “outside” the package taxonomy while other layers are inside it

### Why `ts/contracts`

Contracts are public wire and transport shapes, not the business model itself. Keeping them under `ts/contracts` communicates that they are:

- shared TypeScript-first packages
- distinct from the domain model
- reusable across Nest, Angular, future Rails integration boundaries, tooling, and tests where applicable

### Why technology-prefixed application packages

`application` remains technology-prefixed because the repository intends to support multiple reusable package families. The application layer is still part of the hexagonal core, but packaging it under a technology family:

- preserves parallel structure across Nest, Rails, and other backend families
- keeps package discovery and publishing consistent
- avoids collisions between multiple application package implementations in the same bounded context

This means `nest/application` is not “less core”; it is the Nest-packaged application layer for that bounded context.

## Consequences

### Positive

- one consistent naming model across the repo
- clear separation between the shared TS core and technology-specific families
- easier onboarding for future technology families such as Rails
- clearer package publishing and consumption story
- less ambiguity in docs, imports, and architecture discussions

### Trade-offs

- some additional path verbosity
- migration work in docs, examples, generators, and package naming
- contributors must understand that application can still be inside the hexagon even when technology-prefixed

## Rules Derived From This Decision

1. `ts/domain` remains pure and framework-free.
2. `ts/contracts` must not become the domain model.
3. Technology-prefixed `application` packages may depend on `ts/domain` and, where needed, `ts/contracts`.
4. Technology-prefixed presentation and infrastructure packages must depend inward on application/core packages.
5. Facade packages remain technology-prefixed and are outside the hexagon as composition helpers.

## Migration Guidance

When updating or creating bounded contexts:

1. Prefer `ts/domain` over root-level `domain`.
2. Prefer `ts/contracts` over root-level `contracts`.
3. Keep backend/runtime-specific `application`, `presentation`, `infrastructure-*`, and `facade` packages under their technology family.
4. Update docs, generators, release groups, tags, and examples to match the package taxonomy.
5. Preserve the hexagonal dependency rules while migrating names.

## Related

- [README](../../README.md)
- [AGENTS](../../AGENTS.md)
- [ADR-0001: Hexagonal Architecture With DDD Boundaries](./0001-hexagonal-architecture-with-ddd-boundaries.md)
