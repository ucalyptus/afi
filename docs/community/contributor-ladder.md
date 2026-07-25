---
title: Contributor ladder
description: Roles, responsibilities, and advancement criteria for AFI contributors.
---

# Contributor ladder

The AFI contributor ladder defines roles, responsibilities, and
advancement criteria. It shows community members how to grow their
involvement from a first contribution to project leadership.

## Guiding principles

- **Earned trust.** Advancement follows from demonstrated contributions,
  good judgment, and sustained engagement. Tenure alone is not enough.
- **Multiple growth pathways.** Spec work, transport bindings,
  documentation, conformance testing, and community building all lead to
  advancement.
- **Transparency.** Criteria for advancement are explicit and applied
  consistently.
- **Alignment with AFI goals.** Contributors must show commitment to AFI
  beyond any single employer's interests. Vendor-neutrality is not
  optional.

## Roles at a glance

| Role | Summary | Key privileges | Minimum timeline |
|---|---|---|---|
| [**Contributor**](#contributor) | Anyone who contributes to AFI | Submit issues, PRs, participate in AEPs | Immediate |
| [**Member**](#member) | Established, active contributor | GitHub org membership, triage rights, eligible for WG leadership | 2–3 months of meaningful contributions |
| [**Maintainer**](#maintainer) | Area steward with operational responsibility | Merge rights on their area, release participation | 6+ months as Member |
| [**Core Maintainer**](#core-maintainer) | Technical leadership and spec stewardship | Final decision authority on the spec, governance participation | By invitation after sustained Maintainer contribution |
| [**Lead Maintainer**](#lead-maintainer) | Ultimate project authority (initial founders) | All Core Maintainer privileges, veto authority, appoints Core Maintainers | Reserved for project founders; succession only |

!!! note "Timelines are minimums"
    Timelines protect the project from rapid privilege escalation and
    ensure a high bar of demonstrated commitment. Actual advancement is
    discretionary and may take longer. Exceptions require explicit Core
    Maintainer approval with documented rationale.

## Contributor

Anyone who has contributed to AFI in any form is a Contributor. This
includes opening issues, submitting pull requests, participating in AEP
discussions, improving documentation, or helping other community members.

**There are no formal requirements.** We welcome all contributions that
follow our [contributing guidelines](https://github.com/ucalyptus/afi/blob/main/CONTRIBUTING.md).

**Getting started:**

- Review the [contributing guide](https://github.com/ucalyptus/afi/blob/main/CONTRIBUTING.md)
- Look for issues tagged `good-first-issue` or `help-wanted`
- Write a [provider adapter note](../providers/index.md) for a system
  you know

## Member

Members are established Contributors with a record of ongoing commitment
to AFI.

**Requirements:**

- Multiple meaningful contributions over ≥ 2 months
- At least one merged PR or accepted AEP
- Ongoing engagement, not just one-off contributions
- Public commitment to AFI's [design principles](design-principles.md)

**Privileges:**

- Membership in the `ucalyptus/afi` GitHub org (or its post-donation
  successor)
- Triage rights on issues and PRs
- Eligibility to lead Working Groups and Interest Groups

**How to advance:** any active Maintainer may nominate. Nominations are
reviewed by Core Maintainers.

## Maintainer

Maintainers steward a specific area with operational responsibility.

**Requirements:**

- ≥ 6 months as a Member with sustained activity
- Deep expertise in the area they'll maintain (e.g., a specific
  transport binding, the conformance kit, docs)
- Nomination by an existing Maintainer of that area *or* by a Core
  Maintainer

**Privileges:**

- Merge rights on their area
- Vote in Maintainer-level decisions
- Release participation for their area

**How to advance:** any Core Maintainer may nominate. Nominations
require approval by two Core Maintainers.

## Core Maintainer

Core Maintainers steer the AFI specification and overall project
direction.

**Requirements:**

- Sustained excellence as a Maintainer
- Broad understanding across the whole spec, not just one area
- Demonstrated vendor-neutral judgment on contentious decisions
- Explicit invitation by existing Core Maintainers

**Privileges:**

- Final decision authority on the spec (subject to Lead Maintainer veto)
- Admin access to all AFI repositories
- Vote in Core Maintainer decisions (one vote per person, capped at one
  per organization)
- Appointment authority for Maintainers

**How to advance:** by invitation only. A candidate must be discussed
and approved by unanimous vote of active Core Maintainers.

## Lead Maintainer

Lead Maintainer(s) hold ultimate project authority. Reserved for the
project's founders and their appointed successors.

**Privileges:**

- All Core Maintainer privileges
- Veto authority over any Core Maintainer or Maintainer decision (must
  be publicly justified)
- Appointment authority for Core Maintainers
- Administration of all project infrastructure

**Succession:** Lead Maintainer(s) name their successor(s), subject to
approval by the active Core Maintainer group.

## Inactivity policy

Any role above Contributor may be marked **inactive** after 6 months
without meaningful contribution. Inactive members:

- Lose voting rights until they resume active participation
- May be removed from the GitHub org after 12 months of inactivity (with
  30 days' notice)
- May regain their role by resuming contributions; the reinstatement
  bar is lower than the original advancement bar

## Advancement transparency

All advancements are announced publicly in the AFI GitHub repository.
Advancement rationale is documented, either in a public issue or in the
project's decision log.
