---
title: Governance
description: AFI's governance model, roles, and decision process.
---

# Governance

AFI is developed openly under a vendor-neutral governance model. This
document defines how decisions are made, who makes them, and how the
project evolves toward a neutral home.

## General project policies

AFI is being established as a standalone open-source project with the
intent of donation to a neutral foundation (see [Neutral home](#neutral-home)
below). Until that donation completes, the project is stewarded by the
initial maintainers.

Contributions to the AFI specification and reference code MUST be made
under the [Apache License, Version 2.0](https://www.apache.org/licenses/LICENSE-2.0)
("the Project License"). Documentation (excluding the normative
specification) is available under
[Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/).

The copyright in all contributions remains with the copyright holder as
independent works of authorship. No contributor is required to assign
copyrights to the project.

## Technical governance

AFI adopts a hierarchical structure, similar to Python, PyTorch, MCP, and
other open-source projects:

| Role | Scope |
|---|---|
| **Lead Maintainer(s) (BDFL)** | Final decision authority |
| **Core Maintainers** | Overall project direction |
| **Maintainers** | Working Groups, transport bindings, conformance kit |
| **Contributors** | Issues, PRs, discussions |

- **Contributors** file issues, open PRs, and participate in AEP
  discussions.
- **Maintainers** drive individual components: a transport binding, a
  reference SDK, the conformance kit, documentation, or a Working Group.
- **Core Maintainers** drive overall project direction and oversee
  Contributors and Maintainers.
- **Lead Maintainer(s)** are the final decision maker(s) — the role
  commonly known as BDFL (Benevolent Dictator for Life).

Together, Maintainers, Core Maintainers, and Lead Maintainer(s) form the
**AFI Steering Group**.

All maintainers are expected to have a strong bias toward AFI's
[design principles](design-principles.md). Membership in the technical
governance process is for individuals, not companies. There are no seats
reserved for specific companies, and membership is associated with the
person rather than the company employing that person.

### Roles

The [Contributor Ladder](contributor-ladder.md) is the canonical
definition of each role — its requirements, responsibilities, privileges,
advancement process, and inactivity policy.

Summary:

- **Maintainers** steward specific areas such as transport bindings, the
  conformance kit, documentation, or working groups. They make decisions
  for their area independently and escalate to Core Maintainers when
  needed. Maintainers have write access to their respective areas.
- **Core Maintainers** steer the AFI specification and overall project
  direction. They can veto Maintainer decisions by majority vote, resolve
  disputes, and appoint or remove Maintainers. Core Maintainers have
  admin access to all AFI repositories but use the same pull-request
  workflow as outside contributors.
- **Lead Maintainer(s)** hold final authority and can veto any decision
  by Core Maintainers or Maintainers. Lead Maintainer(s) appoint and
  remove Core Maintainers and are administrators on all project
  infrastructure. They are part of the Core Maintainer group and are
  expected to publicly articulate their reasoning.

### Decision process

- **Consensus first.** If maintainers agree, merge.
- **Lazy consensus.** If no maintainer objects within 7 days on a PR
  labeled `lazy-consensus`, it passes.
- **Formal vote.** For AEPs and contested decisions. Core Maintainer
  group votes by simple majority, with one vote per Core Maintainer and
  a maximum of one vote per organization to prevent capture.

The Core Maintainer group meets asynchronously via GitHub and, once
adopted, a shared communication channel (see [Communication](communication.md)).

### AEP process

Substantive spec changes go through [AEP guidelines](aep-guidelines.md).

## Neutral home

Once AFI reaches its [v1.0 gate](charter.md#v10-gate), the project will
seek a neutral home. Target hosts, in preference order:

1. **Linux Foundation AI & Data** — closest to the project's problem space
2. **CNCF Sandbox** — proven governance model for infrastructure standards
3. **A newly-formed LF sub-foundation** — modeled on how MCP was
   established as "Model Context Protocol a Series of LF Projects, LLC"

Upon donation:

- No maintainer, contributor, or organization retains special rights
- The project's trademark, domain, and infrastructure transfer to the
  neutral host
- Existing maintainers remain in role until the host's governance
  processes kick in

## Trademark

The name "AFI" and the tagline "Agent Filesystem Interface" are dedicated
to the project.

## Amendment

This governance document is amended via the [AEP process](aep-guidelines.md).
Governance amendments require unanimous approval of active Core
Maintainers.
