---
title: search
description: Locate strings, patterns, or vectors across the tree.
---

# `search`

Locates matches for a query across the tree. Three query modes: exact
substring (required), regex (recommended), and semantic (optional).

## Signature

```typescript
search(query: SearchQuery, opts?: {
  path?:   string          // scope to subtree, default "/"
  limit?:  uint32          // default 100
  cursor?: string
}) -> {
  matches:      Match[]
  next_cursor?: string
} | Error

SearchQuery =
  | string                                          // substring, case-sensitive
  | { regex:    string, flags?: string }            // regex per ECMAScript
  | { semantic: string, threshold?: number }        // vector similarity
```

## Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| `query` | `SearchQuery` | yes | See modes below |
| `opts.path` | string | no | Scope to a subtree. Default: `/` |
| `opts.limit` | uint32 | no | Max matches to return |
| `opts.cursor` | string | no | Pagination cursor |

## Query modes

### Exact (required at Level 2)

```typescript
search("Retry-After", { path: "/rfc-drafts" })
```

Substring match. Case-sensitive by default. Providers MUST support this mode.

### Regex

```typescript
search({ regex: "TODO\\(.*\\)", flags: "i" }, { path: "/notes" })
```

Pattern per ECMAScript regex (compatible with Rust `regex` crate and Go
`regexp`). `flags` is a subset of `["i", "m", "s", "u"]`. Providers that
support regex MUST declare `"regex"` in `manifest.search.modes`.

### Semantic

```typescript
search({ semantic: "how do I authenticate", threshold: 0.7 }, {})
```

Vector similarity against the provider's embedding index. Match `score`
(a value in `[0, 1]`) MUST be present on results. Providers that support
semantic search MUST declare `"semantic"` in `manifest.search.modes`.

## Response

```typescript
{
  matches:      Match[]      // ordered per §Ordering below
  next_cursor?: string       // present iff more matches exist
}
```

See [`Match`](../schemas/match.md) for the match schema.

## Error codes

| Code | When |
|---|---|
| `unsupported` | Query mode not declared in `manifest.search.modes` |
| `invalid_path` | `opts.path` is malformed or escapes root |
| `not_found` | `opts.path` does not exist OR caller lacks access |
| `rate_limited` | Provider throttling; `retry_after` MUST be set |
| `internal` | Backing store failure |

## Ordering

- **Exact and regex modes:** results are ordered lexicographically by
  `(path, line)`.
- **Semantic mode:** results are ordered by `score` descending.
- Providers MAY declare an alternate ordering via
  `manifest.search.x-ordering` but MUST NOT do so silently.

## Access control

Matches for inaccessible paths MUST be omitted from `matches` per the
[cloaking rule](../basic/access-control.md). This filtering MUST happen
before pagination so `next_cursor` does not reveal filtered-out counts.

## Provider guidance

Providers SHOULD:

- Cap `limit` at a reasonable value (typically 100–500)
- Return snippets in `Match.text` short enough to fit in an LLM context
  budget (e.g., ≤ 256 characters per snippet)
- Include a small amount of `context.before` and `context.after` when
  cheap to compute — helps the agent understand match relevance without
  a follow-up `read`
