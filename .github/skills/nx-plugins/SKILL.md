---
name: nx-plugins
description: Find and add Nx plugins. USE WHEN user wants to discover available plugins, install a new plugin, or add support for a specific framework or technology to the workspace.
---

## Finding and Installing new plugins

- List plugins: `pnpm nx list`
- Install plugins `pnpm nx add <plugin>`. Example: `pnpm nx add @nx/react`.

## Hexagonal + DDD Guardrails

When adding plugins, preserve architecture boundaries:

- Do not introduce plugin-driven framework dependencies into `domain` packages.
- Prefer configuring plugins for adapter layers (`presentation`, frontend, `infrastructure-*`) and tooling libraries.
- Keep `application` focused on use cases and ports, independent from framework internals where possible.
- If plugin generators create mixed concerns, refactor output into bounded context + layer structure before finalizing.
