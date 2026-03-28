---
name: ddd-bounded-context-setup
description: Create or restructure a bounded context in the DDD repo. USE WHEN the user asks to add a new domain, split a mixed library into DDD layers, scaffold a bounded context, or align a context with domain/application/infrastructure/facade structure.
---

# DDD Bounded Context Setup

Use this skill when work changes the structure of a bounded context.

## Target Outcome

Default internal shape:

- `domain`
- `contracts` or `ts`
- `application`
- `infrastructure-*`
- `presentation` or frontend adapter layers
- `facade`

Treat that structure as a hexagon:

- `domain` and `application` are inside the hexagon
- `presentation` and Angular delivery layers are primary adapters
- `infrastructure-*` packages are secondary adapters
- dependencies must point inward toward the hexagon, never outward from it

Do not collapse those concerns into a single mixed package unless the user explicitly asks for an exception.

## Required Checks

1. Define the bounded context name and its public product surface.
2. Decide the internal layers needed right now.
3. Keep the public easy-mode facade package separate from advanced layer packages.
4. Update release-group and domain-tag validation when a new bounded context is introduced.
5. Add or update example-app usage if the new context needs integration validation.

## Dependency Direction

- `application -> domain`
- `infrastructure -> application`, `domain`
- `presentation -> application`
- Angular: `ui <- feature -> state -> data-access`

Hexagonal interpretation:

- delivery adapters call use cases through application-facing ports
- secondary adapters implement ports owned by the application layer
- inner layers must not import adapter implementations

Do not let scaffolding introduce shortcuts that will need cleanup later.

## Output Expectations

When implementing or planning a bounded-context change, explicitly cover:

- package layout
- public facade import path
- advanced layer import paths
- release-group impact
- docs impact
- validation targets
