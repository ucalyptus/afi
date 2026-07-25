# Provider: Mintlify ChromaFs

Informative adapter note. Not part of the normative spec.

- **System:** ChromaFs (Mintlify internal; not open-source at time of writing)
- **Backing store:** Chroma vector database (docs already indexed for search)
- **Native transport:** In-process via [`just-bash`](https://github.com/vercel-labs/just-bash)
  `IFileSystem` interface
- **Conformance target:** Level 2 (search)
- **Auth:** per-user capability token; path tree is pruned per session

## Manifest

```json
{
  "afi_version":      "0.1",
  "provider":         "chromafs",
  "provider_version": "1.x",
  "profile":          2,
  "capabilities":     ["stat", "list", "read", "search"],
  "search": {
    "modes":       ["exact", "regex", "semantic"],
    "max_results": 100
  },
  "path":             { "case_sensitive": true, "max_length": 512 },
  "auth":             { "required": true,  "methods": ["capability"] }
}
```

## Operation mapping

| AFI                    | ChromaFs                                                              |
|------------------------|-----------------------------------------------------------------------|
| bootstrap              | Fetch `__path_tree__` gzipped JSON document, decompress into in-memory `Set<path>` and `Map<dir, children>` |
| `stat(path)`           | Membership check in the in-memory path set + tree metadata            |
| `list(path)`           | In-memory `Map<dir, children[]>` lookup (no network)                  |
| `read(path)`           | `collection.get(ids=[path])` → assemble chunks in order               |
| `search(str)`          | Chroma query with `where_document={"$contains": str}`                 |
| `search({semantic})`   | Chroma similarity query on the docs collection                        |

## Access control

ChromaFs prunes the path tree by capability *before* building it, so
unauthorized entries never appear. This matches AFI §9 exactly: `not_found`
is returned uniformly for missing and unauthorized paths.

## Notes

- ChromaFs is the origin case for the AFI shape — everything AFI standardizes
  matches ChromaFs's operational surface
- Because docs are read-only, ChromaFs targets Level 2, not Level 3
- The in-memory bootstrap makes `list`/`stat` sub-millisecond; only `read` and
  `search` hit Chroma
- Session-scoped capability pruning is a good reference implementation of §9
