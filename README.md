# pharolith/harness

Thin Composer wrapper around the native Pharolith binary.

This public package is generated from a single source release. It carries the
CLI and MCP bridge for every supported platform; installation performs no
scripts or runtime downloads. `SHA256SUMS` records every native executable.

```bash
composer require --dev pharolith/harness
vendor/bin/pharolith init
vendor/bin/pharolith status
```

The wrapper holds no coordination logic. It resolves the platform binary,
runs it, forwards stdio, and propagates the exit code.

## MCP setup

Package includes bridge and automatic setup for Codex, Claude Code and Cursor:

```bash
composer require --dev pharolith/harness
vendor/bin/pharolith setup
```

Setup works before project initialization, copies bridge to stable user path,
self-checks it, configures all supported clients using absolute path, and
verifies results. It is explicit and idempotent. Preview with
`vendor/bin/pharolith setup --dry-run`. Full fallback manual:
[MCP_SETUP.md](MCP_SETUP.md).

## Completion quality

The native harness preserves each new write task's acceptance outcomes, maps
them to implementation and recorded gates, and refuses completion with missing,
failed, stale or modified evidence. CLI, MCP and stop hooks use the same checks.
Canonical skills and their focused references install with the package.

```bash
vendor/bin/pharolith quality show <task> --json
vendor/bin/pharolith quality set <task> --file contract.json
vendor/bin/pharolith quality scan <task>
vendor/bin/pharolith verify <task>
```

UI tasks also require product/state contracts and actual rendered review material.
`quality review` records reviewer-supplied evidence with integrity and freshness;
agents still render, interact and assess the interface.

## Binary resolution

In order:

1. `PHAROLITH_BINARY` — an explicit path, which wins over everything.
2. `vendor-bin/<platform>-<arch>/pharolith` bundled inside this package.
3. `.pharolith/bin/pharolith`, or an npm platform package, in this project or a
   parent. These remain compatibility fallbacks; the published Composer package
   always includes its own native builds.
4. `pharolith` on `PATH`, ignoring Composer's own `vendor/bin` shims so the
   wrapper can never call itself.

If none of those exist the wrapper exits `127` and says what it looked for.

## PHP only

This package requires PHP 8.1 and nothing else. It does not require Node, and
it resolves the same native binary the npm wrapper uses.
