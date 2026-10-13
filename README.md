# Anarchitecture Bricks - DDD

Modular, reusable libraries and showcase applications for scalable software architectures, with a Domain-Driven Design first repository structure.

## Purpose

- Publishable domain libraries in `libs/` organised by bounded context, technology family, and layer.
- A shared TypeScript core at the center of each bounded context, expressed through `ts/domain` and `ts/contracts`.
- Explicit application, infrastructure, and presentation/frontend adapter layers around the core model.
- Public facade packages for easy consumption, with deeper layer packages available for advanced composition.
- Shared HTTP contracts owned by `anarchitecture-contracts`, with Zod-first schemas, operation definitions and published OpenAPI 3.1.
- Shared transport and public-contract packages separated from domain model packages.
- Nx-managed example applications in `examples/` for integration and contract validation.

## Companion Repo Position

This repository is the DDD companion to:

- `anarchitects/anarchitecture-bricks-3tier`

The two repos should align on domain intent and public capability surface, while differing intentionally in internal architectural style.

## Purpose Of The Package Taxonomy

Technology is part of the package naming on purpose.

This repository is intended to support parallel reusable package families over time, for example:

- `nest/*`
- `angular/*`
- future `rails/*`
- future technology-specific adapter families where needed

To make that scalable without losing the shared core, the repository distinguishes between:

- `ts/*` for reusable TypeScript-first core packages
- technology-prefixed package families for delivery and adapter concerns

That means the same bounded context can expose a stable shared TS core while also providing framework-specific application, presentation, infrastructure, and facade packages.

## Core DDD Position

This repository structure treats the domain model as the center of the architecture.

For each bounded context:

- `ts/domain` contains pure business model code
- `ts/contracts` contains local transport adapters and compatibility exports consuming canonical shared HTTP contracts
- `application` contains use cases, orchestration, and ports
- `infrastructure` contains adapters to persistence, engines, mail, external systems
- `presentation` or frontend adapter layers expose the product surface
- facade packages provide an easy-mode entrypoint over those internals

The shared core is pure TypeScript:

- no Angular or Nest dependencies
- no TypeORM decorators
- no HTTP DTO concerns in `ts/domain`
- no environment access
- no transport framework assumptions

## Hexagonal Architecture Position

This repository follows hexagonal architecture inside each bounded context, with DDD as the modeling strategy.

- **Core (inside the hexagon):** `ts/domain` + application packages
- **Ports (owned by application):** inbound and outbound interfaces in technology-specific application packages
- **Adapters (outside the hexagon):** `infrastructure-*` and `presentation`/frontend adapters
- **Composition roots:** wire ports to adapters in facades, module bootstrap, or app startup boundaries

### Non-negotiable dependency direction

- `ts/domain` depends on no framework/runtime infrastructure concerns
- technology-specific `application` packages depend on `ts/domain` and may depend on `ts/contracts` only where explicit public-boundary mapping requires it
- `infrastructure-*` implements application ports
- `presentation` invokes application use cases via inbound ports
- adapter-to-adapter coupling is forbidden unless done via application contracts

### Implementation checklist (every change)

1. Put business rules, invariants, policies, and aggregate behavior in `ts/domain`.
2. Put orchestration and ports in technology-specific `application` packages.
3. Put external I/O in adapters (`infrastructure-*`, `presentation`, frontend data-access).
4. Map contracts/DTOs at boundaries; do not leak transport or persistence shapes into `ts/domain`.
5. Wire dependencies only in composition roots and facades.
6. Keep third-party SDKs behind adapter implementations.

## Source of Truth

