---
title: Draft (v0.1)
description: The draft AFI specification.
---

# AFI specification — draft (v0.1)

!!! warning "This is a draft"
    The draft version is the working copy of the AFI specification. It may
    change without notice. Providers and clients targeting a stable version
    should wait for `0.1` to be tagged as a numbered release.

## Sections

- **[Overview](overview.md)** — the shape of the spec
- **[Terminology](terminology.md)** — precise definitions
- **[Path model](path-model.md)** — how paths behave

**Core**

- **[Basic — Discovery, access control, errors, extensions](basic/index.md)**
- **[Operations](operations/index.md)** — `stat`, `list`, `read`, `search`
- **[Schemas](schemas/index.md)** — `Entry`, `Match`

**Transports**

- **[Transports](transports/index.md)** — MCP, HTTP, FUSE, SSH

**Conformance**

- **[Conformance](conformance/index.md)** — profiles and test kit

## Change log for `draft`

| Date | Change | AEP |
|---|---|---|
| 2026-07-24 | Initial v0.1 draft published | [AEP-0001](../../aeps/AEP-0001-charter.md) |
