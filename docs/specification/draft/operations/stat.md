---
title: stat
description: Return metadata for one path.
---

# `stat`

Returns metadata for a single path.

## Signature

```typescript
stat(path: string) -> Entry | Error
```

## Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| `path` | string | yes | POSIX absolute path. See [Path model](../path-model.md). |

## Response

On success: an [`Entry`](../schemas/entry.md).

On failure: an [`Error`](../basic/errors.md).

## Error codes

| Code | When |
|---|---|
| `not_found` | Path does not exist OR caller lacks access (see [cloaking](../basic/access-control.md)) |
| `invalid_path` | Path is malformed or escapes root |

## Example

**Request:**

```
stat("/api-reference/users")
```

**Response:**

```json
{
  "path":         "/api-reference/users",
  "type":         "file",
  "size":         12847,
  "mtime":        "2026-07-20T14:22:11Z",
  "ctime":        "2026-06-15T09:01:03Z",
  "content_type": "text/markdown",
  "etag":         "\"a3f2b1c9\""
}
```

## Semantics

- `stat` on the root path `/` MUST succeed on any provider, returning an
  `Entry` with `type: "directory"`.
- `stat` MUST NOT follow symlinks. To resolve a symlink, `stat` its
  `target` explicitly.
- `stat` is a read-only operation. It MUST NOT mutate provider state.

## Idempotency and caching

`stat` is safe to cache by `path` for the lifetime indicated by the
provider's `etag` semantics (opaque to AFI). Clients MAY re-issue `stat`
freely.