1. Domain model and business rules: `libs/<bounded-context>/ts/domain`.
2. Shared public HTTP operations and wire schemas: [`anarchitecture-contracts`](https://github.com/anarchitects/anarchitecture-contracts), authored as Zod schemas plus first-class operation definitions. Deterministic OpenAPI 3.1 is its published artifact.
3. Local `libs/<bounded-context>/ts/contracts`: framework-neutral transport adapters and compatibility exports, explicitly nonauthoritative for shared HTTP operations.
4. Nest controllers, `@RouteSchema`, `tools/api-specs/route-metadata.ts` and implementation-generated `docs/openapi/*`: legacy baseline or conformance evidence only, never the public API master.

Consume exact published `@anarchitects/*-contracts` versions or documented immutable
artifacts with integrity evidence. Never overwrite released versions or import
3-tier implementation code, packages or generated specifications as DDD's contract
dependency. Map canonical transport shapes at presentation/application boundaries;
keep domain invariants and internal ports in DDD. Existing `auth-ts` policy
semantics remain separate from their canonical HTTP representation.

Shared API changes start in contracts with compatibility review and a versioned
release. DDD support requires both canonical OpenAPI checks and real HTTP
positive/negative/security tests against that version. See
[ADR-0006](docs/adr/0006-canonical-public-api-contract-consumption.md) and
[Meta ADR-0012](https://github.com/anarchitects/anarchitecture-meta/blob/main/adr/0012-canonical-public-api-contract-ownership.md).

### Current adoption status

This checkout contains governance documentation and Nx workspace foundations;
it currently has no Nx projects. The structure, package names and commands below
describe the intended architecture, not available libraries or executable targets.
Contracts [issue #2](https://github.com/anarchitects/anarchitecture-contracts/issues/2)
delivers foundations only. DDD [issue #24](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/24)
establishes ownership guidance, not package adoption, runtime support or parity.
Auth, Forms, Identity and Newsletter support must be demonstrated individually
under [epic #23](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/23);
missing implementations are not supported capabilities.

## Repository Structure

```text
libs/
  auth/
    ts/
      domain/
      contracts/
    nest/
      application/
      presentation/
      infrastructure-auth-engine/
      infrastructure-persistence/
      infrastructure-mailer/
      facade/
    angular/
      data-access/
      state/
      feature/
      ui/
      facade/

  forms/
    ts/
      domain/
      contracts/
    nest/
      application/
      presentation/
      infrastructure-persistence/
      facade/
    angular/
      data-access/
      state/
      feature/
      ui/
      facade/

  common/
    angular/design/
    angular/ui-primitives/
    angular/ui-composition/
    angular/ui-layouts/
    nest/mailer/
    ts/

examples/
  auth-angular-example/
  auth-nest-example/
  forms-angular-example/
  forms-nest-example/

docs/
  openapi/
  guides/
  adr/

tools/
  api-specs/
  angular-docs/
  typeorm/
```

## Layering Rules

### Bounded context structure

- `ts/domain` is the center and depends on nothing outside shared language-level utilities.
- `ts/contracts` is the local TypeScript transport/compatibility layer consuming canonical shared HTTP contracts.
- technology-specific `application -> ts/domain`
- `infrastructure -> application`, `ts/domain`
- `presentation -> application`
- Angular `feature -> state -> data-access`
- Angular `ui <- feature`
- `config`, `util`, and `ts/contracts` are shared support layers where explicitly intended

### Allowed dependency direction

- Nest: `presentation -> application <- infrastructure`
- Angular: `ui <- feature -> state -> data-access`
- Domain: pure TS only, under `ts/domain`
- Contracts: framework-agnostic transport adapters/exports under `ts/contracts`; shared HTTP schemas authored in `anarchitecture-contracts`
- Hexagonal core: `ts/domain <- application`; adapters point inward through ports

### Forbidden shortcuts

- infrastructure must not own application ports
- presentation must not depend directly on infrastructure services
- Angular `feature` must not import `data-access` directly
- DTO/transport packages must not become the domain model
- TypeORM entities must not become domain entities
- adapters must not call other adapters directly to bypass application orchestration

## Domain vs Contracts

The repository keeps a strict distinction between domain model and public contract model.

### Domain layer (`ts/domain`)

Use the domain layer for:

- entities
- value objects
- aggregates
- domain services
- domain events
- invariants
- specifications

Do not use the domain layer for:

- HTTP DTOs
- OpenAPI schemas
- ORM decorators
- Angular view models
- Nest controller request/response types

### Contracts layer (`ts/contracts`)

Use `ts/contracts` for:

- canonical Zod schema and inferred wire-type imports/exports
- framework-neutral transport adapters and compatibility exports
- serialization and validation support consuming canonical schemas
- audited legacy TypeBox compatibility material during migration
- frontend/backend alignment to the same immutable contract version

The local contracts layer may expose package-owned compatibility APIs, but it
does not author shared HTTP contracts. The domain layer owns business meaning.

## Public Consumption Model

Provide two consumption modes at the same time.

### Easy mode

Expose one root facade package per domain/tech when ergonomic consumption matters:

- `@anarchitects/auth-nest`
- `@anarchitects/auth-angular`
- `@anarchitects/forms-nest`
- `@anarchitects/forms-angular`

Facade packages compose internal DDD layers but do not erase them.

### Advanced mode

Expose layer-specific packages for targeted overrides:

- `@anarchitects/auth-ts-domain`
- `@anarchitects/auth-ts-contracts`
- `@anarchitects/auth-nest-application`
- `@anarchitects/auth-nest-presentation`
- `@anarchitects/auth-nest-infrastructure-persistence`
- `@anarchitects/auth-angular-state`
- `@anarchitects/auth-angular-data-access`

Use advanced mode for:

- replacing adapters
- custom composition
- host-specific infrastructure overrides
- narrow testing/integration needs

## Third-Party Library Policy

The default integration rule remains:

- the brick is the product
- the third-party library is the canonical internal engine
- ports/adapters remain the escape hatch

Examples:

- Better Auth as the internal auth engine
- TypeORM as the internal persistence engine
- Nodemailer via shared mailer modules as the internal mail engine

In a DDD repo, those engines belong in infrastructure layers, not in the shared TS core and not as the primary public API.

## Topology Position

This repository delivers reusable bounded-context libraries that can be composed in multiple deployment topologies.

Supported topologies:

- moduliths
- microservices
- micro-frontends

The default recommendation is:

- **start modulith-first**
- **extract later when justified**

The primary product of the repository is the reusable bounded-context library model. Deployment topology is an application composition concern that may evolve over time.

## Cross-Repo Alignment Position

This repository should stay aligned with `anarchitecture-bricks-3tier` at the domain/capability level.

Alignment means:

- comparable domain intent
- comparable public capability surface
- traceable mapping between 3-tier packages and DDD package families
- documented migration paths from 3-tier structure to DDD structure

Alignment does **not** mean identical file structure or reduced DDD explicitness.

## Quickstart

These are planned workflows; the projects and targets are not yet implemented in
this checkout. Implementation-derived OpenAPI will provide conformance evidence,
not canonical contract generation.

```bash
yarn install

# Implementation OpenAPI evidence (canonical artifacts come from contracts)
nx run api-specs:generate
nx run api-specs:lint
nx run api-specs:verify

# Angular technical docs
nx run angular-docs:generate

# Storybook
nx run storybook-angular:storybook

# Example apps
nx run auth-nest-example:serve
nx run auth-angular-example:serve
nx run forms-nest-example:serve
nx run forms-angular-example:serve

# Planned implementation checks; canonical HTTP conformance must also be proven
nx run auth-nest-example:contract-test
nx run auth-angular-example:contract-test
nx run forms-nest-example:contract-test
nx run forms-angular-example:contract-test
```

## Key Nx Targets

| Command                                             | Description                                               |
| --------------------------------------------------- | --------------------------------------------------------- |
| `nx run api-specs:generate`                         | Generate implementation OpenAPI evidence from controllers |
| `nx run api-specs:lint`                             | Lint generated OpenAPI                                    |
| `nx run api-specs:verify`                           | Validate required endpoints + snapshot stability          |
| `nx run angular-docs:generate`                      | Generate and merge Compodoc docs                          |
| `nx run docs-hub:validate-content`                  | Enforce required docs sections for guides and READMEs     |
| `nx run docs-hub:build`                             | Build static docs hub pages                               |
| `nx run release-tools:validate-non-bumping-commits` | Enforce non-bumping commit types for docs-surface PRs     |
| `nx affected -t lint test build`                    | Standard affected checks                                  |

## Why This Structure

This DDD-first structure is the default because it gives the repository:

- real architectural boundaries at the Nx project level
- better dependency enforcement than folder conventions alone
- a clear distinction between the shared TS core and technology-specific adapter families
- smaller affected sets in CI
- clearer ownership by bounded context and layer
- clean separation between domain model and transport model
- infrastructure replaceability without pretending every engine is first-class

## Architecture Guides

- [Guide: Angular Layering and Explicit State Scoping](docs/guides/angular-layering-and-state.md)
- [Guide: Topology Guide: Moduliths, Microservices, and Micro-Frontends](docs/guides/topology-modulith-microservices-microfrontends.md)
- [Guide: Migration Paths](docs/guides/migration-paths.md)
- [Guide: Alignment With `anarchitecture-bricks-3tier`](docs/guides/alignment-with-bricks-3tier.md)
- [Guide: Migration From `anarchitecture-bricks-3tier`](docs/guides/migration-from-bricks-3tier.md)

## Architecture Decision Records

- [ADR-0001: Hexagonal Architecture With DDD Boundaries](docs/adr/0001-hexagonal-architecture-with-ddd-boundaries.md)
- [ADR-0002: Technology-Prefixed Package Families With A Shared TS Core](docs/adr/0002-technology-prefixed-package-families-with-shared-ts-core.md)
- [ADR-0003: Angular Layering And Explicit State Scoping](docs/adr/0003-angular-layering-and-explicit-state-scoping.md)
- [ADR-0004: Modulith-First With Extraction Paths](docs/adr/0004-modulith-first-with-extraction-paths.md)
- [ADR-0005: Align With `anarchitecture-bricks-3tier` And Accept 3-Tier Migration](docs/adr/0005-align-with-bricks-3tier-and-accept-3tier-migration.md)
- [ADR-0006: Consume Canonical Public API Contracts Across DDD Boundaries](docs/adr/0006-canonical-public-api-contract-consumption.md)

## Documentation Tooling

- Storybook remains the default UI documentation and interaction surface.
- Compodoc provides Angular technical API metadata and pages.
- Canonical published OpenAPI 3.1 comes from `anarchitecture-contracts`; implementation-derived OpenAPI provides baseline/conformance evidence only.
- Docs hub static site is generated via `nx run docs-hub:build`.
- Package `README.md` files remain first-class docs sources and should describe both easy mode and advanced mode usage.

## Human-In-The-Loop Shortlist

See the contributor workflow context in `CONTRIBUTING.md`.

- Humans review all AI-proposed code changes.
- Humans own `git commit`, pull request creation, and pull request merge.
- AI agents must explicitly call out potential breaking changes.
- AI agents may suggest commit messages; humans run the final commit command.

## License

MIT
