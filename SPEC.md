# Agent Filesystem Interface — Specification

**Version:** 0.1 (Draft)
**Status:** Request for comment
**License:** Apache-2.0

## 1. Overview

AFI defines an abstract interface for exposing arbitrary data sources to AI
agents as a POSIX-shaped filesystem. Implementations of this interface are
called **AFI providers**. Software that consumes providers is called an **AFI
client** (typically an agent runtime).

AFI is transport-agnostic; §7 specifies concrete bindings for MCP, HTTP, FUSE,
and SSH. Any binding that preserves the semantics defined here is conformant.

## 2. Terminology

The key words MUST, MUST NOT, SHOULD, SHOULD NOT, and MAY in this document are
to be interpreted as described in [RFC 2119].

- **Provider** — a system that implements AFI operations
- **Client** — an agent runtime (or human tool) that calls AFI operations
- **Entry** — a file, directory, or symlink metadata record
- **Capability** — an authorization token scoping a client's access
- **Profile** — a declared conformance level (§8)

## 3. Path model

- All paths are POSIX-style, absolute, using `/` as separator
- Providers expose exactly one virtual root at `/`
- Path components MUST NOT contain `\0` or `/`
- Providers MAY reserve the `/.afi/` prefix for metadata (see §6)
- Case sensitivity is provider-declared in the manifest and MUST be uniform
  across the tree

## 4. Core operations

### 4.1 `stat`

```
stat(path: string) -> Entry | NotFound
```

Returns metadata for a single path. See §5.1 for the `Entry` schema.

### 4.2 `list`

```
list(path: string, opts?: {
  limit?:  uint32,    // default 1000, provider MAY cap
  cursor?: string,    // pagination
  glob?:   string,    // fnmatch pattern applied to child names
}) -> {
  entries:      Entry[],
  next_cursor?: string,
}
```

Lists direct children of a directory. `path` MUST be a directory; providers
MUST return `Error{code: "invalid_path"}` otherwise. Ordering is lexicographic
by `path` unless the provider declares otherwise in the manifest.

### 4.3 `read`

```
read(path: string, opts?: {
  offset?: uint64,
  length?: uint64,
}) -> {
  bytes:        bytes,
  content_type: string,
  eof:          boolean,
}
```

Reads file content, optionally a byte range. Providers MUST return `content_type`
consistent with the `Entry.content_type` returned by `stat`.

### 4.4 `search`

```
search(query: SearchQuery, opts?: {
  path?:   string,    // scope to subtree, default "/"
  limit?:  uint32,    // default 100
  cursor?: string,
}) -> {
  matches:      Match[],
  next_cursor?: string,
}

SearchQuery =
  | string                                          // substring, case-sensitive
  | { regex:    string, flags?:     string }        // regex per ECMAScript
  | { semantic: string, threshold?: number }        // vector similarity
```

Providers MUST support plain-string search. Providers SHOULD support at least
one of `regex` or `semantic` and MUST declare supported modes in the manifest.

## 5. Data schemas

### 5.1 `Entry`

```
Entry {
  path:         string
  type:         "file" | "directory" | "symlink"
  size:         uint64              // 0 for directories
  mtime:        string              // RFC 3339
  ctime:        string              // RFC 3339
  content_type: string              // IANA MIME, e.g. "text/markdown"
  etag?:        string
  target?:      string              // symlink target, when type=symlink
  attributes?: {
    provenance?:  string            // source URL / DB id / etc
    permissions?: string[]          // capabilities required to access
    [key: `x-${string}`]: any       // vendor-specific
  }
}
```

### 5.2 `Match`

```
Match {
  path:     string
  line:     uint32                  // 1-indexed
  column?:  uint32                  // 1-indexed
  text:     string                  // matched line content
  context?: {
    before: string[],
    after:  string[],
  }
  score?:   number                  // 0.0–1.0, present iff semantic mode
}
```

## 6. Discovery: `/.afi/manifest.json`

Providers MUST serve a manifest at `/.afi/manifest.json`:

```json
{
  "afi_version":      "0.1",
  "provider":         "chromafs",
  "provider_version": "1.2.0",
  "profile":          2,
  "capabilities":     ["stat", "list", "read", "search"],
  "search": {
    "modes":       ["exact", "regex", "semantic"],
    "max_results": 100
  },
  "path": {
    "case_sensitive": true,
    "max_length":     4096
  },
  "auth": {
    "required": false,
    "methods":  ["bearer", "capability"]
  }
}
```

