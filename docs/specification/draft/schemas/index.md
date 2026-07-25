---
title: Schemas
description: Shared data types used across operations.
---

# Schemas

Two data types appear across multiple operations:

- **[`Entry`](entry.md)** — metadata for one file, directory, or symlink.
  Returned by [`stat`](../operations/stat.md) and inside
  [`list`](../operations/list.md) responses.
- **[`Match`](match.md)** — one search result. Returned inside
  [`search`](../operations/search.md) responses.

Both types support `x-` prefixed vendor extensions per
[Extensions & versioning](../basic/extensions.md).
