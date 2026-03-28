<!-- nx configuration start-->
<!-- Leave the start & end comments to automatically receive updates. -->

## General Guidelines for working with Nx

- For navigating/exploring the workspace, invoke the `nx-workspace` skill first - it has patterns for querying projects, targets, and dependencies
- When running tasks (for example build, lint, test, e2e, etc.), always prefer running the task through `nx` (i.e. `nx run`, `nx run-many`, `nx affected`) instead of using the underlying tooling directly
- Prefix nx commands with the workspace's package manager (e.g., `pnpm nx build`, `npm exec nx test`) - avoids using globally installed CLI
- You have access to the Nx MCP server and its tools, use them to help the user
- For Nx plugin best practices, check `node_modules/@nx/<plugin>/PLUGIN.md`. Not all plugins have this file - proceed without it if unavailable.
- NEVER guess CLI flags - always check nx_docs or `--help` first when unsure

## Scaffolding & Generators

- For scaffolding tasks (creating apps, libs, project structure, setup), ALWAYS invoke the `nx-generate` skill FIRST before exploring or calling MCP tools

## When to use nx_docs

- USE for: advanced config options, unfamiliar flags, migration guides, plugin configuration, edge cases
- DON'T USE for: basic generator syntax (`nx g @nx/react:app`), standard commands, things you already know
- The `nx-generate` skill handles generator discovery internally - don't call nx_docs just to look up generator syntax


<!-- nx configuration end-->

# Agents Instructions - Anarchitecture Bricks DDD

This file lives under `docs/ddd/` so it can be copied into a DDD-first repo without changing the current 3-tier repository.

## Role

You are an engineering assistant for an Nx monorepo containing reusable libraries and example applications, organized by bounded context and DDD layers.

## Repository Default

The default internal structure is:

- bounded context first
- layer split second
- facade packages on top for easy consumption

The default expectation is that each bounded context contains:

- `domain`: pure TypeScript business model
- `contracts` or `ts`: public transport and shared wire contracts
- `application`: use cases, orchestration, ports
- `infrastructure-*`: persistence, engines, mail, external adapters
- `presentation` or frontend adapter layers
- `facade`: root easy-mode package for consumers

## Allowed Work

- Create, refactor, or remove Nx libraries under `libs/`.
- Create and maintain Nx-managed example applications under `examples/`.
- Update docs (`README.md`, `CONTRIBUTING.md`, package READMEs, guides).
- Add or refine domain entities, value objects, aggregates, policies, and domain services in pure TS domain libraries.
- Add and maintain transport contracts, DTOs, validators, builders, and mappers in `contracts` or `ts` libraries.
- Maintain Nx targets for build, lint, test, docs, release, and contract verification.
- Maintain OpenAPI generation from Nest presentation implementations.
- Add tests at the correct layer: domain, application, infrastructure, presentation, integration, contract, e2e.

## Forbidden Work

- Do not create or modify any directory under `apps/`.
- Do not manually edit generated OpenAPI artifacts in `docs/openapi/`.
- Do not collapse multiple DDD layers back into a single mixed library for convenience.
- Do not put framework code in `domain`.
- Do not place application ports in infrastructure libraries.
- Do not expose third-party libraries as the primary public API of a brick.
- Do not push directly to `main`.

## Architectural Principles

1. Split by bounded context first, then by layer.
2. Treat `domain` as the center of the architecture.
3. Keep `domain` pure TypeScript:
   - no Nest
   - no Angular
   - no TypeORM decorators
   - no environment access
   - no HTTP DTO concerns
4. Keep `contracts` or `ts` distinct from `domain`:
   - contracts model transport shape
   - domain models business meaning
5. Keep dependency direction strict:
   - Domain: depends only on language-level utilities and explicitly shared pure TS helpers
   - Nest: `presentation -> application <- infrastructure`
   - Angular: `ui <- feature -> state -> data-access`
   - `config`, `util`, `contracts`: only where intentionally shared
6. Application owns ports; infrastructure implements them.
7. Third-party libraries are canonical internal engines, not the public product surface.
8. Provide two consumption modes:
   - easy mode: root facade package
   - advanced mode: layer-specific packages or entry points
9. Keep example apps as integration and contract validation surfaces, not publishable bricks.
10. Prefer one root facade import in quick starts, but preserve advanced composition seams.
11. Keep typed configuration centralized and out of the domain layer.
12. Keep environment access out of domain and application logic.
13. Keep cross-bounded-context persistence relationships out of runtime entities.
14. Define cross-context database relationships only in integration-only migration schemas when needed.
15. Keep domain events, invariants, and aggregate rules in domain, not in controllers or repositories.

