---
title: Working groups
description: Focused subteams driving specific areas of AFI.
---

# Working groups

Working Groups (WGs) are focused subteams driving specific areas of AFI.
They give experts an operational scope smaller than "the whole project"
while remaining accountable to the Core Maintainer group.

## Currently active

*None yet.* Working Groups will be chartered once the project has enough
active Contributors to justify them.

## Planned

The following Working Groups are anticipated in the AFI v0.1 → v1.0
timeframe:

### Conformance WG
- **Scope:** design and maintain the
  [conformance test kit](../../specification/draft/conformance/test-kit.md)
- **Deliverables:** `@afi/conformance` reference implementation, test
  scenario catalog, badge criteria
- **Sponsors needed:** a Maintainer with QA / test-tooling background

### Transports WG
- **Scope:** maintain and evolve the four
  [transport bindings](../../specification/draft/transports/index.md)
- **Deliverables:** reference SDKs for each transport, transport-specific
  test coverage
- **Sponsors needed:** one Maintainer per binding

### Extensions WG
- **Scope:** shepherd `x-` extensions through the standardization
  pipeline; maintain the extension registry
- **Deliverables:** extension registry document, promotion criteria,
  deprecation tracking
- **Sponsors needed:** a Maintainer with strong spec-writing background

### Providers WG
- **Scope:** onboard new provider adapter notes; maintain provider
  compatibility metadata
- **Deliverables:** provider catalog, compatibility matrix, provider
  outreach
- **Sponsors needed:** a Maintainer with ecosystem-relations background

## How to charter a Working Group

To propose a new Working Group:

1. Open an AEP (Process type) titled `AEP-NNNN: Charter — <WG Name>`
2. Include:
    - Scope (what the WG owns; what it does *not* own)
    - Deliverables and success criteria
    - Initial charter (or a link to the WG's own charter document under
      `docs/community/working-groups/<name>/`)
    - Initial members (at least one Maintainer or Core Maintainer)
    - Cadence (async by default; sync meetings optional)
3. Follow the standard [AEP process](../aep-guidelines.md)

## WG governance

WGs are autonomous within their scope:

- WGs may set their own communication norms and meeting cadence
- WG decisions on their own scope stand unless overturned by Core
  Maintainers by simple majority
- WGs may nominate Maintainers within their scope; nominations require
  Core Maintainer approval

WGs are accountable to the Core Maintainer group:

- WG charters are reviewed annually
- Inactive WGs (no substantive activity for 6 months) MAY be sunset by
  the Core Maintainer group
