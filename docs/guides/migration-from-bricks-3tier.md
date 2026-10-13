# Migration From `anarchitecture-bricks-3tier`

- Status: Active guide
- Audience: Maintainers planning or executing migration from `anarchitecture-bricks-3tier` into `anarchitecture-bricks-ddd`
- Scope: Domain-by-domain and layer-by-layer migration from 3-tier structure to DDD structure

## Purpose

This guide describes how to migrate capabilities from the 3-tier repo into the DDD repo.

The goal is **not** to copy code mechanically. The goal is to preserve domain meaning and public capability while translating the implementation into the more explicit DDD structure used here.

## Core Principle

Migrate **architecture structure**, not **business meaning**.

A successful migration preserves:

- the same bounded-context/domain intent
- the same or intentionally evolved public language
- the same capability surface where appropriate
- comparable example use cases where practical

What changes is the internal structure and separation of responsibilities.

Shared public HTTP contracts are owned by
[`anarchitecture-contracts`](https://github.com/anarchitects/anarchitecture-contracts),
not by either implementation. Before migration, read the applicable canonical
contract/version and compatibility rules. Consume exact published versions or
documented immutable artifacts with integrity evidence; do not import 3-tier
code, packages or generated API specifications as DDD contract dependencies.
The structural mapping below does not transfer shared HTTP authorship into DDD.
See [ADR-0006](../adr/0006-canonical-public-api-contract-consumption.md).

## Canonical Structural Mapping

### Shared TS

From 3-tier:

- `libs/<domain>/ts`

To DDD:

- `libs/<context>/ts/domain`
- `libs/<context>/ts/contracts`

Migration question:

- which parts of the 3-tier TS package are business model concerns?
- which parts are transport/public schema concerns?

### Nest

From 3-tier:

- `libs/<domain>/nest`

To DDD:

- `libs/<context>/nest/application`
- `libs/<context>/nest/presentation`
- `libs/<context>/nest/infrastructure-*`
- `libs/<context>/nest/facade`

Migration question:

- which code is orchestration/use-case logic?
- which code is controller/presentation?
- which code is persistence/integration adapter logic?
- which code is easy-mode composition?

### Angular

From 3-tier:

- `libs/<domain>/angular`

To DDD:

- `libs/<context>/angular/ui`
- `libs/<context>/angular/feature`
- `libs/<context>/angular/state`
- `libs/<context>/angular/data-access`
- `libs/<context>/angular/facade`

Migration question:

- which code is presentational?
- which code is smart composition?
- which code is state/orchestration?
- which code is transport/integration?

## Recommended Migration Sequence

### Step 1: confirm the target bounded context

Before migrating, confirm that the 3-tier domain maps cleanly to one target bounded context in the DDD repo.

If not, decide whether:

- the 3-tier domain should split into multiple bounded contexts
- the 3-tier domain should remain one bounded context with cleaner internal boundaries

### Step 2: split TS concerns first

Start by separating:

- domain meaning
- public transport/contracts

This is usually the most important structural change because this repo treats those as separate first-class packages.

Keep business rules in `ts/domain`; use local `ts/contracts` only for transport
adapters/compatibility exports consuming canonical shared HTTP schemas. Map
requests, responses and errors at presentation/application boundaries. Preserve
`auth-ts` policy semantics separately from their HTTP representation.

### Step 3: extract Nest layer responsibilities

Inside the 3-tier Nest package, identify:

- application/use-case logic
- presentation/controller concerns
- infrastructure adapters
- facade/composition logic

Move these into the DDD Nest package family.

### Step 4: extract Angular layer responsibilities

Inside the 3-tier Angular package, identify:

- `ui`
- `feature`
- `state`
- `data-access`
- `facade`

Do not migrate the Angular package as one undifferentiated unit.

### Step 5: preserve public capability parity

After the structural migration, verify that the target DDD packages still represent the same domain capability intentionally.

### Step 6: validate with comparable examples

Use or create example flows that prove the migrated DDD bounded context still supports the equivalent use cases.

Prove each claimed operation against the same immutable canonical version with
OpenAPI comparison and actual HTTP positive, negative and security tests. Record
missing capabilities as unsupported. Contract packages and conformance tooling
must be available before adoption; governance work alone does not establish parity.

## Incremental Migration Strategy

Preferred strategy:

- migrate one domain/bounded context at a time
- migrate TS meaning/contracts first
- migrate Nest and Angular in slices
- keep both repos documented during overlap

Avoid trying to rewrite the whole repo family in one move.

## Practical Checklist

For each migrating domain, answer:

1. What is the target bounded-context name in the DDD repo?
2. What in `libs/<domain>/ts` becomes `ts/domain`?
3. What in `libs/<domain>/ts` becomes `ts/contracts`?
4. Which Nest code becomes `application`?
5. Which Nest code becomes `presentation`?
6. Which Nest code becomes `infrastructure-*`?
7. Which Angular code becomes `ui`, `feature`, `state`, and `data-access`?
8. Which example use cases prove parity after migration?

## Safe Migration Heuristics

A 3-tier domain is a good candidate for DDD migration when:

- business rules are becoming harder to isolate
- DTO/schema concerns are mixed with domain meaning
- Nest code is carrying multiple responsibilities in one package
- Angular code is growing into a clear `ui`/`feature`/`state`/`data-access` split anyway
- teams want more explicit ports/adapters and composition boundaries

## Anti-Patterns

Avoid:

- copying the 3-tier TS package unchanged into `ts/domain`
- moving all Nest code into one DDD layer and calling it migrated
- moving all Angular code into `feature` and leaving `ui`/`state`/`data-access` empty
- renaming without separating responsibilities
- allowing the two repos to drift during migration without documenting equivalence

## Related

- [Guide: Alignment With `anarchitecture-bricks-3tier`](./alignment-with-bricks-3tier.md)
