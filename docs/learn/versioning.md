---
title: Versioning
description: How AFI spec versions, provider versions, and client versions interact.
---

# Versioning

AFI uses [SemVer](https://semver.org/) with an explicit distinction between
**the spec** and **implementations** of the spec.

## Spec versions

The spec itself is versioned:

- **`draft`** — the working version. What you're reading now. May change
  without notice; not suitable for production commitments.
- **`0.x`** — pre-1.0 versions. Minor bumps may include breaking changes
  during pre-1.0.
- **`1.0`** and later — SemVer applies strictly. Breaking changes require
  a major bump *and* an approved AEP.

At any moment there is exactly one `draft` and zero or more numbered
versions. When a numbered version is cut, its content becomes immutable
except for editorial corrections.

## Provider versions

Providers declare which AFI spec version they implement:

```json
{ "afi_version": "0.1", "provider_version": "1.2.0", ... }
```

- `afi_version` is the highest AFI spec version the provider claims
  conformance to
- `provider_version` is the provider's own SemVer, opaque to AFI

A provider MAY implement multiple `afi_version` values simultaneously by
serving different manifests at different endpoints, but each endpoint MUST
advertise one version.

## Client versions

Clients declare which AFI spec version they target. Clients MUST:

- Tolerate unknown fields on responses (forward compatibility)
- Refuse to call operations the manifest didn't declare
- Refuse to speak to providers advertising a major version higher than
  the client's target, unless the client explicitly enables best-effort
  interop

Clients SHOULD warn (not error) when a provider advertises a *lower* minor
version than the client's target — some operations the client understands
may return `unsupported`.

## Feature lifecycle within a version

Individual features inside a version move through states independently.
See [Feature lifecycle](../community/feature-lifecycle.md) for the full
policy. Summary:

- **Active** — normative in the current version
- **Deprecated** — still in the spec, scheduled for removal; migration
  path documented
- **Removed** — deleted from `draft`; will be absent from the next
  numbered version

## Compatibility promises

Once AFI hits 1.0, we commit to:

- Never removing an Active feature without at least one release cycle in
  Deprecated state
- Never adding a required field to an existing operation response —
  additions must be optional
- Providing a migration guide for every major version bump

Pre-1.0, we make no such promises. Implementers who need stability should
pin to a specific version and follow the roadmap.
