# DDD Capability Inventory and Canonical API Mapping

- Status: Audited snapshot; no DDD runtime support claimed
- Audit date: 2026-10-13
- Audience: Maintainers planning canonical contract adoption
- Scope: Auth, Forms, Identity and Newsletter; [issue #25](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/25) under [epic #23](https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/23)

## Result

DDD implements **none of the four bounded contexts or their HTTP operations** at
the audited revision. There are no application libraries, transport adapters,
example hosts or contract tests. The README package tree is intended architecture.
There is no DDD runtime against which to assert either compatibility or a divergent
response. All capabilities below are **absent and unsupported**.

The canonical contracts repository currently contains workspace/release foundations,
without domain packages, operation definitions, generated OpenAPI or conformance
fixtures. Canonical package versions and operation mappings are therefore
**pending**, not inferred from 3-tier. This inventory can complete before those
artifacts exist; code adoption cannot.

## Evidence and method

The following clean local revisions were inspected. The 3-tier and contracts
revisions also matched GitHub `main` at audit time. Links pin evidence to those
commits so later upstream changes do not silently alter this snapshot.

| Repository    | Audited revision                                                                                                                                        | Evidence role                                                                 |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| Bricks DDD    | [`91a1114abdd291d60a6fe53c99643c6feb6747fc`](https://github.com/anarchitects/anarchitecture-bricks-ddd/tree/91a1114abdd291d60a6fe53c99643c6feb6747fc)   | Actual implementation inventory after #24 / PR #28                            |
| Contracts     | [`44e4db89367521a97f1d4a4917ae2baae880fe15`](https://github.com/anarchitects/anarchitecture-contracts/tree/44e4db89367521a97f1d4a4917ae2baae880fe15)    | Canonical owner; foundations only                                             |
| Bricks 3-tier | [`ecda1b6e0d9bc39c66373452a92dce91e06abee0`](https://github.com/anarchitects/anarchitecture-bricks-3tier/tree/ecda1b6e0d9bc39c66373452a92dce91e06abee0) | Read-only legacy implementation reference, not DDD's specification dependency |
| Meta          | [`e2f34553c6e2492d0ab6f80ac795de44fc592a70`](https://github.com/anarchitects/anarchitecture-meta/tree/e2f34553c6e2492d0ab6f80ac795de44fc592a70/adr)     | Accepted ownership and compatibility constraints                              |

DDD checks and findings:

| Check                                                     | Observed result                                                                                            |
| --------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `yarn nx show projects --json`                            | `[]`; no projects or inferred project targets                                                              |
| `git ls-files libs examples tools/api-specs docs/openapi` | No tracked files                                                                                           |
| Tracked `package.json` inventory                          | Only the private root manifest; development tooling only, no capability or canonical contract dependencies |
| Source-file inventory                                     | Only maintainer CI skill scripts; no application/controller/domain source                                  |
| `nx.json` and `.github/workflows/ci.yml`                  | Workspace inputs and generic CI task invocation; no capability-specific HTTP conformance gates             |

The contracts checkout similarly has only a root package manifest;
`packages/README.md` defines future conventions. Its [release rules][contract-releases]
separate publication from consumer adoption. A matching npm scope or package name
in documentation is not release evidence. No canonical version is selected here.

All 15 Meta and all 8 local ADRs were enumerated and read, along with the contracts
foundation ADR/release rules and local alignment, migration and topology guides.
Accepted Meta ADR-0012 and [local ADR-0006](../adr/0006-canonical-public-api-contract-consumption.md)
govern authority; local hexagonal, taxonomy and alignment decisions preserve domain,
port and adapter boundaries. Proposed governance ADRs remain proposals. Earlier
ownership-pending language is resolved by ADR-0006. No new architectural exception
is needed for this inventory.

This is a static source/artifact audit. It did not run 3-tier hosts, regenerate
their OpenAPI, test production deployments or inspect published npm artifacts.
The counts below describe the pinned evidence, not all provider-generated routes
or a verified 3-tier runtime support matrix. The full legacy wire/schema/security
freeze belongs to [contracts #3][contracts-3]. No implementation code or API
specification is imported into DDD by this audit.

## Support matrix

Status definitions:

- **Supported:** an implemented DDD operation passes canonical OpenAPI and actual
  HTTP positive/negative/security checks against a recorded immutable version.
- **Absent:** no DDD implementation exists; unsupported, regardless of 3-tier availability.
- **Divergent:** an implemented DDD operation demonstrably differs from an available
  canonical version. None can be evaluated yet; absence is not a successful comparison.
- **Pending canonical mapping:** contracts has not released the operation/version
  needed to finalize the mapping. This can coexist with an absent implementation.

| Context    | 3-tier reference scope                                        | DDD libraries / routes / host | DDD support | Canonical package/version | DDD runtime/adapter and conformance evidence |
| ---------- | ------------------------------------------------------------- | ----------------------------- | ----------- | ------------------------- | -------------------------------------------- |
| Auth       | 14 OpenAPI operations; 3 additional optional JWT declarations | Absent / absent / absent      | Unsupported | Pending                   | None                                         |
| Forms      | 4 OpenAPI operations                                          | Absent / absent / absent      | Unsupported | Pending                   | None                                         |
| Identity   | 4 OpenAPI operations                                          | Absent / absent / absent      | Unsupported | Pending                   | None                                         |
| Newsletter | 4 OpenAPI operations, with conditional controller mounting    | Absent / absent / absent      | Unsupported | Pending                   | None                                         |

Totals: **0 supported DDD operations**, **26 reference OpenAPI operations absent**,
and **3 additional optional JWT routes absent**. Canonical divergence is not
assessable. The stale refresh-token metadata entry below is not counted as an
implemented operation. No Angular capability or public DDD package is implied by
the presence of Nx/Angular/Nest development dependencies in the root manifest.

## Operation mapping from the legacy reference

Every row maps a 3-tier method/path to its intended contract domain and future DDD
bounded context of the same name. The operation IDs are **3-tier metadata IDs**,
not finalized canonical IDs. All rows await reconciliation with contracts #3–#8.
Paths are as declared in the reference generation host, without any downstream
host prefix; braces represent path parameters.

The [committed OpenAPI][legacy-openapi], [operation map][route-metadata] and
controller sources agree on these 26 rows. All have DDD state **Absent**.

| Context    | Method and path                                    | Legacy operation ID             | DDD state |
| ---------- | -------------------------------------------------- | ------------------------------- | --------- |
| Auth       | `PATCH /auth/activate`                             | `activateUser`                  | Absent    |
| Auth       | `PATCH /auth/change-password/{userId}`             | `changePassword`                | Absent    |
| Auth       | `POST /auth/forgot-password`                       | `forgotPassword`                | Absent    |
| Auth       | `POST /auth/login`                                 | `login`                         | Absent    |
| Auth       | `POST /auth/logout`                                | `logout`                        | Absent    |
| Auth       | `GET /auth/me`                                     | `getLoggedInUserInfo`           | Absent    |
| Auth       | `POST /auth/passkeys/authentication/begin`         | `beginAuthentication`           | Absent    |
| Auth       | `POST /auth/passkeys/authentication/finish`        | `finishAuthentication`          | Absent    |
| Auth       | `POST /auth/passkeys/registration/begin`           | `beginRegistration`             | Absent    |
| Auth       | `POST /auth/passkeys/registration/finish`          | `finishRegistration`            | Absent    |
| Auth       | `POST /auth/register`                              | `registerUser`                  | Absent    |
| Auth       | `POST /auth/reset-password`                        | `resetPassword`                 | Absent    |
| Auth       | `PATCH /auth/update-email/{userId}`                | `updateEmail`                   | Absent    |
| Auth       | `POST /auth/verify-email`                          | `verifyEmail`                   | Absent    |
| Forms      | `GET /forms/{formId}`                              | `getFormDefinition`             | Absent    |
| Forms      | `GET /forms/submissions`                           | `getSubmissions`                | Absent    |
| Forms      | `GET /forms/submissions/{submissionId}`            | `getSubmission`                 | Absent    |
| Forms      | `POST /forms/submit`                               | `submitForm`                    | Absent    |
| Identity   | `POST /identity/profiles`                          | `createUserProfile`             | Absent    |
| Identity   | `GET /identity/profiles/{profileId}`               | `getUserProfileById`            | Absent    |
| Identity   | `PATCH /identity/profiles/{profileId}`             | `updateUserProfile`             | Absent    |
| Identity   | `GET /identity/profiles/by-auth-user/{authUserId}` | `getUserProfileByAuthUserId`    | Absent    |
| Newsletter | `POST /newsletter/confirm`                         | `confirmNewsletterSubscription` | Absent    |
| Newsletter | `POST /newsletter/subscribe`                       | `subscribeToNewsletter`         | Absent    |
| Newsletter | `POST /newsletter/unsubscribe`                     | `unsubscribeFromNewsletter`     | Absent    |
| Newsletter | `POST /newsletter/webhook`                         | `receiveNewsletterWebhook`      | Absent    |

Controller evidence: [core Auth][auth-controller], [passkeys][passkey-controller],
[form definitions][forms-controller], [submissions][submissions-controller],
[profiles][identity-controller], [subscription][subscription-controller],
[native newsletter actions][native-controller] and [webhook][webhook-controller].

### Reference discrepancies and configuration boundaries

These are 3-tier baseline observations to reconcile in contracts #3, not proven
DDD divergences or authorizations to change the shared wire contract.

| Reference                            | Observation                                                                                                                                    | DDD treatment / follow-up                                                                                                        |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| `POST /auth/jwt/login`               | Declared by [JWT controller][jwt-controller]; absent from committed OpenAPI and operation map                                                  | Absent; canonical inclusion and ID pending                                                                                       |
| `POST /auth/jwt/logout`              | Declared by the same optional controller; absent from committed OpenAPI and operation map                                                      | Absent; canonical inclusion and ID pending                                                                                       |
| `POST /auth/jwt/refresh`             | Declared by the same optional controller; absent from committed OpenAPI and operation map                                                      | Absent; canonical inclusion and ID pending                                                                                       |
| `POST /auth/refresh-tokens/{userId}` | `refreshTokens` remains in the operation map, but no matching controller route or committed OpenAPI operation was found                        | Metadata-only candidate discrepancy; do not invent a DDD endpoint or equate it with `/auth/jwt/refresh`                          |
| Auth passkeys / JWT                  | [Presentation module][auth-module] mounts them only when their respective plugins are enabled                                                  | Specify capability/configuration explicitly; core Auth support cannot imply either optional feature                              |
| Newsletter actions / webhook         | [Presentation module][newsletter-module] gates native actions and webhook independently, supports a configurable path, and requires Fastify    | Record the selected configuration and adapter; do not infer all four operations or adapter portability from subscription support |
| OpenAPI generation host              | [Generator][legacy-generator] mounts reference controllers with stub services, includes passkeys and all newsletter controllers, and omits JWT | Artifact presence alone does not prove host mounting, persistence, authorization or HTTP runtime behavior                        |

## Mapping into DDD layers

The following destinations are **planned responsibility mappings**, not existing
packages or approved capability commitments. 3-tier `libs/<context>/ts`, `nest`
and `angular` packages are reference surfaces only; DDD does not depend on them.

| Context and 3-tier source                       | DDD domain/application responsibilities to establish                                                                                   | Transport and adapter boundaries to establish                                                                                                                                                                                               |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Auth: `libs/auth/{ts,nest,angular}`             | Account/session use cases and application-owned engine, persistence and mail ports; pure business rules in `auth/ts/domain`            | Canonical Auth requests/results mapped in `auth/nest/presentation`; engine/session/passkey/JWT I/O through application ports implemented by infrastructure adapters; policy wire representation mapped without changing `auth-ts` semantics |
| Forms: `libs/forms/{ts,nest,angular}`           | Form-definition/version and submission use cases; application-owned persistence and notification ports                                 | Canonical form/payload/query/response mapping at presentation; persistence serialization in infrastructure; transport validation remains distinct from business invariants                                                                  |
| Identity: `libs/identity/{ts,nest,angular}`     | Profile creation/read/update and identity ownership rules; application-owned profile persistence port                                  | Canonical profile DTOs mapped to domain concepts; retain scalar Auth user references rather than runtime cross-context ORM relations                                                                                                        |
| Newsletter: `libs/newsletter/{ts,nest,angular}` | Subscription/consent and confirmation/unsubscription use cases; provider, persistence and mail ports as required by the selected slice | Canonical request/error mapping at presentation; provider/webhook signature and delivery I/O behind explicit boundaries; optional native/webhook features remain separate support claims                                                    |

For each selected context, local `ts/contracts` consumes/re-exports the immutable
canonical wire artifact; it does not become the domain model or author a second
schema set. Application ports live in `nest/application`, outbound adapters in
`nest/infrastructure-*`, inbound HTTP handling in `nest/presentation`, and wiring
in `nest/facade` or an `examples/` host. Angular consumers, if later selected,
retain `ui <- feature -> state -> data-access`, explicit state scope and canonical
transport consumption at data-access boundaries. This inventory adds none of these
packages, ports, adapters or mappings.

## Phased migration and dependency gates

| Phase                                       | Owner / work item                                                                                                          | Entry and exit evidence                                                                                                                                                                                      | Current position                                                                                                 |
| ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------- |
| 1. Establish ownership and inventory        | DDD #24 and #25                                                                                                            | Accepted ADR; pinned actual capability/route evidence and explicit gaps                                                                                                                                      | #24 merged; this document delivers #25                                                                           |
| 2. Freeze and publish the contract baseline | Contracts [#3][contracts-3], [#4][contracts-4], [#5][contracts-5], [#6][contracts-6], [#7][contracts-7], [#8][contracts-8] | Resolve reference discrepancies; define canonical domain packages and Zod operations; verify legacy compatibility; publish immutable packages/OpenAPI and conformance fixtures/tooling with release evidence | Required artifacts absent at audited revision                                                                    |
| 3. Select a real DDD implementation slice   | DDD maintainers; explicit implementation prerequisite to #26                                                               | Select specific operations and a bounded context; establish domain/application behavior, adapters and a runnable host with layer tests; record deferred operations                                           | No existing capability can be wired; implementation work must be explicitly scoped rather than inferred from #26 |
| 4. Adopt canonical versions                 | DDD [#26][ddd-26], after contracts SI-1.7 ([#8][contracts-8]) and the selected implementation                              | Pin exact package versions/integrity; map requests, results and errors through the DDD boundaries; preserve audited wire behavior and optional-feature boundaries                                            | Pending prerequisites; do not claim completion with an empty consumer set                                        |
| 5. Verify and release supported slices      | DDD [#27][ddd-27], after #26                                                                                               | Canonical OpenAPI comparison plus real HTTP positive/negative/security fixtures at the same version as 3-tier; packed-consumer and release compatibility evidence; update each operation's support record    | No runtime evidence exists; all capabilities remain unsupported                                                  |

New capability implementation requires its own reviewed scope, release-group
configuration, docs/examples and build/lint/test coverage under the repository's
onboarding rule. Choose one bounded context and a coherent operation slice at a
time; this audit does not prescribe implementing all 29 reference routes or silently
expand #26 into full parity. Until an implementation is selected, retain the absent
entries and report that dependency when starting code adoption.

Keep Nest 12 as the feature-development target and Nest 11 patch/security-only.
Verify actual runtime and adapter availability for the selected slice independently
of Nx tooling versions. Contract adoption is not an auth-provider replacement or
permission to migrate sessions, remove compatibility APIs or overwrite releases.
Use a reviewed versioned migration for breaks; rollback to the last compatible
immutable artifact once one exists.

## Maintaining the inventory and claiming support

When upstream or DDD revisions change, rerun the source/project inventory and
reconcile these reference rows against the released canonical operation set. Add
new operations explicitly and retain an explanation for removed or renamed ones.
Do not turn reference route counts into a permanent conformance expectation.

Before changing an operation from absent/unverified to supported, record:

1. Canonical package name, exact version, integrity/release evidence and operation ID.
2. DDD implementation revision, application use case/port, presentation mapper,
   outbound adapters and composition root, with actual file paths.
3. Host, runtime/HTTP adapter, route prefix, authentication and optional-feature configuration.
4. OpenAPI comparison and real HTTP positive, negative and security evidence,
   covering relevant status/error shapes, validation, authorization, cookies/headers,
   pagination, token/replay or webhook behavior; schema comparison alone is insufficient.
5. DDD package release/compatibility evidence and explicit unsupported operations.

This documentation-only change adds no runtime API, port, adapter, composition
wiring or boundary mapper. It causes no breaking package change and warrants no
npm deprecation. Completing #25 does not complete adoption or close epic #23.

## Related

- [Alignment with Bricks 3-tier](alignment-with-bricks-3tier.md)
- [Migration from Bricks 3-tier](migration-from-bricks-3tier.md)
- [Canonical contract consumption ADR](../adr/0006-canonical-public-api-contract-consumption.md)

[contract-releases]: https://github.com/anarchitects/anarchitecture-contracts/blob/44e4db89367521a97f1d4a4917ae2baae880fe15/docs/releases.md
[legacy-openapi]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/docs/openapi/openapi.json
[route-metadata]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/tools/api-specs/route-metadata.ts
[legacy-generator]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/tools/api-specs/generate.ts
[auth-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/auth/nest/src/presentation/controllers/auth.controller.ts
[passkey-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/auth/nest/src/presentation/controllers/passkey-auth.controller.ts
[forms-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/forms/nest/src/presentation/controllers/forms.controller.ts
[submissions-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/forms/nest/src/presentation/controllers/submissions.controller.ts
[identity-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/identity/nest/src/presentation/controllers/user-profiles.controller.ts
[subscription-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/newsletter/nest/src/presentation/newsletter-subscription.controller.ts
[native-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/newsletter/nest/src/presentation/newsletter-native.controller.ts
[webhook-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/newsletter/nest/src/presentation/newsletter-webhook.controller.ts
[jwt-controller]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/auth/nest/src/infrastructure-engine/better-auth/plugins/jwt/jwt-auth-plugin.controller.ts
[auth-module]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/auth/nest/src/presentation/presentation.module.ts
[newsletter-module]: https://github.com/anarchitects/anarchitecture-bricks-3tier/blob/ecda1b6e0d9bc39c66373452a92dce91e06abee0/libs/newsletter/nest/src/presentation/newsletter-presentation.module.ts
[contracts-3]: https://github.com/anarchitects/anarchitecture-contracts/issues/3
[contracts-4]: https://github.com/anarchitects/anarchitecture-contracts/issues/4
[contracts-5]: https://github.com/anarchitects/anarchitecture-contracts/issues/5
[contracts-6]: https://github.com/anarchitects/anarchitecture-contracts/issues/6
[contracts-7]: https://github.com/anarchitects/anarchitecture-contracts/issues/7
[contracts-8]: https://github.com/anarchitects/anarchitecture-contracts/issues/8
[ddd-26]: https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/26
[ddd-27]: https://github.com/anarchitects/anarchitecture-bricks-ddd/issues/27
