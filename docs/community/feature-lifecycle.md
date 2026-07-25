---
title: Feature lifecycle
description: How AFI specification features move through Active, Deprecated, and Removed states.
---

# Feature lifecycle and deprecation policy

This policy defines a lifecycle for individual features within the AFI
specification. It defines three feature states (Active, Deprecated,
Removed), the criteria and procedure for moving between them, a minimum
window between deprecation and removal, and the documentation required
at each transition.

## Scope

This policy governs **features** of the AFI core specification: operations,
schema fields, transport bindings, capabilities, and normative behavioral
requirements. The revision lifecycle of the specification document itself
(`draft` vs numbered versions) is defined in
[Learn → Versioning](../learn/versioning.md).

## Feature states

A specification feature is in exactly one of three states:

| State | Meaning | Implementer expectation |
|---|---|---|
| **Active** | The feature is part of the Current specification revision. | Implement per the feature's normative requirements. |
| **Deprecated** | The feature remains in the specification but is scheduled for removal. A migration path is documented. | New implementations should not adopt the feature. Existing implementations should migrate before the earliest removal date. |
| **Removed** | The feature has been deleted from `draft` and will be absent from the next Current revision. It remains documented in the Final revision it last appeared in. | Implementations targeting that next Current revision must not depend on the feature. |

A Deprecated feature MAY be restored to Active by an AEP that supersedes
the deprecation AEP and documents the changed circumstances. Restoration
follows the same approval path as deprecation. If the feature is later
deprecated again, the minimum deprecation window is measured afresh from
the revision in which the new deprecation takes effect.

## SDKs and provider compat

Removal from the specification does not oblige a provider or client SDK
to drop the feature from releases. That timeline is governed by the
SDK's own revision-support policy. Common patterns:

- Providers SHOULD continue to accept requests using Deprecated
  operations for at least one full major-version cycle after removal.
- Providers MAY optionally return a `Deprecation` HTTP header (for HTTP
  transport) or the equivalent MCP metadata pointing to the AEP that
  removed the feature.

## Deprecating a feature

A feature may be proposed for deprecation when:

- It has been **superseded** by another feature that covers the same
  use cases
- It presents a **security, privacy, or interoperability risk** that
  cannot be mitigated in place
- Ecosystem telemetry or SDK maintainer consensus indicates **negligible
  adoption relative to its maintenance cost**
- Any other reasons the Core Maintainers deem appropriate

Deprecation is a specification change and therefore requires an AEP per
the [AEP guidelines](aep-guidelines.md). The deprecation AEP MUST:

1. Identify the feature by name and link to its definition in the spec
2. State the rationale against the criteria above
3. Document the migration path — what implementers should do instead
4. Propose a target removal revision (must satisfy the minimum window
   below)

## Minimum deprecation window

Deprecated features MUST remain in the specification for **at least one
full release cycle** (one numbered version) before removal. Concretely:

- Feature Deprecated in `0.2` → earliest removal in `0.4`
- Feature Deprecated in `1.3` → earliest removal in `2.0`

Longer windows are strongly preferred for widely-adopted features.

## Removing a feature

Removal requires a separate AEP that:

1. References the deprecation AEP
2. Confirms the minimum window has elapsed
3. Documents adoption metrics or migration completeness
4. Specifies the exact revision in which the feature is Removed

A feature marked Removed:

- Is deleted from `draft`
- Remains documented in the last Final revision that contained it
- MUST NOT be added back without a fresh, unrelated AEP

## Editorial changes vs feature changes

Editorial improvements to a feature's documentation (clarifications,
examples, typo fixes) do not constitute a feature change and do not
require the deprecation process. Semantic changes to a feature's
behavior do — those are effectively deprecating the old behavior and
adding a new one.

## Reporting deprecations

The [`draft/index.md`](../specification/draft/index.md) change log MUST
list every Active → Deprecated and Deprecated → Removed transition,
with a link to the driving AEP.
