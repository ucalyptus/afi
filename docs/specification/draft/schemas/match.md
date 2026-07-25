---
title: Match
description: One search result.
---

# `Match`

One search result returned inside a
[`search`](../operations/search.md) response.

## Schema

```typescript
Match {
  path:     string                  // path of the entry containing the match
  line:     uint32                  // 1-indexed line number
  column?:  uint32                  // 1-indexed column (present when known)
  text:     string                  // the matched line content (or snippet)
  context?: {
    before: string[]                // lines immediately before `text`
    after:  string[]                // lines immediately after `text`
  }
  score?:   number                  // 0.0–1.0, MUST be present iff query mode is "semantic"
  [key: `x-${string}`]: any         // vendor-specific
}
```

## Field semantics

### `path`
The path of the entry containing the match. MUST be a valid file (not a
directory), because AFI search returns text matches only.

### `line`
1-indexed line number where the match starts. Providers SHOULD produce
this from a consistent line-splitting algorithm (typically split-on-`\n`,
counting `\r\n` as one line ending).

### `column`
1-indexed column of the match start within `line`. Optional; providers
MAY omit if it's expensive to compute.

### `text`
The matched content. For exact and regex modes, this is typically the
full matched line (possibly truncated). For semantic mode, this is a
snippet the provider considers most representative of why this match
scored high.

Providers SHOULD keep `text` under ~256 characters to fit reasonably in
LLM context budgets. If truncation is needed, providers SHOULD mark it
with an ellipsis (`…`) at the truncation point.

### `context`
Optional lines around `text` for the agent to disambiguate the match
without a follow-up `read`.

- `context.before` — lines immediately preceding `text` (last element is
  the line just before `text`).
- `context.after` — lines immediately following `text` (first element is
  the line just after `text`).

Providers SHOULD include a small window (e.g., 2 lines on each side) when
cheap. Providers MAY omit `context` entirely if the backing store makes
it expensive.

### `score`
- **Required** when the query mode was `semantic`. Value in `[0.0, 1.0]`;
  higher is more relevant.
- **MUST be absent** when the query mode was exact or regex.
- Providers MUST NOT return matches with `score` below the query's
  `threshold` (default: implementation-defined, but sensible values are
  ≥ 0.5).

## Example — exact/regex match

```json
{
  "path":   "/rfc-drafts/rate-limiting.md",
  "line":   47,
  "column": 12,
  "text":   "The `Retry-After` header MUST be included in 429 responses.",
  "context": {
    "before": ["## Rate limits", ""],
    "after":  ["", "See also RFC 6585 §4."]
  }
}
```

## Example — semantic match

```json
{
  "path":  "/api-reference/auth.md",
  "line":  1,
  "text":  "Authentication in this API uses signed JWTs issued by /oauth/token…",
  "score": 0.87
}
```
