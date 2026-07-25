# AFI — Agent Filesystem Interface

**A vendor-neutral specification for exposing any data source to AI agents as
a filesystem-shaped interface.**

Status: **Draft v0.1** · License: Apache-2.0 · [Spec](./SPEC.md) ·
[Governance](./GOVERNANCE.md) · [RFCs](./rfcs/) · [Providers](./providers/)

---

## Why

LLM agents are converging on filesystems as their primary interface for
exploring information. The same `ls`, `cat`, `grep`, `find` skills the model
learned from code work equally well for docs, tickets, transcripts, and agent
state. Several teams have built essentially the same thing in isolation:

| Project | What it exposes | Backing store | Transport |
|---|---|---|---|
| [Mintlify ChromaFs](https://www.mintlify.com/blog/how-we-built-a-virtual-filesystem-for-our-assistant) | Docs | Chroma vector DB | In-process (just-bash) |
| [Supabase supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh) | Docs | Markdown files | SSH (just-bash) |
| [Turso AgentFS](https://github.com/tursodatabase/agentfs) | Agent state | SQLite | FUSE / NFS |
| Vercel d0 filesystem agent | Cube DSL / analytics | Files in sandbox | Bash tool |

Each of these is genuinely useful. None of them can talk to each other. An
agent that learned "how ChromaFs answers `ls`" can't reuse that learning
against AgentFS, supabase.sh, or a future D1-backed provider without new
adapter code. Every vendor is re-inventing the same primitives with slightly
different shapes.

**AFI is the shared shape.** Implementers become AFI *providers*; agents talk
to any provider through the same small set of operations.

## What AFI is (and isn't)

**AFI is:**
- An abstract interface (operations + data schemas) that any store can implement
- A conformance framework (levels 0–3, from stat-only to full read/write)
- A set of transport bindings (MCP tools, HTTP, FUSE, SSH) so providers can
  pick what fits their deployment

**AFI is not:**
- A wire protocol — any of the bindings above will do
- A specific implementation — SQLite / vector DB / object store / HTTP API all work
- A replacement for MCP — AFI can *ship as* an MCP tool suite; the two compose
- A replacement for POSIX / FUSE — those are the mental model AFI borrows from

## Quickstart

1. Read the [SPEC](./SPEC.md)
2. Pick a conformance level (0 = discovery, 3 = full read/write)
3. Wrap your data source behind the operations
4. Publish a `/.afi/manifest.json`
5. Optional: contribute an adapter note under [`providers/`](./providers/) so
   agents can discover you

## Governance

AFI aims to be a **vendor-neutral standard**, not a single-vendor spec. See
[GOVERNANCE.md](./GOVERNANCE.md) for the RFC process, decision model, and how
to join. Substantive changes ship via numbered [RFCs](./rfcs/); v1.0 will be
cut once two independent implementations pass the conformance suite.

## Prior art & inspiration

- **CSI** (Container Storage Interface) — the model of "an interface, not an
  implementation" that let Kubernetes storage flourish
- **LSP** (Language Server Protocol) — proof that a small, well-scoped
  interface can outlive its originating vendor
- **MCP** (Model Context Protocol) — the transport we'll happily ride
- **9P / FUSE / POSIX** — the mental model everyone already shares

## Status

- [x] Draft SPEC v0.1
- [x] Provider adapter sketches: AgentFS, ChromaFs, supabase.sh, D1
- [ ] Reference MCP transport binding
- [ ] Conformance test kit
- [ ] Two independent implementations
- [ ] Cut v1.0, seek neutral home (LF AI & Data, CNCF sandbox)

## Contact

Open an issue or an RFC. See [CONTRIBUTING.md](./CONTRIBUTING.md).
