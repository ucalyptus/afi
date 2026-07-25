---
title: Turso AgentFS
description: How AgentFS can be exposed as an AFI Level 3 provider.
---

# Provider: Turso AgentFS

Informative adapter note. Not part of the normative spec.

- **System:** [tursodatabase/agentfs](https://github.com/tursodatabase/agentfs)
- **Backing store:** SQLite (per AgentFS SPEC.md v0.4)
- **Native transport:** FUSE (Linux) / NFS (macOS) + Rust / TypeScript / Python SDKs
- **Conformance target:** Level 3 (read/write)
- **Auth:** filesystem-native + optional encryption key

## Manifest

```json
{
  "afi_version":      "0.1",
  "provider":         "agentfs",
  "provider_version": "0.4",
  "profile":          3,
  "capabilities":     ["stat", "list", "read", "search", "write", "mkdir", "delete", "move"],
  "search":           { "modes": ["exact", "regex"], "max_results": 1000 },
  "path":             { "case_sensitive": true, "max_length": 4096 },
  "auth":             { "required": false, "methods": ["capability"] },
  "x-agentfs-db":     "sqlite:///path/to/agent.db"
}
```

## Operation mapping

| AFI | AgentFS |
|---|---|
| [`stat(path)`](../specification/draft/operations/stat.md) | `SELECT … FROM fs_inode JOIN fs_dentry WHERE path = ?` |
| [`list(path)`](../specification/draft/operations/list.md) | `SELECT … FROM fs_dentry WHERE parent_ino = ?` |
| [`read(path, off, len)`](../specification/draft/operations/read.md) | Assemble chunks from `fs_data` where `ino = ?` in `chunk_size` slices |
| [`search(str)`](../specification/draft/operations/search.md) | LIKE scan over `fs_data` for text/* content types |
| `write(path, bytes)` | Insert inode + dentry + chunked `fs_data` rows |
| `mkdir(path)` | Insert directory inode + dentry |
| `delete(path)` | Decrement `nlink`, delete dentry, GC unreferenced inodes |
| `move(src, dst)` | UPDATE `fs_dentry` row |

The `tool_calls` and KV tables in AgentFS's spec fall outside AFI's scope
(they're agent runtime concerns, not filesystem concerns) and remain as
AgentFS-specific `x-` extensions.

## Notes

- AgentFS's inode/dentry design maps cleanly onto AFI's [`Entry`](../specification/draft/schemas/entry.md) schema.
- Chunked `fs_data` gives natural byte-range `read` support.
- AFI adds structured `search` semantics that AgentFS v0.4 does not
  currently standardize — an AFI adapter over AgentFS would provide the
  reference implementation.
- The AFI adapter would be a thin translation layer, not a rewrite.

## Deployment posture

Level 3 conformance is aspirational until AEP-0002 nails down the
mutation operations. Until then, AgentFS-as-AFI is best deployed at
Level 2 (search-capable read-only) if strict AFI-conformant portability
matters.
