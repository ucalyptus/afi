---
title: Design principles applied
description: Worked examples of the AFI design principles in action.
---

# Design principles applied

The [community design principles](../community/design-principles.md) are the
constitution. This page shows them applied to real design tradeoffs in
v0.1.

## Convergence over choice → four operations, one shape

We rejected variants like "each provider defines its own operation set". The
result is that a `search` implementation choice — glob-based? regex? semantic?
— is captured via a single, declared manifest capability, not a whole
new operation.

## Composability over specificity → `search` is generic, not doc-flavored

We considered adding a `retrieve_docs(query, top_k)` operation modeled on
RAG APIs. Rejected: it composes from `search` + `read`. Any client that wants
"top-K docs" can `search` then `read` the top results. Adding a
higher-level primitive would fragment the spec.

## Interoperability over optimization → four transport bindings

FUSE is efficient for local mounts but useless for a browser-based agent.
MCP is what most agent runtimes speak but pointless if you want to
`ssh some.afi.host`. We defined four bindings so providers can pick without
locking themselves out of a client population.

## Stability over velocity → mutation deferred to AEP-0002

The v0.1 draft ships `stat`, `list`, `read`, `search` as normative. `write`,
`mkdir`, `delete`, `move` are listed at Level 3 but deferred to a future AEP.
Rationale: read-only is enough to unblock every existing implementation
(ChromaFs, supabase.sh, AgentFS-as-reader); write semantics deserve their
own design pass on conflict, atomicity, and byte-range writes without
foreclosing options.

## Capability over compensation → no "chunk this file for me" operation

Some early proposals included a `chunk(path, target_tokens)` operation to
help models fit large files into context. Rejected: models keep getting
longer context windows, and `read` with `offset`/`length` composes into any
chunking strategy the client wants. A permanent `chunk` operation would still
be there long after the context-window problem faded.

## Demonstration over deliberation → provider adapters ship in v0.1

We refuse to publish a "spec looking for implementations". Every operation
in v0.1 is demonstrated by at least one of ChromaFs, AgentFS, supabase.sh,
or the [D1 reference sketch](../providers/d1.md). If it doesn't map to at
least one real system, it isn't in the spec.

## Pragmatism over purity → `list` isn't recursive

A recursive `list` would be more elegant, but agents and clients that need
recursion can walk trees themselves. Making `list` recursive by default
would explode response sizes and complicate pagination. The pragmatic call
is: `list` is depth-1, and clients that need walk semantics implement them
using `list` — which composes into recursion trivially.

## Standardization over innovation → we shipped what the ecosystem already did

Every operation in v0.1 exists in ChromaFs, supabase.sh, or AgentFS today.
AFI is not inventing new primitives; it's picking the shape that has already
been proven in production and writing it down.
