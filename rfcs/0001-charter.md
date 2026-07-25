# RFC 0001: Project charter

- **Status:** Accepted
- **Editor:** @dasayad
- **Created:** 2026-07-24
- **Target spec version:** 0.1

## Summary

Establishes AFI (Agent Filesystem Interface) as a vendor-neutral specification
for exposing arbitrary data sources to AI agents via a POSIX-shaped interface,
and defines the initial scope, non-goals, and governance model.

## Motivation

Multiple teams in 2025–2026 have independently built the same primitive: a
virtual filesystem over their proprietary data source, letting agents use
`ls`, `cat`, `grep`, `find` to explore it.

- Mintlify built [ChromaFs](https://www.mintlify.com/blog/how-we-built-a-virtual-filesystem-for-our-assistant)
  over their Chroma docs index
- Supabase built [supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh)
  over their docs corpus (SSH transport, `just-bash` runtime)
- Turso shipped [AgentFS](https://github.com/tursodatabase/agentfs) as a
  SQLite-backed agent state store, mountable via FUSE/NFS
- Vercel published [multiple](https://vercel.com/blog/we-removed-80-percent-of-our-agents-tools)
  [posts](https://vercel.com/blog/how-to-build-agents-with-filesystems-and-bash)
  arguing the filesystem-as-agent-tool pattern is the right default

Each of these is genuinely useful. None of them can talk to each other. Every
new adopter re-invents the same operations with slightly different shapes,
so agents cannot be portable across providers.

The problem is not lack of implementations. The problem is lack of a
**shared interface** — a common set of operations and schemas that any of
these systems could satisfy.

CSI solved this pattern for container storage in Kubernetes. LSP solved it
for language tooling. MCP is solving it for tool invocation. Nothing yet
exists for filesystem-shaped agent context.

## Detailed design

See `SPEC.md` v0.1 in the same tree as this RFC. The charter constitutes:

1. **Adoption of that draft as the v0.1 baseline.**
2. **Governance under `GOVERNANCE.md`** — RFC-driven, consensus-first,
   vendor-neutral, target-neutral-home after 2 independent implementations.
3. **License: Apache-2.0** for both spec text and any reference code, chosen
   for its explicit patent grant.
4. **Scope for v0.1:** read-oriented operations (`stat`, `list`, `read`,
   `search`), discovery manifest, MCP + HTTP + FUSE + SSH transport bindings,
   4 conformance levels (0–3). Mutation operations at level 3 are stubbed
   pending RFC 0002.

## Compatibility

N/A — this is the founding charter.

## Alternatives considered

1. **Do nothing.** Let vendors continue implementing per-product filesystems.
   Rejected: the market is clearly converging on the pattern, and the cost of
   a bad convergence (each vendor's shape becoming a de facto local standard)
   is far higher than the cost of coordinating early.

2. **Extend MCP directly with a "filesystem" server type.** Rejected as sole
   solution because MCP is a transport/RPC framework, not a data-shape spec —
   the "how do you name a search result?" question is orthogonal to MCP. AFI
   ships an MCP binding as the recommended transport, so this composes rather
   than competes.

3. **Standardize AgentFS's SQLite schema.** Rejected because AgentFS's schema
   assumes a specific backing store (SQLite) and specific transport (FUSE).
   AFI is a strictly larger tent that AgentFS can fit inside as one provider.

4. **Adopt 9P as-is.** Rejected because 9P assumes a real filesystem
   substrate; AFI needs first-class search, semantic search, capability-based
   access control, and structured metadata that don't map cleanly onto 9P.

## Prior art

- CSI (Container Storage Interface)
- CRI (Container Runtime Interface)
- LSP (Language Server Protocol)
- MCP (Model Context Protocol)
- 9P, POSIX, FUSE, WebDAV

## Unresolved questions

- Trademark & domain: does the project register `afi.dev` / `afi-spec.org`
  before donation, or only after finding a neutral home?
- Should the initial reference implementation be a Node/TypeScript library
  (broadest reach) or a Rust crate (best fit for AgentFS interop)?

## Adoption plan

- **Day 0 (this RFC):** v0.1 SPEC published; adapter notes for ChromaFs,
  AgentFS, supabase.sh, and a D1 sketch shipped alongside
- **Day 30:** Reference MCP transport binding published as a library
- **Day 60:** Conformance test kit (~50 scenarios)
- **Day 90:** Reach out to Mintlify, Turso, Supabase, Vercel for review
- **v1.0 gate:** 2 independent implementations pass conformance, at least one
  is not maintained by the AFI project itself
