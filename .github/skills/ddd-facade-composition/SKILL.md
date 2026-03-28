---
name: ddd-facade-composition
description: Maintain easy-mode facade packages without collapsing the DDD layers underneath. USE WHEN building or reviewing root package APIs, facade modules, secondary entry points, quick starts, or advanced override seams for DDD libraries.
---

# DDD Facade Composition

Use this skill when a bounded context exposes a public package surface.

## Consumption Model

Every publishable domain should support two modes:

1. Easy mode

- one root facade package or module
- minimal host wiring
- documented quick start

2. Advanced mode

- layer-specific packages or entry points
- targeted overrides
- explicit composition

## Facade Rules

- facades compose internal layers
- facades do not erase internal boundaries
- advanced mode remains supported and documented
- quick starts should prefer the facade

Hexagonal rule:

- facades are assembly and consumption helpers outside the hexagon
- facades may compose adapters and application modules
- facades must not push framework or adapter concerns back into domain or application

## Nest Guidance

For configurable Nest libraries, prefer:

- `forRoot(options)`
- `forRootFromConfig(overrides?)`

Use consistent precedence:

- explicit overrides
- config-derived values
- hardcoded defaults

## Angular Guidance

- keep root consumption ergonomic
- preserve layer-specific packages internally and for advanced consumers
- do not let facade convenience create invalid layer imports inside the implementation

Primary-adapter guidance:

- Angular feature and state layers are primary adapters over application behavior
- UI stays presentational
- data-access stays an outbound adapter and must not become a direct dependency of feature when state should mediate it

## Documentation Rule

README and guides should show:

- root facade quick start first
- advanced layer imports second
- which parts are stable public API
- which parts are infrastructure internals
- which parts sit inside the hexagon versus which parts are adapters or assembly
