# AEP-0001: Project charter

- **Status:** Accepted
- **Type:** Process
- **Author(s):** @dasayad
- **Sponsor:** @dasayad
- **Created:** 2026-07-24
- **Target spec version:** 0.1

## Abstract

Establishes AFI (Agent Filesystem Interface) as a vendor-neutral
specification for exposing arbitrary data sources to AI agents via a
POSIX-shaped interface, and defines the initial scope, non-goals, and
governance model.

## Motivation

Multiple teams in 2025–2026 have independently built the same primitive:
a virtual filesystem over their proprietary data source, letting agents
use `ls`, `cat`, `grep`, `find` to explore it.

- Mintlify built [ChromaFs](https://www.mintlify.com/blog/how-we-built-a-virtual-filesystem-for-our-assistant)
  over their Chroma docs index
- Supabase built [supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh)
  over their docs corpus (SSH transport, `just-bash` runtime)
- Turso shipped [AgentFS](https://github.com/tursodatabase/agentfs) as
  a SQLite-backed agent state store, mountable via FUSE/NFS
- Vercel published [multiple](https://vercel.com/blog/we-removed-80-percent-of-our-agents-tools)
  [posts](https://vercel.com/blog/how-to-build-agents-with-filesystems-and-bash)
  arguing the filesystem-as-agent-tool pattern is the right default

Each of these is genuinely useful. None of them can talk to each other.
Every new adopter re-invents the same operations with slightly different
shapes, so agents cannot be portable across providers.

The problem is not lack of implementations. The problem is lack of a
**shared interface** — a common set of operations and schemas that any
of these systems could satisfy.

CSI solved this pattern for container storage in Kubernetes. LSP solved
it for language tooling. MCP is solving it for tool invocation. Nothing
yet exists for filesystem-shaped agent context.

This AEP is the founding process document that establishes AFI to fill
that gap.

## Specification

This AEP constitutes:

1. **Adoption of the v0.1 draft** in
   [`docs/specification/draft/`](https://ucalyptus.github.io/afi/specification/draft/)
   as the baseline for the project's first numbered version.
2. **Governance** under
   [`docs/community/governance.md`](https://ucalyptus.github.io/afi/community/governance/)
   — AEP-driven, consensus-first, vendor-neutral, targeting a neutral
   home once the [v1.0 gate](https://ucalyptus.github.io/afi/community/charter/#v10-gate)
   conditions are met.
3. **License: Apache-2.0** for both spec text and any reference code,
   chosen for its explicit patent grant. Documentation (non-normative
   pages) is available under Creative Commons Attribution 4.0
   International.
4. **Initial scope for v0.1:**
    - Read-oriented operations: [`stat`](https://ucalyptus.github.io/afi/specification/draft/operations/stat/),
      [`list`](https://ucalyptus.github.io/afi/specification/draft/operations/list/),
      [`read`](https://ucalyptus.github.io/afi/specification/draft/operations/read/),
      [`search`](https://ucalyptus.github.io/afi/specification/draft/operations/search/)
    - [Discovery manifest](https://ucalyptus.github.io/afi/specification/draft/basic/discovery/)
    - Four [transport bindings](https://ucalyptus.github.io/afi/specification/draft/transports/):
      MCP, HTTP, FUSE, SSH
    - Four [conformance levels](https://ucalyptus.github.io/afi/specification/draft/conformance/profiles/)
      (0–3)
    - Mutation operations at Level 3 are stubbed pending a future
      Standards Track AEP.
5. **Design principles** documented in
   [`docs/community/design-principles.md`](https://ucalyptus.github.io/afi/community/design-principles/)
   as the constitutional criteria for evaluating future AEPs.

## Rationale

**Alternatives considered:**

1. **Do nothing.** Let vendors continue implementing per-product
   filesystems. Rejected: the market is clearly converging on the
   pattern, and the cost of a bad convergence (each vendor's shape
   becoming a de facto local standard) is far higher than the cost of
   coordinating early.

2. **Extend MCP directly with a "filesystem" server type.** Rejected as
   sole solution because MCP is a transport/RPC framework, not a
   data-shape spec — the "how do you name a search result?" question is
   orthogonal to MCP. AFI ships an MCP binding as the recommended
   transport, so this composes rather than competes.

3. **Standardize AgentFS's SQLite schema.** Rejected because AgentFS's
   schema assumes a specific backing store (SQLite) and specific
   transport (FUSE). AFI is a strictly larger tent that AgentFS can fit
   inside as one provider.

4. **Adopt 9P as-is.** Rejected because 9P assumes a real filesystem
   substrate; AFI needs first-class search, semantic search,
   capability-based access control, and structured metadata that don't
   map cleanly onto 9P.

**Prior art referenced:**

- CSI (Container Storage Interface)
- CRI (Container Runtime Interface)
- LSP (Language Server Protocol)
- MCP (Model Context Protocol)
- OpenTelemetry semantic conventions
- 9P, POSIX, FUSE, WebDAV, HTTP Range

## Backwards compatibility

N/A — this is the founding charter.

## Reference implementation

N/A — process AEP. The v0.1 spec text itself, along with provider
adapter notes for ChromaFs, AgentFS, supabase.sh, and the Cloudflare D1
sketch, ship alongside this AEP.

## Security considerations

The initial scope explicitly includes
[access control semantics](https://ucalyptus.github.io/afi/specification/draft/basic/access-control/)
in v0.1 (uniform `not_found` cloaking, capability-based auth) rather
than deferring them to a follow-up. Rationale: retrofitting access
control after providers have shipped is significantly harder than
getting it right in the first version.

Cross-reference the [security policy](https://ucalyptus.github.io/afi/community/security/).

## Privacy considerations

The spec deliberately avoids standardizing telemetry, usage tracking, or
provider-to-provider information flow. Any privacy-sensitive extensions
(e.g., cross-provider audit trails) must go through a future AEP with
explicit privacy review.

## Adoption plan

- **Day 0 (this AEP accepted):** v0.1 SPEC published; adapter notes for
  ChromaFs, AgentFS, supabase.sh, and a D1 sketch shipped alongside
- **Day 30:** Reference MCP transport binding published as a library
- **Day 60:** Conformance test kit (~50 scenarios)
- **Day 90:** Outreach to Mintlify, Turso, Supabase, Vercel for review
- **v1.0 gate:** two independent implementations pass conformance, at
  least one is not maintained by AFI core

## Unresolved questions

- **Trademark & domain:** does the project register `afi.dev` /
  `afi-spec.org` before donation, or only after finding a neutral home?
- **Reference SDK language:** should the initial reference
  implementation be a Node/TypeScript library (broadest reach) or a
  Rust crate (best fit for AgentFS interop), or both?
- **Working Group cadence:** at what contributor count do we formally
  charter the anticipated Working Groups (Conformance, Transports,
  Extensions, Providers)? Current placeholder: ≥ 5 contributors from
  ≥ 2 organizations.

## Changelog

- 2026-07-24 — Initial draft as `rfcs/0001-charter.md`
- 2026-11-08 — Migrated to `aeps/AEP-0001-charter.md`; updated internal
  links to the MkDocs site
