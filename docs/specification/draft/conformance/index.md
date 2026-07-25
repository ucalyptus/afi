---
title: Conformance
description: How to declare and verify AFI conformance.
---

# Conformance

An AFI provider declares a conformance level in its manifest. A public
test kit validates that declaration is honest.

- **[Profiles](profiles.md)** — the four levels (Discovery, Read, Search,
  Read/Write) and their required operations
- **[Test kit plan](test-kit.md)** — the planned conformance test harness

## Why conformance matters

Clients can trust a provider's manifest only if the ecosystem checks it.
Without a test kit, "AFI-conformant" becomes a marketing claim.

AFI's conformance model borrows from CSI (Container Storage Interface):
providers self-declare, tests verify, results are published, and clients
gate integration on published pass/fail status.
