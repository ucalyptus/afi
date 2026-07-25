---
title: list
description: List direct children of a directory.
---

# `list`

Lists direct children of a directory. Depth-1 only; clients that need
recursive traversal must walk trees themselves.

## Signature

```typescript
list(path: string, opts?: {
  limit?:  uint32          // default 1000, provider MAY cap
  cursor?: string          // opaque pagination cursor
  glob?:   string          // fnmatch pattern applied to child names
}) -> {
  entries:      Entry[]
  next_cursor?: string     // present iff more results exist
} | Error
```

## Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| `path` | string | yes | Must resolve to a directory |
| `opts.limit` | uint32 | no | Maximum entries to return. Providers MAY cap this. |
| `opts.cursor` | string | no | Continue from a previous page |
| `opts.glob` | string | no | [fnmatch](https://man7.org/linux/man-pages/man7/glob.7.html) pattern applied to child names only (not full paths) |

## Response

```typescript
{
  entries:      Entry[]
  next_cursor?: string     // absent iff this is the final page
}
```

Entries in `entries` are child paths of the requested directory. Ordering
is lexicographic by `path` unless the provider declares otherwise in the
manifest.

## Error codes

| Code | When |
|---|---|
| `not_found` | Path does not exist OR caller lacks access |
| `invalid_path` | Path is `type: "file"` (not a directory), or syntactically invalid |

## Example

**Request:**

```
list("/api-reference", { glob: "*.md", limit: 3 })
```

**Response:**

```json
{
  "entries": [
    {
      "path":         "/api-reference/create-customer.md",
      "type":         "file",
      "size":         4213,
      "mtime":        "2026-07-19T11:00:00Z",
      "ctime":        "2026-05-01T00:00:00Z",
      "content_type": "text/markdown"
    },
    {
      "path":         "/api-reference/delete-customer.md",
      "type":         "file",
      "size":         2104,
      "mtime":        "2026-07-19T11:00:00Z",
      "ctime":        "2026-05-01T00:00:00Z",
      "content_type": "text/markdown"
    },
    {
      "path":         "/api-reference/list-customers.md",
      "type":         "file",
      "size":         5321,
      "mtime":        "2026-07-19T11:00:00Z",
      "ctime":        "2026-05-01T00:00:00Z",
      "content_type": "text/markdown"
    }
  ],
  "next_cursor": "eyJvZmZzZXQiOjN9"
}
```

## Semantics

- `list` returns direct children only. Recursion is a client concern.
- `list("/")` MUST succeed on any provider (subject to the cloaking rule
  if the root's children are all inaccessible — return an empty
  `entries[]`).
- `glob` matches child names, not full paths. `list("/docs", {glob: "*.md"})`
  filters on the segment after `/docs/`, not on the whole child path.
- Access-controlled entries MUST be omitted from `entries` without
  affecting `next_cursor` semantics — see
  [Access control → Filtering at query time](../basic/access-control.md#filtering-at-query-time).

## Pagination

- Clients loop until `next_cursor` is absent.
- Cursors are opaque strings; clients MUST NOT attempt to decode them.
- Providers MUST keep cursors valid for at least the session but MAY
  invalidate them across sessions or configuration changes.
