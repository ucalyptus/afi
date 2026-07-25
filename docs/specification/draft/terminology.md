---
title: Terminology
description: Precise definitions of AFI terms.
---

# Terminology

The following definitions apply throughout the specification.

**Provider**
: A system that implements AFI operations against a backing store.

**Client**
: A software agent, tool, or human utility that calls AFI operations on a
  provider.

**Backing store**
: The underlying storage or data source that a provider adapts. Examples:
  a SQLite database, a Chroma collection, a directory of Markdown files,
  a Postgres table, an object bucket.

**Entry**
: A metadata record describing one file, directory, or symlink. Defined in
  [Schemas → Entry](schemas/entry.md).

**Match**
: A record describing one search result. Defined in
  [Schemas → Match](schemas/match.md).

**Capability**
: An authorization token, opaque to AFI, scoping a client's access to a
  subset of the provider's tree.

**Profile**
: A declared [conformance level](conformance/profiles.md) — Discovery,
  Read, Search, or Read/Write.

**Manifest**
: The JSON document served at `/.afi/manifest.json` describing a provider's
  version, profile, capabilities, and auth requirements. See
  [Discovery](basic/discovery.md).

**Operation**
: One of the verbs a client can call on a provider — `stat`, `list`, `read`,
  `search` (and, at Level 3, `write`, `mkdir`, `delete`, `move`).

**Transport binding**
: A concrete mapping from AFI operations to a specific wire protocol.
  v0.1 defines [MCP](transports/mcp.md), [HTTP/JSON](transports/http.md),
  [FUSE](transports/fuse.md), and [SSH](transports/ssh.md) bindings.

**AEP**
: AFI Enhancement Proposal — the design-document format used to propose
  changes to this specification. See
  [AEP guidelines](../../community/aep-guidelines.md).

**Draft / Numbered version**
: The **draft** version is the working copy of the spec, mutable at any
  time. A **numbered version** is a tagged snapshot (e.g. `0.1`) that is
  immutable except for editorial corrections.
