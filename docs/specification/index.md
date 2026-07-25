---
title: Specification
description: The normative AFI specification.
---

# Specification

The **Agent Filesystem Interface** specification is the normative reference
for AFI providers and clients. Non-normative background lives in
[Learn](../learn/index.md); this section is what implementers must follow.

## Versions

| Version | Status | Notes |
|---|---|---|
| [`draft`](draft/index.md) | Draft (v0.1) | Working version. May change without notice. |

Once the draft meets the [v1.0 gate](../community/charter.md#v10-gate),
it will be tagged as a numbered version and this table will grow.

## Reading order

Newcomers should read in this order:

1. [Overview](draft/overview.md) — the shape of the spec
2. [Terminology](draft/terminology.md) — precise definitions
3. [Path model](draft/path-model.md) — how paths behave
4. [Operations](draft/operations/index.md) — the four core verbs
5. [Schemas](draft/schemas/index.md) — Entry and Match data types
6. [Basic concepts](draft/basic/index.md) — discovery, access control, errors
7. [Transports](draft/transports/index.md) — MCP, HTTP, FUSE, SSH bindings
8. [Conformance](draft/conformance/index.md) — profiles and testing

## Conventions

The key words **MUST**, **MUST NOT**, **SHOULD**, **SHOULD NOT**, and **MAY**
in this specification are to be interpreted as described in
[RFC 2119](https://www.rfc-editor.org/rfc/rfc2119) and
[RFC 8174](https://www.rfc-editor.org/rfc/rfc8174) when, and only when, they
appear in all capitals.

Schema examples use TypeScript-style pseudo-syntax for readability.
Normative schemas will migrate to JSON Schema before v1.0.
