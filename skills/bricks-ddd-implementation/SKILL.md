---
name: bricks-ddd-implementation
description: implement and refactor code in anarchitecture-bricks-ddd. use when work belongs specifically in the ddd bricks repo, especially for bounded contexts, ts/domain, ts/contracts, explicit nest application or presentation or infrastructure layers, angular ui or feature or state or data-access layers, topology guidance, and preserving the ddd and hexagonal architecture of the repo.
---

# Overview

Use this skill when the task belongs in `anarchitecture-bricks-ddd`.

Before changing code, inspect these repo files when relevant:

- `AGENTS.md`
- `README.md`
- relevant `docs/guides/*`
- relevant `docs/adr/*`
- bounded-context package `README.md` files

For shared public HTTP API work, follow
[ADR-0006](../../docs/adr/0006-canonical-public-api-contract-consumption.md): read
the applicable canonical contract/version, compatibility rules and fixtures in
`anarchitecture-contracts`. Consume immutable versions; local `ts/contracts`
provides transport adapters/compatibility exports, not independent shared HTTP
authorship. Do not import 3-tier code or generated specs as contract dependencies.
If canonical artifacts are unavailable, report the dependency and limit work to
governance/inventory rather than inventing a local API or claiming support.

## Core implementation rules

- Preserve the shared TS split:
  - `ts/domain` for business meaning
  - `ts/contracts` for transport adapters and compatibility exports consuming canonical shared HTTP contracts
- Preserve the Nest layer split:
  - `application`
  - `presentation`
  - `infrastructure-*`
  - `facade`
- Preserve the Angular layer split:
  - `ui`
  - `feature`
  - `state`
  - `data-access`
  - `facade`
- Keep ports in `application` and adapters in `infrastructure-*`.
- Keep domain code framework-free.
- Do not create or modify anything under `apps/`.

## Required workflow

1. Identify the bounded context and target layer.
2. Read the governing docs for that layer before changing code.
3. Implement the smallest change that preserves the DDD structure.
4. Validate with package-manager-prefixed Nx commands.
5. In the final summary, call out affected boundaries, mappings, and any cross-repo impact on `anarchitecture-bricks-3tier`.

## Angular-specific guidance

- Follow the Angular layering and explicit state scoping rules.
- Keep state style and scope intentional.
- Do not introduce implicit root-scoped domain state.
- Do not introduce Signal Forms as the default forms approach.

## Topology and migration guidance

When the change affects repo topology guidance or migration expectations, also inspect:

- `docs/guides/topology-modulith-microservices-microfrontends.md`
- `docs/guides/migration-paths.md`
- `docs/guides/alignment-with-bricks-3tier.md`
- `docs/guides/migration-from-bricks-3tier.md`

## Output expectations

In the final summary, explicitly state:

- bounded context
- target layer(s)
- whether ports, adapters, or boundary mappings changed
- whether cross-repo alignment with the 3-tier repo may need follow-up
