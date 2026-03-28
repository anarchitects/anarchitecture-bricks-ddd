---
name: ddd-ports-and-adapters
description: Place ports in application and adapters in infrastructure in the DDD repo. USE WHEN designing integrations with auth engines, persistence, mail, queues, storage, or external services, or when cleaning up boundary violations where infrastructure owns an application-facing interface.
---

# DDD Ports And Adapters

Use this skill when introducing or cleaning up integration seams.

## Core Rule

- ports live in `application`
- adapters live in `infrastructure-*`
- composition roots wire them together

Do not place a port in an infrastructure folder just because the first implementation is persistence-backed or SDK-backed.

This is the concrete hexagonal architecture rule for the repo:

- `domain` and `application` are the inside of the hexagon
- primary adapters drive the application from the outside in
- secondary adapters are driven by the application through ports
- no adapter may become a dependency of the inside

## Third-Party Library Rule

The brick is the product.
The third-party library is the canonical internal engine.
Ports and adapters remain the override seam.

Examples:

- auth engine behind an auth-engine adapter
- TypeORM behind persistence adapters
- mail transport behind mailer adapters
- Nest controllers and Angular feature/state entry points as primary adapters over application use cases

## Placement Guidance

Put in `application`:

- repository contracts
- engine contracts
- mailer contracts
- transaction ports
- query/use-case contracts consumed by application services
- inbound use-case interfaces when multiple delivery adapters need the same application behavior

Put in `infrastructure-*`:

- TypeORM implementations
- Better Auth implementations
- Nodemailer implementations
- external API client implementations

## Smell Checks

Refactor when you see:

- application importing contracts from infrastructure folders
- config importing types from application unless they are neutral shared types
- presentation importing infrastructure services directly outside composition roots
- controllers or frontend orchestration calling SDKs or repositories directly instead of going through the application layer

## Output Expectations

When changing a port/adapter seam, document:

- who owns the contract
- who implements it
- where composition happens
- whether the public API changes
