# ADR-0005: Align With `anarchitecture-bricks-3tier` And Accept 3-Tier Migration

- Status: Accepted
- Date: 2026-04-23
- Owners: Architecture maintainers
- Context: `anarchitecture-bricks-ddd` and `anarchitecture-bricks-3tier` are companion repositories with different internal architectural styles

## Context

This repository and `anarchitecture-bricks-3tier` are intended to evolve as companion repositories.

They should not become literal copies of one another, but they also must not drift into unrelated products.

We need an explicit decision that:

- defines the relationship between the DDD and 3-tier repos
- clarifies what should align between them
- treats migration from 3-tier to DDD as a supported path

## Decision

### Companion repo posture

`anarchitecture-bricks-ddd` and `anarchitecture-bricks-3tier` are companion repositories.

They should align on:

- bounded-context/domain intent
- public capability surface by domain
- contract ownership and domain language
- example-app use cases where practical

They may differ on:

- internal package structure
- internal layering granularity
- explicitness of ports/adapters and DDD boundaries

### Architectural distinction

This repository remains the DDD/hexagonal companion implementation style.

The 3-tier repo remains the simpler 3-tier companion implementation style.

Neither repo is a mere copy of the other.

### Migration posture

Migration from 3-tier to DDD is a supported path.

That migration should preserve domain meaning and public capability while translating structure from:

- shared `ts`
- domain-level Angular package
- domain-level Nest package

into the more explicit DDD package families.

## Consequences

### Positive

- cross-repo drift becomes easier to detect
- the two repos can evolve coherently without forced sameness
- migration from 3-tier to DDD becomes part of the documented architecture story
- maintainers get a clear mapping model between the repos

### Trade-offs

- maintainers must document intentional divergence explicitly
- some domains require structural translation rather than direct copy/move operations
- cross-repo parity requires ongoing discipline

## Rules Derived From This Decision

1. New or changed bounded contexts in this repo should consider their counterpart in `anarchitecture-bricks-3tier`.
2. Capability alignment matters more than path-level sameness.
3. Migration from 3-tier should separate business meaning from transport/contracts and split frontend/backend responsibilities more explicitly.
4. Silent conceptual drift between the repos is not acceptable.

## Related

- [Guide: Alignment With `anarchitecture-bricks-3tier`](../guides/alignment-with-bricks-3tier.md)
- [Guide: Migration From `anarchitecture-bricks-3tier`](../guides/migration-from-bricks-3tier.md)