Clients MUST fetch the manifest before issuing other requests and MUST honor
the declared `profile` and `capabilities`.

## 7. Transport bindings

### 7.1 MCP binding (RECOMMENDED)

Providers SHOULD expose operations as MCP tools with these names and input
schemas mirroring §4:

- `afi.stat({path})`
- `afi.list({path, limit?, cursor?, glob?})`
- `afi.read({path, offset?, length?})`
- `afi.search({query, path?, limit?, cursor?})`

Errors returned as MCP tool errors with structured `code`, `message`, `path`.

### 7.2 HTTP/JSON binding

- `GET  /afi/stat?path=…`
- `GET  /afi/list?path=…&limit=…&cursor=…&glob=…`
- `GET  /afi/read?path=…&offset=…&length=…` — returns raw bytes with
  `Content-Type` header; range signaled via `Content-Range` when partial
- `POST /afi/search` — JSON body

`/.afi/manifest.json` is served at that literal URL.

### 7.3 FUSE binding

Providers targeting local mounts SHOULD map operations to FUSE callbacks:
`stat → getattr`, `list → readdir`, `read → read`. `search` is exposed via a
synthetic control file at `/.afi/search` accepting JSON writes and yielding
NDJSON reads, or via an ioctl if the platform supports it.

### 7.4 SSH binding

Providers targeting the `ssh $host` UX (per Supabase's model) SHOULD wrap
[`just-bash`](https://github.com/vercel-labs/just-bash) or equivalent and
implement its `IFileSystem` interface against AFI operations. The manifest is
served as a synthetic `/.afi/manifest.json` file.

## 8. Conformance profiles

| Level | Name       | Required operations                                         |
|-------|------------|-------------------------------------------------------------|
| 0     | Discovery  | `stat`, `list`                                              |
| 1     | Read       | Level 0 + `read`                                            |
| 2     | Search     | Level 1 + `search` (exact mode minimum)                     |
| 3     | Read/Write | Level 2 + `write`, `mkdir`, `delete`, `move` (see RFC 0002) |

Providers MUST declare their level in the manifest as `profile`.

Level 3 operations are defined in [RFC 0002 — Mutation](./rfcs/) (draft,
placeholder). Until 0002 lands, Level 3 is provider-defined and non-portable.

## 9. Access control

- Every request MAY carry a capability token (transport-defined; e.g.
  `Authorization: Bearer …` for HTTP, session-scoped for MCP)
- Providers MUST filter `list`, `stat`, and `search` results such that entries
  the caller lacks access to do not appear
- Providers MUST NOT distinguish "not found" from "not authorized" in responses
  for paths the caller cannot see — return `not_found` in both cases (§10)
- Providers MUST reject `read` on inaccessible paths with `not_found`

## 10. Errors

Errors are structured:

```
Error {
  code:         "not_found" | "invalid_path" | "unsupported"
              | "rate_limited" | "internal" | "unauthorized" | "conflict"
  message:      string
  path?:        string
  retry_after?: number    // seconds, for rate_limited
}
```

Providers MUST use `unsupported` for operations outside their declared profile.

## 11. Extensions

- Vendor-specific fields on `Entry.attributes` MUST use the `x-` prefix
- Vendor-specific manifest fields MUST use the `x-` prefix
- New standard fields require an RFC (see [GOVERNANCE.md](./GOVERNANCE.md))
- Clients MUST tolerate unknown fields (forward compatibility)

## 12. Versioning

- SemVer for the spec
- Providers declare the highest AFI version they implement in `afi_version`
- Breaking changes require a major version bump and an RFC

---

## Appendix A — Reference: mapping to bash commands

| bash                    | AFI                                     |
|-------------------------|-----------------------------------------|
| `ls PATH`               | `list(PATH)`                            |
| `cat PATH`              | `read(PATH)`                            |
| `stat PATH`             | `stat(PATH)`                            |
| `grep -r STR PATH`      | `search(STR, {path: PATH})`             |
| `grep -E RE PATH`       | `search({regex: RE}, {path: PATH})`     |
| `find PATH -name GLOB`  | `list(PATH, {glob: GLOB})` recursively  |
| `head -c N PATH`        | `read(PATH, {length: N})`               |

## Appendix B — Non-goals for v0.1

- Streaming subscriptions / watch semantics (planned for v0.2)
- Cross-provider federation (planned for v0.3)
- Content-addressed storage semantics (out of scope)
- Structured (non-text) result formats (out of scope; use `attributes`)

[RFC 2119]: https://www.rfc-editor.org/rfc/rfc2119
