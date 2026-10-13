# Alignment With `anarchitecture-bricks-3tier`

- Status: Active guide
- Audience: Maintainers and contributors of `anarchitecture-bricks-ddd`
- Scope: Cross-repo alignment with `anarchitects/anarchitecture-bricks-3tier`

## Purpose

`anarchitecture-bricks-ddd` and `anarchitecture-bricks-3tier` are companion repositories.

They must mirror each other sensibly at the bounded-context and capability level, while preserving their different internal architectural styles.

The goal is:

- the same business capabilities remain recognizable across both repos
- this repo provides the DDD/hexagonal implementation style
- the 3-tier repo provides the 3-tier implementation style
- migration from 3-tier to DDD remains explicit and practical

## Core Position

The two repos should align on:

- bounded-context/domain intent where possible
- public capability surface by domain
- contract ownership and domain language
- example-app scenarios used to validate the libraries
- documented mapping between the two architectural styles

The two repos should differ on:

- internal package structure
- internal layering explicitness
- granularity of ports/adapters and application boundaries

## What “Aligned” Means

Alignment does **not** mean that both repos must use the same file structure or the same internal layer count.

Alignment means that a maintainer can answer these questions clearly across both repos:

1. Which bounded context in DDD corresponds to which domain in 3-tier?
2. Which public capabilities are intentionally equivalent?
3. How does the simpler 3-tier packaging map into the more explicit DDD packaging?
4. What is the migration path from 3-tier implementation to DDD implementation?

## Repository Relationship

### `anarchitecture-bricks-ddd`

This repo is the DDD companion implementation style for:

- reusable libraries organized by bounded context, technology family, and explicit layer
- shared TS core split into `ts/domain` and `ts/contracts`
- explicit Nest and Angular package families

### `anarchitecture-bricks-3tier`

The 3-tier repo is the companion implementation style for:

- reusable libraries organized per domain in a simpler 3-tier package model
- shared TypeScript models and DTOs in `libs/<domain>/ts`
- Angular and Nest deliverables packaged per domain without the full DDD split

## Canonical Mapping Model

For shared public HTTP APIs, both implementations consume immutable versioned
contracts from [`anarchitecture-contracts`](https://github.com/anarchitects/anarchitecture-contracts).
Neither implementation's controllers, local DTOs or generated OpenAPI govern the
other. Local `ts/contracts` adapts or re-exports canonical wire contracts; domain
meaning and internal application ports remain in each bounded context. See
[ADR-0006](../adr/0006-canonical-public-api-contract-consumption.md).

The structural mapping below is migration guidance, not permission to import
3-tier code, packages or generated specifications as DDD contract dependencies.
Capability alignment is a goal: claim support only for operations and contract
versions proven by canonical OpenAPI and real HTTP positive/negative/security
tests. Record missing implementations explicitly.

Use this mapping model when documenting or implementing equivalent capabilities.

### Shared TS

3-tier:

- `libs/<domain>/ts`

DDD:

- `libs/<context>/ts/domain`
- `libs/<context>/ts/contracts`

Interpretation:

- the 3-tier TS library often contains concerns that this repo intentionally separates
- migration should distinguish business meaning from transport/public schemas

### Nest

3-tier:

- `libs/<domain>/nest`

DDD:

- `libs/<context>/nest/application`
- `libs/<context>/nest/presentation`
- `libs/<context>/nest/infrastructure-*`
- `libs/<context>/nest/facade`

Interpretation:

- one 3-tier Nest package typically maps to several DDD Nest layer packages

### Angular

3-tier:

- `libs/<domain>/angular`

DDD:

- `libs/<context>/angular/ui`
- `libs/<context>/angular/feature`
- `libs/<context>/angular/state`
- `libs/<context>/angular/data-access`
- `libs/<context>/angular/facade`

Interpretation:

- one 3-tier Angular package typically maps to several explicit frontend layer packages in this repo

## Alignment Rules

### 1. Keep bounded-context/domain names intentionally close

If a capability exists in one repo, the counterpart in the other repo should be clearly traceable by name and intent.

### 2. Keep public language aligned

Public DTO names, concepts, and package-level language should not drift casually across repos.

### 3. Keep examples comparable

If `auth` or `forms` exists in both repos, the example flows should still validate comparable domain use cases.

### 4. Preserve architectural distinction

This repo should remain more explicit than the 3-tier repo. Alignment must not collapse the DDD structure back into a simpler 3-tier model.

### 5. Document intentional divergence

If one repo intentionally moves ahead of the other for a capability, document the divergence explicitly.

## Preferred Evolution Pattern

When a capability first exists in 3-tier and later needs stronger domain boundaries, the preferred evolution is:

1. preserve domain name and public intent
2. separate business meaning from transport/contracts
3. translate Nest concerns into application, presentation, infrastructure, and facade packages
4. translate Angular concerns into `ui`, `feature`, `state`, `data-access`, and `facade`
5. document equivalence and migration in both repos

## Anti-Patterns

Avoid:

- letting the same domain mean different things across the repos
- mirroring paths mechanically without architectural translation
- treating this DDD repo as just a renamed copy of 3-tier
- drifting away from the 3-tier repo without documenting why

## Related

- [Guide: Migration From `anarchitecture-bricks-3tier`](./migration-from-bricks-3tier.md)