## Library API Paradigm

- The brick is the product.
- The third-party library is the canonical internal engine.
- Ports and adapters are the override seam.

Apply this consistently to:

- auth engines
- persistence engines
- mail transports
- search, queueing, storage, and messaging adapters

Public APIs should be package-owned. Third-party SDK types, handlers, or entities should not become the primary public surface.

## Layer Rules

### Domain

Use for:

- entities
- value objects
- aggregates
- domain services
- domain events
- invariants
- specifications

Do not use for:

- DTOs
- TypeORM entities
- Nest providers
- Angular state
- HTTP schemas
- environment reads

### Application

Use for:

- commands and queries
- use cases
- orchestration
- policy coordination
- ports
- transaction boundaries

Application may depend on:

- domain
- shared pure TS utilities
- contracts only when the public transport boundary requires explicit mapping

Application must not depend on infrastructure implementations.

### Infrastructure

Use for:

- persistence adapters
- mail adapters
- auth engine adapters
- external service clients
- queue adapters
- file storage adapters

Infrastructure may depend on:

- application ports
- domain
- contracts when transport/persistence mapping needs them

Infrastructure must not own business rules that belong in domain or orchestration that belongs in application.

### Presentation And Frontend Adapters

Use for:

- Nest controllers, guards, interceptors, route schemas
- Angular data-access, state, feature, ui layers

Presentation depends on application. It must not reach directly into infrastructure internals except in explicit composition roots.

## Repository Structure

Recommended default:

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
```

## Preferred Commands

- `yarn nx run <project>:lint`
- `yarn nx run <project>:test`
- `yarn nx run <project>:build`
- `yarn nx run api-specs:generate`
- `yarn nx run angular-docs:generate`
- `yarn nx affected -t lint test build`

## Expected Output Quality

- Production-ready code and docs.
- Passing lint, test, and build for affected scope.
- Clear domain boundaries.
- No framework leakage into `domain`.
- No dependency-direction shortcuts.
- Stable public APIs with advanced override seams preserved.

## Project Planning And Board Sync

When executing work tied to GitHub issues, keep the planning board synchronized during implementation.

- Set active issues to `In Progress` when implementation starts.
- Use `Blocked` immediately when work cannot proceed.
- Set `In Review` when a PR is opened.
- Set `Done` only after completion criteria are met.

Keep Milestone and Priority unchanged unless explicitly asked to modify them.

## Human In The Loop Governance

- AI agents may analyze, edit files, run validation, and suggest commit messages.
- Human developers review all code changes.
- Human developers perform git commit, PR creation, and PR merge.
- AI agents must explicitly call out potential breaking changes.
- AI agents must explicitly call out when a bug fix may warrant npm package deprecation.
- Any deprecation action remains proposed-only until human approval is given.

## DDD-Specific Guardrails

### Domain Purity Rule

Domain libraries must not:

- import Nest packages
- import Angular packages
- import ORM decorators or repository APIs
- read environment variables
- import HTTP DTO libraries as if they were the domain model

### Ports And Adapters Rule

- Ports live in `application`.
- Adapters live in `infrastructure-*`.
- Composition roots wire ports to adapters.
- Do not place ports in persistence folders merely because repositories implement them.

### Facade Rule

- Root facade packages provide easy-mode consumption.
- Facades compose internal layers; they do not erase them.
- Advanced packages remain first-class and supported.

### Contracts Rule

- `contracts` or `ts` packages define public transport shape.
- Do not let DTOs, validators, or route schemas become the domain model by accident.

## Cross-Context Persistence Convention

Bounded contexts must not define ORM relations directly across context boundaries in runtime entities.

Use:

- scalar foreign keys in runtime entities
- integration-only schemas for migration-time foreign keys
- application composition, query joins, or read models for cross-context reads

## Angular State Scoping Convention

Domain state stores must not be implicitly global.

- do not use `providedIn: 'root'` for domain stores
- provide stores through explicit provider helpers
- register state at component, route, feature, or app bootstrap scope intentionally

## New Bounded Context Onboarding Rule

Whenever a new bounded context is introduced:

- add the context’s layer libraries
- add or update release-group configuration
- update docs and examples
- add minimal contract, build, lint, and test coverage
- preserve the bounded-context + layer dependency direction from the start
