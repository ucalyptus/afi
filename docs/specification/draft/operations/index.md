---
title: Operations
description: The four core AFI operations.
---

# Operations

AFI defines four core operations. All four are required at
[Level 2 conformance](../conformance/profiles.md); providers at lower
levels omit some.

| Operation | Purpose | Required at |
|---|---|---|
| [`stat`](stat.md) | Metadata for one path | Level 0+ |
| [`list`](list.md) | Directory contents | Level 0+ |
| [`read`](read.md) | File contents | Level 1+ |
| [`search`](search.md) | Locate strings/patterns/vectors across the tree | Level 2+ |

Level 3 (mutation) adds `write`, `mkdir`, `delete`, `move` — deferred to
AEP-0002.

## Common conventions

All operations share these conventions:

- **Paths** are POSIX-style absolute paths. See [Path model](../path-model.md).
- **Errors** are structured per [Errors](../basic/errors.md).
- **Pagination** uses opaque `cursor` strings. Providers MUST make cursors
  stable for the lifetime of a session but MAY invalidate them across
  sessions.
- **Unknown fields** on responses MUST be tolerated by clients.
