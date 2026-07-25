---
title: Discovery manifest
description: The /.afi/manifest.json document.
---

# Discovery manifest

Every AFI provider MUST serve a manifest describing itself.

## Location

- **HTTP transport:** `GET /.afi/manifest.json`
- **MCP transport:** the tool `afi.manifest()` returns the manifest object,
  and the file is *also* readable via `afi.read({path: "/.afi/manifest.json"})`
- **FUSE transport:** readable at `/.afi/manifest.json` in the mounted root
- **SSH transport:** readable via `cat /.afi/manifest.json` from the shell

Clients MUST fetch the manifest before issuing any other AFI operation. They
MAY cache it for the lifetime of a session.

## Schema

```typescript
Manifest {
  afi_version:      string        // e.g. "0.1", the AFI spec version this provider targets
  provider:         string        // short provider identifier, e.g. "chromafs"
  provider_version: string        // provider's own SemVer, opaque to AFI
  profile:          0 | 1 | 2 | 3 // declared conformance profile
  capabilities:     string[]      // operations supported (must be consistent with profile)
  search: {
    modes:          ("exact" | "regex" | "semantic")[]
    max_results:    uint32
  }
  path: {
    case_sensitive: boolean
    max_length:     uint32
  }
  auth: {
    required:       boolean
    methods:        ("bearer" | "capability" | string)[]
  }
  [key: `x-${string}`]: any       // vendor extensions
}
```

## Example

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
    "required": true,
    "methods":  ["capability"]
  }
}
```

## Semantics

- `afi_version` MUST be a version this provider fully implements. A
  provider MAY implement multiple versions by serving multiple manifests
  at different endpoints; each endpoint MUST advertise exactly one version.
- `capabilities` MUST contain, at minimum, the operations required by the
  declared `profile` — see [Profiles](../conformance/profiles.md).
- `search.modes` MUST contain `"exact"` for any provider at Level 2 or
  higher.
- `auth.methods` is a hint for clients. Providers MAY accept more methods
  than they advertise but MUST accept at least all advertised methods.

## Client obligations

Clients MUST:

- Fetch the manifest before issuing other operations.
- Refuse to call operations not present in `capabilities`, returning
  `Error{code: "unsupported"}` client-side without contacting the provider.
- Tolerate unknown fields on the manifest.
- Warn (not error) when connecting to a provider whose `afi_version` is
  lower than the client's declared target version.
