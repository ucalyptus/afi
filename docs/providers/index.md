---
title: Providers
description: Adapter notes for existing systems that fit the AFI shape.
---

# Providers

Adapter notes for existing systems that can (or already do) fit the AFI
shape. Each page explains the mapping from AFI operations to that
system's native primitives.

!!! note "Informative, not normative"
    Provider adapter notes are informative. They document how existing
    systems can be exposed as AFI providers but are not part of the
    normative specification. Providers may or may not be maintained by
    the systems they describe.

## Existing sketches

| Provider | Backing store | Conformance target | Transport | Status |
|---|---|---|---|---|
| [Turso AgentFS](agentfs.md) | SQLite | Level 3 (read/write) | FUSE / NFS + native SDK | Adapter sketch |
| [Mintlify ChromaFs](chromafs.md) | Chroma vector DB | Level 2 (search) | In-process (just-bash) | Adapter sketch |
| [Supabase supabase.sh](supabase-sh.md) | Markdown files | Level 2 (search) | SSH + just-bash | Adapter sketch |
| [Cloudflare D1](d1.md) | D1 (SQLite over HTTP) | Level 3 (read/write) | HTTP + MCP | Reference design |

## Adding an adapter

Copy the shape of any existing note into a new page. This does not
require an AEP — adapter notes are informative.

Recommended sections:

1. Frontmatter + one-line summary
2. Manifest example
3. Operation mapping table (AFI → native)
4. Access control notes
5. Anything unusual about performance, versioning, or gotchas

Open a PR against [`docs/providers/`](https://github.com/ucalyptus/afi/tree/main/docs/providers).
