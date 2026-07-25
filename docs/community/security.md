---
title: Security policy
description: How to report vulnerabilities in the AFI specification or its reference implementations.
---

# Security policy

## Scope

Two kinds of security issues are in scope for this project:

1. **Specification bugs** where a normative requirement, if implemented
   as written, leads to insecure behavior across providers
2. **Reference implementation bugs** in any code repository maintained
   directly by the AFI project (currently: the future
   `@afi/conformance` test kit and any reference transport binding libraries)

Out of scope:

- Vulnerabilities in specific providers (contact the provider directly,
  e.g., Turso for AgentFS, Supabase for supabase.sh)
- Vulnerabilities in third-party agent runtimes that integrate AFI

## Reporting a vulnerability

**Please do not open public GitHub issues for suspected security
vulnerabilities.** Instead:

  Open a private
  [GitHub Security Advisory](https://github.com/ucalyptus/afi/security/advisories/new)
  on the AFI repository, or
- Email the AFI Core Maintainers at the address published at
  [`.well-known/security.txt`](https://ucalyptus.github.io/afi/.well-known/security.txt)
  once established

Include:

- A description of the issue
- Steps to reproduce, ideally with a minimal example
- The version of the spec or code affected
- Your assessment of impact and severity

## Response process

- **Acknowledgment:** within 5 business days
- **Triage:** within 14 days — we'll classify and set an expected
  timeline
- **Coordinated disclosure:** we'll agree on a disclosure timeline with
  the reporter, typically 60–90 days for spec bugs and 30–60 days for
  implementation bugs
- **Fix and advisory:** we'll publish a fix (spec revision or code
  patch), a
  [GitHub Security Advisory](https://github.com/ucalyptus/afi/security/advisories),
  and credit the reporter (unless anonymity is requested)

## Threat model

The AFI specification's threat model assumes:

- **Untrusted clients.** Providers MUST assume any client may be
  compromised or malicious. See
  [Access control](../specification/draft/basic/access-control.md).
- **Untrusted providers.** Clients SHOULD NOT trust `Entry.attributes`
  fields blindly, especially `attributes.provenance`. Content served by
  a provider is not authenticated by AFI itself.
- **Public network paths.** All transport bindings MUST support
  encrypted transport (HTTPS for HTTP, SSH channel encryption for SSH,
  MCP transport-level encryption, filesystem-native controls for FUSE).

Notable non-goals of the threat model in v0.1:

- **Byte-level content authenticity.** AFI does not currently include a
  cryptographic content-integrity mechanism. Providers that need this
  MAY add `x-` extension fields (e.g., `x-content-sha256`); if a
  cross-provider need emerges, standardization goes through the AEP
  process.
- **DoS resistance.** AFI defines `rate_limited` as an error code but
  does not mandate a specific rate-limiting model. Providers are
  responsible for defending their own infrastructure.

## Security-sensitive AEPs

AEPs that touch access control, authentication, transport security, or
capability semantics are **security-sensitive** and MUST receive review
from at least one Core Maintainer with a security background before
acceptance. If no such Core Maintainer is currently active, the Core
Maintainer group MUST recruit an external security reviewer before
merging.

## Bug bounty

None currently. If a foundation or sponsor establishes one post-donation,
this policy will be updated.
