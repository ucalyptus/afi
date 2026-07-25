---
title: Overview
description: The shape of the AFI specification.
---

# Overview

The Agent Filesystem Interface defines an abstract interface for exposing
arbitrary data sources to AI agents as a POSIX-shaped filesystem.

Implementations of this interface are called **AFI providers**. Software
that consumes providers is called an **AFI client** (typically an agent
runtime).

AFI is transport-agnostic. The [transports section](transports/index.md)
specifies concrete bindings for MCP, HTTP, FUSE, and SSH. Any binding that
preserves the semantics defined in this specification is conformant.

## Structure

The spec is organized as follows:

- **[Terminology](terminology.md)** defines the words this spec uses precisely.
- **[Path model](path-model.md)** governs what a valid AFI path is and how
  paths are compared.
- **[Basic](basic/index.md)** covers cross-cutting concerns: discovery via
  the manifest, access control, structured errors, and the extension model.
- **[Operations](operations/index.md)** are the four core verbs
  (`stat`, `list`, `read`, `search`) — each has its own page with request
  and response schemas.
- **[Schemas](schemas/index.md)** defines the two shared data types
  (`Entry`, `Match`).
- **[Transports](transports/index.md)** specifies how operations travel:
  MCP tool calls, HTTP endpoints, FUSE callbacks, or SSH-hosted bash.
- **[Conformance](conformance/index.md)** defines four profiles and the
  test kit that validates them.

## What's *not* in v0.1

- Streaming subscriptions or watch semantics (planned for v0.2)
- Cross-provider federation (planned for v0.3)
- Content-addressed storage semantics (out of scope)
- Structured (non-text) match result formats (out of scope; use
  `attributes` on `Entry`)
- Mutation operations (`write`, `mkdir`, `delete`, `move`) — reserved
  for AEP-0002

## Conventions

- Key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, **MAY** are
  interpreted per [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) when
  in all caps.
- Schemas use TypeScript-style syntax for readability. JSON Schema
  equivalents will be published alongside every numbered release.
- Time values use [RFC 3339](https://www.rfc-editor.org/rfc/rfc3339) unless
  otherwise noted.
