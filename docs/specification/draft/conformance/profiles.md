---
title: Conformance profiles
description: The four AFI conformance levels.
---

# Conformance profiles

An AFI provider declares exactly one profile in its
[manifest](../basic/discovery.md) as `profile`.

## The four levels

| Level | Name | Required operations | Typical use case |
|---|---|---|---|
| **0** | Discovery | `stat`, `list` | Metadata-only providers, browsers |
| **1** | Read | Level 0 + `read` | Read-only doc / knowledge providers |
| **2** | Search | Level 1 + `search` (exact mode) | Full-text doc providers, RAG-shaped stores |
| **3** | Read/Write | Level 2 + `write`, `mkdir`, `delete`, `move` | Agent state, workspaces |

## Level 0 — Discovery

The minimum: navigate the tree.

**Required operations:** `stat`, `list`, plus serving the manifest.

**Use case:** a provider that only wants to advertise its shape without
serving content — e.g., a directory of downloadable artifacts where the
`read` layer lives elsewhere, or a metadata catalog.

## Level 1 — Read

Adds file content.

**Required operations:** `stat`, `list`, `read`.

**Use case:** static documentation, unindexed markdown corpora, one-off
data dumps.

## Level 2 — Search

Adds search.

**Required operations:** `stat`, `list`, `read`, `search`. Providers MUST
support at least exact-string search mode.

**Use case:** documentation providers where agents need to locate content
without walking the whole tree. This is the sweet spot for most
docs-over-AFI providers ([ChromaFs](../../../providers/chromafs.md),
[supabase.sh](../../../providers/supabase-sh.md)).

## Level 3 — Read/Write

Adds mutation.

**Required operations:** all of Level 2 plus `write`, `mkdir`, `delete`,
`move`.

**Use case:** agent state stores where the agent needs to persist
artifacts, scratch files, or evolving documents
([AgentFS](../../../providers/agentfs.md), most agent-workspace scenarios).

!!! warning "Level 3 operations are draft-frozen"
    The `write`, `mkdir`, `delete`, `move` operations are placeholders in
    v0.1. Their normative definitions are deferred to AEP-0002. Providers
    at Level 3 today are, by definition, non-portable across
    implementations pending that AEP.

## Sub-profile capabilities

Beyond the profile, providers declare individual capabilities in
`manifest.capabilities`. Clients MUST refuse to call operations not in
this list.

A Level 2 provider that supports only exact-string search declares:

```json
{ "profile": 2, "capabilities": ["stat", "list", "read", "search"],
  "search": { "modes": ["exact"] } }
```

A Level 2 provider that supports all three search modes declares:

```json
{ "profile": 2, "capabilities": ["stat", "list", "read", "search"],
  "search": { "modes": ["exact", "regex", "semantic"] } }
```

Both are Level 2 conformant.

## Profile upgrade paths

A provider MAY upgrade its profile over time. Downgrading is
strongly discouraged (breaks existing clients). If a provider MUST
downgrade, it SHOULD:

1. Advertise the new (lower) profile in a new manifest URL
2. Continue serving the old manifest with a deprecation warning for a
   full release cycle
3. Coordinate with major clients before dropping the old endpoint
