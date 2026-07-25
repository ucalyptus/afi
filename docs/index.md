---
title: Agent Filesystem Interface
description: A vendor-neutral specification for exposing any data source to AI agents as a filesystem-shaped interface.
hide:
  - navigation
  - toc
---

# Agent Filesystem Interface

**A vendor-neutral specification for exposing any data source to AI agents as
a filesystem-shaped interface.**

<div class="grid cards" markdown>

-   :material-book-open-page-variant: **[Get started](getting-started/index.md)**

    Understand what AFI is, then walk through building your first provider or
    client in under an hour.

-   :material-file-document-outline: **[Specification](specification/index.md)**

    The normative draft. Operations, schemas, discovery, transports,
    conformance profiles.

-   :material-toy-brick-outline: **[Providers](providers/index.md)**

    Adapter notes for existing systems: AgentFS, ChromaFs, supabase.sh,
    and a Cloudflare D1 sketch.

-   :material-account-group: **[Community](community/index.md)**

    Governance, contributor ladder, AEP process, working groups. Vendor-neutral
    by design.

</div>

---

## Why AFI

LLM agents are converging on filesystems as their primary interface for
exploring information. `ls`, `cat`, `grep`, `find` — the muscle memory the
model has from millions of code repos works just as well for docs, tickets,
transcripts, and agent state.

Multiple teams in 2025–2026 shipped essentially the same primitive in isolation:

| Project | What it exposes | Backing store | Transport |
|---|---|---|---|
| [Mintlify ChromaFs](https://www.mintlify.com/blog/how-we-built-a-virtual-filesystem-for-our-assistant) | Docs | Chroma vector DB | In-process (just-bash) |
| [Supabase supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh) | Docs | Markdown files | SSH (just-bash) |
| [Turso AgentFS](https://github.com/tursodatabase/agentfs) | Agent state | SQLite | FUSE / NFS |
| Vercel d0 filesystem agent | Analytics | Files in sandbox | Bash tool |

Each is genuinely useful. None can talk to each other. Every new adopter
re-invents the same operations with slightly different shapes. Agents cannot
be portable across providers.

**AFI is the shared shape.** Implementers become AFI *providers*; agents talk
to any provider through the same small set of operations. The interface is
transport-agnostic: MCP, HTTP, FUSE, and SSH bindings are all defined.

## Standing on the shoulders of

- **CSI** (Container Storage Interface) — "an interface, not an
  implementation" as the primitive that unlocks an ecosystem
- **LSP** (Language Server Protocol) — proof that a small, well-scoped
  interface outlives its originating vendor
- **MCP** (Model Context Protocol) — the transport AFI most happily rides
- **9P / FUSE / POSIX** — the mental model the standard borrows from

## Status

AFI is in **draft**. The v0.1 draft is complete and open for review.
[Read the specification](specification/draft/index.md),
[open an AEP](community/aep-guidelines.md), or
[write an adapter note](providers/index.md).
