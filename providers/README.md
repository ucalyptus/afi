# AFI providers

Adapter notes for existing systems that can (or already do) fit the AFI shape.
Each file explains the mapping from AFI operations to that system's native
primitives.

**Existing sketches:**

| File | System | Owner | Conformance target | Transport |
|---|---|---|---|---|
| [agentfs.md](./agentfs.md) | Turso AgentFS | Turso | Level 3 (read/write) | FUSE / NFS + native SDK |
| [chromafs.md](./chromafs.md) | Mintlify ChromaFs | Mintlify | Level 2 (search) | In-process (just-bash) |
| [supabase-sh.md](./supabase-sh.md) | Supabase docs over SSH | Supabase | Level 2 (search) | SSH + just-bash |
| [d1.md](./d1.md) | Cloudflare D1 (reference sketch) | — | Level 2 (search) | HTTP + MCP |

**Adding an adapter:** copy the shape of any of the above. This does not
require an RFC. Adapter notes are informative, not normative.
