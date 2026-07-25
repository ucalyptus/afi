---
title: Charter
description: The AFI project charter — scope, principles, and v1.0 gate.
---

# Charter

## Mission

The **Agent Filesystem Interface** (AFI) project defines a vendor-neutral
specification for exposing arbitrary data sources to AI agents as a
POSIX-shaped filesystem, plus reference implementations, conformance
tools, and the governance to keep the standard portable across vendors.

## Scope

**In scope:**

- The AFI specification (this repository, `docs/specification/`)
- Reference transport bindings (MCP, HTTP, FUSE, SSH)
- A public conformance test kit
- Adapter notes for existing systems that fit the AFI shape

**Out of scope:**

- Building a hosted service or product on top of AFI
- Endorsing or ranking specific providers
- Defining higher-level agent frameworks or retrieval systems
- Anything that would tie AFI's success to a single vendor's roadmap

## Working assumptions

The project assumes:

- LLM agents will continue treating filesystems as a primary
  exploration interface
- The virtual-FS-over-data pattern is going to converge with or without
  standardization; the choice is between coordinating early and
  discovering incompatible shapes later
- No single vendor should own the resulting standard

## v1.0 gate

`draft` becomes a numbered version (`0.1`, `0.2`, …) via routine AEP
cycles. `0.x` becomes `1.0` when *all* of the following are true:

1. Two or more **independent conforming implementations** exist across
   two or more organizations
2. At least one implementation is not maintained by AFI core
   maintainers or by any organization that has held a core-maintainer
   seat in the past six months
3. The [conformance test kit](../specification/draft/conformance/test-kit.md)
   is at v1.0 and both implementations pass all scenarios at the level
   they declare
4. A [neutral home](governance.md#neutral-home) has been identified and
   accepted the project
5. No accepted AEP proposing breaking changes is still open

## Non-goals as commitments

The project explicitly commits to *not*:

- Add features solely because current models struggle without them
  (see [design principles](design-principles.md#capability-over-compensation))
- Fragment into multiple competing shapes for the same operation
  (see [design principles](design-principles.md#convergence-over-choice))
- Accept new spec features without at least one working implementation
  (see [design principles](design-principles.md#demonstration-over-deliberation))
- Reserve any project role for a specific organization

## Trademark

The name "AFI" and the tagline "Agent Filesystem Interface" are dedicated
to the project. On donation to a neutral home, they transfer to that
host.

## Amendment

This charter is amended via the [AEP process](aep-guidelines.md). Charter
amendments require, at minimum, a 30-day comment period and unanimous
approval by active maintainers (not simple majority).
