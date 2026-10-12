# `pharolith/harness`

Thin Composer wrapper around Pharolith's native CLI and MCP bridge. PHP scripts
resolve a bundled native executable, forward stdio and propagate its status;
coordination remains in the Rust binary.

## Requirements and install

PHP 8.1 or newer with `proc_open` available. The generated public distribution
contains CLI and bridge pairs for all supported targets plus `SHA256SUMS`; it
does not require Node, run install scripts or download a runtime at execution.

```bash
composer require --dev pharolith/harness
vendor/bin/pharolith setup
vendor/bin/pharolith init
vendor/bin/pharolith status
```

Run `setup` once per machine to configure Codex, Claude Code and Cursor. Preview
with `vendor/bin/pharolith setup --dry-run`. Run `init` per invited project.
Setup is explicit and idempotent; it preserves unrelated client settings and
reports reload requirements.

## Resolution and bridge

The Composer CLI wrapper resolves its explicit `PHAROLITH_BINARY` override and
the generated platform-native locations before compatible `PATH` entries,
ignoring Composer's own `vendor/bin` shim. The Composer bridge launcher also
accepts the override, then resolves its native pair. The standalone native host
applies its own root and executable precedence; use setup self-check output when
debugging a mismatch.

Missing resolution exits 127; execution failure exits 126; signal termination
exits 128; otherwise the native child's exit status is returned.

## MCP and quality

```bash
vendor/bin/pharolith mcp
vendor/bin/pharolith agent-boot --json
vendor/bin/pharolith quality show <task> --json
vendor/bin/pharolith verify <task>
```

The native harness provides scoped leases, handoff, source-bound validation,
canonical skills, ADR/docs gates and UI review contracts. Rendered UI evidence
is reviewer-supplied and freshness-checked; the wrapper does not operate a
browser or claim visual quality by itself.

## Source versus public release

This directory is the Composer source template. The public generated repository
contains the six platform CLI/bridge pairs and checksums described by
`docs/contracts/public-distribution.md`. Source release version is 0.15.0;
published registry availability must be checked separately.
