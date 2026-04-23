---
name: monitor-ci
description: troubleshoot ci, workflow, build, test, lint, docs, contract-test, topology-doc, or release failures in anarchitecture-bricks-ddd. use when a github action, nx target, package validation flow, angular docs build, openapi check, or example-app validation fails and the task is specifically about diagnosing and fixing that repo without weakening its ddd or hexagonal architecture.
---

# Overview
Use this skill when CI or automation is failing in `anarchitecture-bricks-ddd`.

Inspect these sources first when relevant:

- workflow logs and failed job output
- `AGENTS.md`
- `README.md`
- relevant `docs/guides/*` and `docs/adr/*`
- affected package `README.md`

## Required workflow
1. Identify the failing workflow, job, and Nx target.
2. Reduce the failure to the smallest reproducible command.
3. Fix the root cause while preserving bounded-context, ports/adapters, and Angular/Nest layer rules.
4. Validate with package-manager-prefixed Nx commands.
5. Summarize the failure, fix, validation, and any follow-up impact on the 3-tier companion repo.

## Repo-specific checks
- Do not bypass DDD layers just to make CI green.
- Do not edit generated artifacts directly.
- If the failure involves Angular state rules, topology guidance, or 3-tier migration docs, mention that explicitly.
- Call out boundary changes, port/adapter changes, and any contract-surface changes in the final summary.
