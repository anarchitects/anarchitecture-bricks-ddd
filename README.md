# Anarchitecture Bricks - DDD

Modular, reusable libraries and showcase applications for scalable software architectures, with a Domain-Driven Design first repository structure.

This file is intentionally isolated under `docs/ddd/` so it can be copied into a DDD-oriented repository without changing this 3-tier repo.

## Purpose

- Publishable domain libraries in `libs/` organised by bounded context and layer.
- A pure TypeScript domain layer at the center of each bounded context.
- Explicit application, infrastructure, and presentation/frontend adapter layers around the domain model.
- Public facade packages for easy consumption, with deeper layer packages available for advanced composition.
- OpenAPI docs generated from Nest presentation implementation metadata (`@RouteSchema`).
- Shared transport and public-contract packages separated from domain model packages.
- Nx-managed example applications in `examples/` for integration and contract validation.

## Core DDD Position

This repository structure treats the domain model as the center of the architecture.

For each bounded context:

- `domain` contains pure business model code
- `application` contains use cases, orchestration, and ports
- `infrastructure` contains adapters to persistence, engines, mail, external systems
- `presentation` or frontend adapter layers expose the product surface
- facade packages provide an easy-mode entrypoint over those internals

The domain layer is pure TypeScript:

- no Angular or Nest dependencies
- no TypeORM decorators
- no HTTP DTO concerns
- no environment access
- no transport framework assumptions

## Source of Truth

1. Domain model and business rules: `libs/<bounded-context>/domain`.
2. Public/shared transport contracts: `libs/<bounded-context>/contracts` or `libs/<bounded-context>/ts`.
3. HTTP contract shape: Nest presentation controllers + pure `@RouteSchema` schema fields.
4. OpenAPI metadata (`operationId`, `tags`) is derived in `tools/api-specs/route-metadata.ts`.
5. Generated OpenAPI artifacts: `docs/openapi/openapi.json` and `docs/openapi/openapi.yaml`.

## Repository Structure

```text
libs/
  auth/
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

tools/
  api-specs/
  angular-docs/
  typeorm/
```

## Layering Rules

### Bounded context structure

- `domain` is the center and depends on nothing outside shared language-level utilities.
- `application -> domain`
- `infrastructure -> application`, `domain`
- `presentation -> application`
- Angular `feature -> state -> data-access`
- Angular `ui <- feature`
- `config`, `util`, and `contracts` are shared support layers where explicitly intended

### Allowed dependency direction

- Nest: `presentation -> application <- infrastructure`
- Angular: `ui <- feature -> state -> data-access`
- Domain: pure TS only
- Contracts: framework-agnostic transport/public schemas only

### Forbidden shortcuts

- infrastructure must not own application ports
- presentation must not depend directly on infrastructure services
- Angular `feature` must not import `data-access` directly
- DTO/transport packages must not become the domain model
- TypeORM entities must not become domain entities

## Domain vs Contracts

The repository keeps a strict distinction between domain model and public contract model.

### Domain layer

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

### Contracts layer

Use `contracts` or `ts` packages for:

- shared DTOs
- TypeBox/Zod schemas
- serialized response shapes
- route-schema imports for Nest presentation
- frontend/backend transport alignment

The contracts layer is package-owned public API. The domain layer is business-owned internal model.

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

In a DDD repo, those engines belong in infrastructure layers, not in the domain layer and not as the primary public API.

## Quickstart

```bash
yarn install

# OpenAPI from implementation
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

# Contract checks
nx run auth-nest-example:contract-test
nx run auth-angular-example:contract-test
nx run forms-nest-example:contract-test
nx run forms-angular-example:contract-test
```

## Key Nx Targets

| Command                                                    | Description                                           |
| ---------------------------------------------------------- | ----------------------------------------------------- |
| `nx run api-specs:generate`                                | Generate OpenAPI JSON/YAML from Nest controllers      |
| `nx run api-specs:lint`                                    | Lint generated OpenAPI                                |
| `nx run api-specs:verify`                                  | Validate required endpoints + snapshot stability      |
| `nx run angular-docs:generate`                             | Generate and merge Compodoc docs                      |
| `nx run docs-hub:validate-content`                         | Enforce required docs sections for guides and READMEs |
| `nx run docs-hub:build`                                    | Build static docs hub pages                           |
| `nx run release-tools:validate-non-bumping-commits`        | Enforce non-bumping commit types for docs-surface PRs |
| `nx affected -t lint test build`                           | Standard affected checks                              |

## Why This Structure

This DDD-first structure is the default because it gives the repository:

- real architectural boundaries at the Nx project level
- better dependency enforcement than folder conventions alone
- smaller affected sets in CI
- clearer ownership by bounded context and layer
- clean separation between domain model and transport model
- infrastructure replaceability without pretending every engine is first-class

## Documentation Tooling

- Storybook remains the default UI documentation and interaction surface.
- Compodoc provides Angular technical API metadata and pages.
- OpenAPI provides HTTP contract documentation derived from implementation.
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
