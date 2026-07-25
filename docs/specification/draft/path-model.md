---
title: Path model
description: How paths work in AFI.
---

# Path model

## Structure

- All paths are POSIX-style, using `/` as separator.
- All paths passed to and returned from AFI operations MUST be absolute
  (starting with `/`).
- Providers expose exactly one virtual root at `/`.

## Character rules

- Path components MUST NOT contain `\0` (null byte) or `/`.
- Providers MAY impose additional character restrictions and MUST declare
  them via the `path.disallowed_chars` field in the manifest (v0.2 feature;
  not required in v0.1).
- Providers MUST declare a maximum path length in `path.max_length` in the
  manifest.

## Case sensitivity

- Providers MUST declare `path.case_sensitive` in the manifest as `true` or
  `false`.
- The declared value MUST apply uniformly across the tree. A provider MUST
  NOT mix case-sensitive and case-insensitive regions.
- On a case-insensitive provider, clients MUST NOT rely on the returned
  casing of `path` fields matching the casing they requested.

## Reserved prefix

Providers MAY reserve the `/.afi/` prefix for AFI metadata. Notably:

- `/.afi/manifest.json` — the [discovery manifest](basic/discovery.md).
  This path MUST be readable via `read` on providers that support the HTTP
  or SSH transport.
- `/.afi/search` — a synthetic control file for search on the FUSE
  transport. See [FUSE binding](transports/fuse.md).

Providers MAY expose additional `/.afi/*` paths. Clients MUST tolerate the
presence of unknown paths under `/.afi/` without treating them as errors.

## Path equality

Two paths are considered equal iff, after normalization, they:

1. Consist of the same sequence of components,
2. Would compare equal under the provider's declared case sensitivity.

Normalization means: resolving `.` and `..` components (rejecting any that
would escape `/`), collapsing consecutive `/` characters, and stripping any
trailing `/` except on `/` itself.

Providers MUST reject paths containing `..` components that would escape
`/` with `Error{code: "invalid_path"}`.

## Symlinks

If a provider exposes symlinks (`type: "symlink"` on `Entry`):

- The `target` field on the `Entry` MUST be a valid AFI path.
- Clients MUST NOT assume follow-through semantics — they SHOULD explicitly
  `stat` the target if they need to know what kind of thing it points to.
- Providers MAY implement symlinks as pure metadata (no actual redirection)
  or as first-class references. This is provider-defined.
