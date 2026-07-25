---
title: Entry
description: Metadata for one file, directory, or symlink.
---

# `Entry`

Metadata for one file, directory, or symlink.

## Schema

```typescript
Entry {
  path:         string              // POSIX absolute path
  type:         "file" | "directory" | "symlink"
  size:         uint64              // total bytes; 0 for directories
  mtime:        string              // RFC 3339 last-modified time
  ctime:        string              // RFC 3339 creation/change time
  content_type: string              // IANA MIME type
  etag?:        string              // opaque entity tag for caching
  target?:      string              // symlink target, present iff type=="symlink"
  attributes?: {
    provenance?:  string            // source URL, DB id, etc
    permissions?: string[]          // capabilities required to access this entry
    [key: `x-${string}`]: any       // vendor-specific
  }
}
```

## Field semantics

### `path`
Absolute POSIX path. See [Path model](../path-model.md) for canonicalization
rules.

### `type`
- `"file"` — a leaf with byte content readable via `read`
- `"directory"` — a node with children discoverable via `list`
- `"symlink"` — a reference; `target` MUST be present

### `size`
- For files: total size in bytes.
- For directories: `0`. Providers MUST NOT use this to indicate child
  count.
- For symlinks: `0`. Symlink metadata carries no size of its own; `stat`
  the `target` if you need the target's size.

### `mtime`, `ctime`
RFC 3339 timestamps. Providers SHOULD populate both accurately. If a
provider cannot distinguish creation from last-modified, it MUST use the
same value for both.

### `content_type`
IANA MIME type. For text files without a well-known MIME, providers
SHOULD use `text/plain`. For binary files, `application/octet-stream` is
the acceptable default. For directories, `content_type` MUST be
`inode/directory`.

### `etag`
An opaque string that changes iff the underlying entry changes. Clients
MAY use it as a cache key. Providers MAY omit it if change detection is
not supported.

### `target`
Only present when `type == "symlink"`. Value is a valid AFI path.

### `attributes`
An optional container for structured metadata beyond the standard fields.

- `attributes.provenance` — a URL, DB identifier, or human-readable
  string identifying where this entry came from. Useful for citing sources
  in agent responses.
- `attributes.permissions` — capability names required to access this
  entry. This is informational; the actual filtering happens per
  [Access control](../basic/access-control.md).
- Vendor-specific fields MUST use the `x-` prefix.

## Example

```json
{
  "path":         "/api-reference/users",
  "type":         "file",
  "size":         12847,
  "mtime":        "2026-07-20T14:22:11Z",
  "ctime":        "2026-06-15T09:01:03Z",
  "content_type": "text/markdown",
  "etag":         "\"a3f2b1c9\"",
  "attributes": {
    "provenance": "https://api.example.com/docs/users",
    "x-source-collection": "docs-v3"
  }
}
```
