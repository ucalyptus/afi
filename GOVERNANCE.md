# Governance

AFI is developed openly. This document describes how decisions get made.

## Principles

- **Vendor-neutral.** Any organization can implement, propose changes, or hold
  a maintainer seat. No single vendor gets veto power.
- **Written down.** Substantive changes ship as numbered RFCs in `rfcs/`.
  If it's not in an RFC, it's not settled.
- **Slow on breaking changes.** Backward-compatible extensions can move
  quickly; breaking changes require a major-version RFC with a two-week
  comment period minimum.
- **Multiple implementations required.** No feature graduates from `Draft` to
  `Stable` without two independent conforming implementations.

## Roles

- **Contributor** — anyone who opens an issue, PR, or RFC. No commitment.
- **Maintainer** — commits & merges. Nominated by existing maintainers; at
  least two maintainers must approve. Aim for representation from ≥2 orgs.
- **Editor** — one maintainer per active RFC, responsible for driving it to a
  decision.

Initial maintainers are listed in [MAINTAINERS.md](./MAINTAINERS.md) (to be
created once the first outside contributor accepts a seat).

## RFC process

1. Copy `rfcs/TEMPLATE.md` to `rfcs/NNNN-short-name.md` (pick the next number)
2. Open a PR with status `Draft`
3. Comment period: **14 days minimum** for substantive changes
4. An editor (a maintainer) is assigned. They summarize objections, seek
   consensus, and mark the RFC `Accepted`, `Rejected`, or `Deferred`
5. Merged RFCs become part of the next spec version

## Decision model

- **Consensus first.** If maintainers agree, merge.
- **Lazy consensus.** If no maintainer objects within 7 days on an RFC PR
  labeled `lazy-consensus`, it passes.
- **Vote as last resort.** Simple majority of active maintainers, one vote per
  maintainer, one vote per org max.

## Neutral home

Once AFI has ≥2 independent conforming implementations across ≥2 orgs, we will
seek a neutral home (target: Linux Foundation AI & Data, or CNCF sandbox). No
maintainer, contributor, or organization retains special rights after donation.

## Trademark

The name "AFI" and the tagline "Agent Filesystem Interface" are dedicated to
the project. On donation to a neutral home, they transfer to that host.

## Amendments

This document is amended via the RFC process.
