---
title: Introduction
description: What AFI is, what it isn't, and the problem it solves.
---

# Introduction

## What AFI is

The **Agent Filesystem Interface** (AFI) is a specification for exposing
arbitrary data sources to AI agents as a POSIX-shaped filesystem. Any system
that implements AFI is an **AFI provider**. Any agent runtime that consumes
AFI providers is an **AFI client**.

The interface is small on purpose. Four core operations —
[`stat`](../specification/draft/operations/stat.md),
[`list`](../specification/draft/operations/list.md),
[`read`](../specification/draft/operations/read.md),
[`search`](../specification/draft/operations/search.md) —
plus discovery, access control, and four transport bindings
(MCP, HTTP, FUSE, SSH).

## What AFI is not

- **Not a wire protocol.** Any of the [transport bindings](../specification/draft/transports/index.md)
  can be conformant.
- **Not a specific implementation.** SQLite, vector DBs, object stores, HTTP
  APIs all work as backing stores.
- **Not a replacement for MCP.** AFI ships an [MCP binding](../specification/draft/transports/mcp.md)
  as the recommended transport — the two compose.
- **Not a replacement for POSIX or FUSE.** AFI *borrows* their mental model.

## The problem

LLM agents are trained on massive volumes of code. Their default reasoning
about "how do I explore an unfamiliar body of information?" is a bash session:
`ls` to see the shape, `find` to locate something specific, `grep` to search
across, `cat` to read.

Recognizing this, several teams shipped virtual-filesystem interfaces over
their data in 2025–2026 — Mintlify over their docs index, Supabase over their
docs corpus via SSH, Turso over agent state via FUSE, Vercel over their
analytics DSL via a bash tool. Each solution works. None of them share a
shape.

That means:

- Agent runtimes must integrate each provider one-off
- A skill learned against one provider doesn't transfer to another
- Every new provider re-invents the same operations, with subtle differences
- No conformance testing means "does it actually work like a filesystem?"
  is an implementer judgment call

AFI is a small standard that fixes exactly these problems, and nothing else.

## The AFI stance

- **A tight core, generous extensions.** Four required operations, structured
  extension points (`x-` prefixed fields), an AEP process for standardizing
  what the ecosystem converges on.
- **Transport-plural.** MCP is recommended, HTTP is offered for direct
  integrations, FUSE serves the "make it look like a real disk" use case,
  SSH serves the "any developer with a terminal can poke at it" use case.
- **Conformance-tested.** [Four profiles](../specification/draft/conformance/profiles.md)
  from discovery-only to full read/write; providers declare their level and
  a public test kit checks that declaration is honest.
- **Vendor-neutral from day one.** The [governance model](../community/governance.md)
  requires two independent implementations before v1.0, and the project aims
  for a neutral home once that gate is met.

## Where to go next

- [Build a provider](build-a-provider.md) — wrap your data source
- [Build a client](build-a-client.md) — consume providers from an agent
- [Architecture](../learn/architecture.md) — how the pieces fit together
- [Specification (draft)](../specification/draft/index.md) — normative
  requirements
