# ADR-0006: Consume Canonical Public API Contracts Across DDD Boundaries

- Status: Accepted
- Date: 2026-10-13
- Owners: Architecture maintainers
- Scope: Shared public HTTP contracts and DDD implementation boundaries
- Implements: [issue #24](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/24), under [epic #23](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/23)

## Context and decision preflight

The repository previously described Nest controllers, `@RouteSchema` fields and
`tools/api-specs/route-metadata.ts` as the HTTP contract source of truth.
[Accepted Meta ADR-0012](https://github.com/anarchitects/anarchitecture-meta/blob/main/adr/0012-canonical-public-api-contract-ownership.md)
instead assigns shared public HTTP contract ownership to
[`anarchitecture-contracts`](https://github.com/anarchitects/anarchitecture-contracts).
Meta PR #23 merged on 2026-10-12 at
`e2f34553c6e2492d0ab6f80ac795de44fc592a70`; the ownership prerequisite is met.

Before this change, all 15 Meta ADR files at that revision and all 7 existing
local ADR files were enumerated and reviewed, including duplicate-numbered
records and the historical landscape record. Meta's cooperative-enrichment and
ADR-0005/0006/0007 governance proposals remain proposed and do not override
accepted decisions. The alignment, migration and topology guides were reviewed.

Applicable constraints are Meta ADR-0001/0004 (independent implementation styles
and landscape governance), HTTP ADR-0008 (adapter independence), ADR-0010/0011
(Zod direction, compatibility and policy semantics), and ADR-0012 (contract
ownership). Local hexagonal ADR-0001, package-taxonomy ADR-0002, topology ADR-0004
and alignment ADR-0005 remain binding. Angular layer/state decisions and the
accepted Modern Angular amendment remain unchanged; Native Node remains a
candidate, not an approved default.

This decision resolves the old controller-derived authority statements and
refines the ownership-pending clause of the accepted Nest-first ADR-0001.
References to local `ts/contracts` in earlier ADRs describe package placement,
not independent authority over shared HTTP operations. Their domain, layer,
port, composition and compatibility rules remain in force.

## Decision

1. **External, contract-first authority.** `anarchitecture-contracts` solely owns
   shared public HTTP methods, paths, operation IDs, parameters, requests,
   responses, errors, statuses, security requirements and relevant headers/cookies.
   Canonical authoring is Zod-first transport schemas plus first-class operation
   definitions. Deterministic OpenAPI 3.1 JSON/YAML is a published artifact of
   that source, never a separately authored specification. Meta owns governance;
   neither Bricks implementation owns the other's API.
2. **Immutable consumption.** Consume domain-scoped `@anarchitects/*-contracts`
   artifacts at exact published versions or documented immutable pins with
   integrity evidence, using actual names approved by the contracts repository.
   Do not use mutable branches, `latest`, sibling checkout paths or unversioned
   copied schemas as the adoption mechanism. Never overwrite published versions;
   corrections require a new release. Do not import 3-tier implementation code,
   packages or generated API specifications to implement DDD contracts. Contracts
   must remain independent of Nest, HTTP platforms, Angular, Nx and both Bricks
   implementations.
3. **DDD owns business meaning.** `ts/domain` retains entities, value objects,
   invariants, aggregates and domain events. Technology-specific `application`
   packages own use cases and inbound/outbound ports. Neither domain models nor
   internal ports move to the external wire-contract repository. Domain code
   must not import Zod HTTP schemas, DTOs, persistence or framework types.
4. **Explicit boundary translation.** Presentation adapters validate canonical
   requests and map them into application commands/queries and domain concepts;
   map application outcomes and errors back to canonical HTTP responses.
   Infrastructure adapters map persistence/SDK shapes through application ports.
   Facades, modules and host bootstrap remain the composition roots. Local
   `ts/contracts` may expose framework-neutral compatibility exports and transport
   adapters, but must not independently author shared HTTP operations or duplicate
   canonical schemas. Keep mappings explicit and tested; matching names do not
   make a transport object a domain object.
5. **Preserve policy semantics.** Existing `@anarchitects/auth-ts` ownership of
   authorization-policy semantics and evaluation conformance remains intact.
   Contracts owns the HTTP representation where policy crosses the wire. Map
   that representation without lossy duplication; unsupported conditional,
   field or deny semantics must fail closed, not weaken authorization.
6. **Implementation evidence, not authorship.** Existing TypeBox DTOs/validators,
   controller metadata, operation/tag mappings and implementation-derived
   OpenAPI are legacy baseline or conformance evidence only. Preserve audited
   compatibility material during migration without evolving a second authored
   schema set. Shared API changes start in contracts with compatibility review
   and release, then flow to DDD consumers.
7. **Prove each supported capability.** Adoption requires canonical OpenAPI
   comparison and real HTTP positive, negative and security tests against the
   same immutable contract version. Cover routes/methods/operation IDs, schemas,
   statuses, authentication/authorization, headers/cookies and pagination where
   applicable. Schema equality alone is insufficient. Record actual supported
   operations, contract version, implementation revision, runtime/adapter and
   evidence per bounded context. Missing Auth, Forms, Identity or Newsletter
   capabilities remain explicitly unsupported; parity is not inferred from the
   companion repository. Fail CI on discrepancies or explicitly remove the
   unsupported capability claim.
8. **Compatibility and sequencing.** Preserve the audited wire baseline unless a
   separately reviewed, versioned breaking change and consumer migration are
   accepted. Publish contracts and fixtures/tooling before dependent code
   adoption. Keep Nest 12 as the new-feature line and Nest 11 patch/security-only.
   Authority changes do not authorize runtime upgrades, provider replacement,
   feature backports or removal of compatibility exports. Rollback uses the last
   compatible immutable pin; fixes go into new versions.
9. **Agent obligations.** In addition to all Meta/local ADRs and relevant guides,
   read the applicable canonical operations/schemas, exact version, compatibility
   rules and fixtures before changing a shared public API. Record the chosen
   version and affected mappings. If the canonical contract is unavailable,
   report that dependency and limit work to governance/inventory; do not invent
   a local replacement or claim adoption.

## Current state and staged delivery

This is the governance slice of epic #23. Contracts issue #2 establishes workspace
and release foundations; it does not establish extracted or published packages.
This DDD checkout currently has no Nx projects (`yarn nx show projects --json`
returns `[]`), implementation libraries, example hosts or API-spec generation
tooling. README package paths and commands describe the intended architecture.
This ADR claims no runtime capability or canonical-contract conformance.

Later subissues #25–#27 deliver inventory, consumption, implementation and
verification under the epic's dependencies. Each receives a separate reviewed
PR. This change adds no runtime package, port, adapter, composition wiring or
boundary mapper and does not complete the epic.

## Consequences

Public wire compatibility can survive different implementation styles and runtime
choices. DDD remains responsible for business behavior and explicit translation,
while contract changes acquire an independent release lifecycle.

Consumer upgrades require contract availability and behavioral evidence. Local
controller or DTO edits cannot authorize shared API drift. This documentation
change introduces no breaking package API and requires no npm deprecation.

## Related

- [Hexagonal boundaries](0001-hexagonal-architecture-with-ddd-boundaries.md)
- [Nest-first ports, adapters and schemas](0001-nest12-first-ports-adapters-and-schema-contracts.md)
- [Cross-repository alignment](../guides/alignment-with-bricks-3tier.md)
- [Contracts foundation ADR](https://github.com/anarchitects/anarchitecture-contracts/blob/main/adr/0001-contract-workspace-and-release-foundations.md)
- [Canonical release and compatibility rules](https://github.com/anarchitects/anarchitecture-contracts/blob/main/docs/releases.md)
