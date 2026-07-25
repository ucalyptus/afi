---
title: Extensions & versioning
description: How AFI grows without breaking things.
---

# Extensions & versioning

## Vendor extensions

Providers can add data without waiting for standardization:

- Vendor-specific fields on `Entry.attributes` MUST use the `x-` prefix.
  Example: `attributes.x-chromafs-collection-id`.
- Vendor-specific fields on the manifest MUST use the `x-` prefix.
  Example: `x-agentfs-db-path`.
- Vendor-specific error fields MUST use the `x-` prefix.

Fields without the `x-` prefix are reserved for the AFI specification.
Clients MUST tolerate unknown fields (forward compatibility) but SHOULD
NOT rely on unrecognized `x-` fields.

## Extension track vs standards track

An `x-` extension MAY be proposed for standardization via an
[AEP](../../../community/aep-guidelines.md). Convergence works like this:

1. A provider ships an `x-my-thing` field
2. Other providers adopt it, possibly with variations
3. An AEP is filed proposing standardization
4. If accepted, the field is renamed (drops the `x-` prefix) in the next
   spec version and the old form is deprecated per the
   [feature lifecycle](../../../community/feature-lifecycle.md)

This mirrors HTTP's `X-Header` → header standardization history and MCP's
extensions track.

## Adding a new operation

A new normative operation requires:

- An accepted AEP with the operation's request/response schemas
- At least two independent implementations demonstrating the operation
- Conformance test coverage
- Documentation in the appropriate spec section

New operations are added in a minor version bump when possible; only
mutually incompatible additions require a major bump.

## Removing behavior

See [Feature lifecycle](../../../community/feature-lifecycle.md) for the
full deprecation policy. Summary:

- Behavior may be **deprecated** in a minor version, with a documented
  migration path.
- Deprecated behavior remains in the spec for at least one full release
  cycle.
- Removal requires an AEP and happens in a major version bump.

## Client compatibility rules

Clients MUST:

- Tolerate unknown fields on manifest, `Entry`, `Match`, and `Error`
  responses
- Refuse to call operations not present in the manifest's `capabilities`
- Warn (not error) when a provider advertises a lower minor version than
  the client's target

Clients MAY:

- Reject providers whose major `afi_version` is higher than the client's
  target
- Attempt best-effort interop across major versions when the client user
  opts in

## Spec versioning

See the [Versioning learn page](../../../learn/versioning.md) for the full
policy on `draft`, numbered versions, and provider/client version
interaction.
