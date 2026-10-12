# ADR-0001: Nest 12 First-Party Infrastructure Through DDD Ports and Adapters

- Status: Proposed
- Date: 2026-10-12
- Owners: Architecture maintainers

## Context
The DDD repo is the companion implementation style to Bricks 3-Tier. Ecosystem decisions A2/B2/C1/D2/E1/F1 are documented in [Meta](https://github.com/anarchitects/anarchitecture-meta/blob/main/adr/0010-nest-first-runtime-and-dependency-policy.md). This repo must implement the same public capability and policy vocabulary without importing framework concerns into its pure TypeScript core.

## Decision
1. Keep bounded-context `ts/domain` and `ts/contracts` independent of Nest, CASL, TypeORM, Node HTTP and concrete authentication providers. Zod becomes the preferred shared transport schema technology, not a domain-model dependency by default.
2. Nest 12 is the feature-development target. Nest 11 receives patch/security fixes only on explicitly maintained lines, with no automatic new-feature backports.
3. `@nestjs/authentication` is the target Nest session/auth engine behind ports, with optional `@simplewebauthn/server` passkeys in Nest adapter packages. Persisted credentials and sessions require explicit migration and security parity before replacing Better Auth.
4. The canonical authorization policy DTO/AST belongs in framework-neutral `@anarchitects/auth-ts` (or its explicitly compatible DDD contract equivalent), with conformance fixtures shared across Nest `@nestjs/authorization` and Angular CASL adapters. Field/conditional/deny semantics must be proven equivalent or fail closed.
5. `@nestjs/mail` is the preferred mail delivery adapter behind application mail ports; NodeMailer/Handlebars dependencies are removable only after template/SMTP parity checks.
6. Nest reliability/outbox/idempotency/locking/observability capabilities are opt-in infrastructure adapters, not dependencies of domain or application packages.
7. Avoid reinventing Nest functionality in facades while preserving the public easy/advanced composition contracts and domain-alignment relationship with Bricks 3-Tier.
8. This ADR establishes a target architecture, not existing shipped functionality. Implementation, compatibility proof and release approvals are separate.

## Related
- [Meta Nest-first direction](https://github.com/anarchitects/anarchitecture-meta/blob/main/adr/0010-nest-first-runtime-and-dependency-policy.md)
- [Bricks 3-Tier counterpart](https://github.com/anarchitects/anarchitecture-bricks-3tier/tree/main/docs/adr)
