---
title: Design principles
description: The constitutional principles that guide AFI's evolution.
---

# Design principles

These principles guide how we evaluate AEPs, weigh tradeoffs, and evolve
AFI. They are guidance by and for the community when developing
[AEPs](aep-guidelines.md).

Applied examples of these principles in real design decisions live in
[Learn → Design principles applied](../learn/design-in-practice.md).

## Convergence over choice

There should be one way to solve a problem in AFI. Rather than supporting
multiple approaches that fragment the ecosystem, we choose a single well-
designed path — accepting harder decisions upfront to deliver a more
cohesive standard.

Extensions (`x-` fields) are where convergence gets tested; the
specification is where it gets committed.

## Composability over specificity

AFI provides four foundational operations: `stat`, `list`, `read`,
`search`. We don't add operations for use cases that can be constructed
from these existing building blocks. This keeps the surface area small
and implementations simple.

When someone asks why AFI doesn't support a feature directly, the answer
is usually that it can be built from what AFI already provides.

## Interoperability over optimization

AFI runs across providers and clients of widely varying sophistication.
We favor features that degrade gracefully over those that only work when
every participant is equally capable. Capability negotiation via the
manifest makes this concrete: providers declare what they support, and
clients adapt rather than assume.

## Stability over velocity

Adding to a standard as widely adopted as AFI aims to be is easy.
Removing from it is nearly impossible. Every addition is a permanent
commitment and a cost for provider implementers to support. We move
deliberately, knowing that "no" today leaves the door open while "yes"
closes it forever.

Contributors accustomed to rapid shipping may find this pace frustrating,
but sustainable standards require sustainable decision-making. We
optimize for decades, not quarters.

## Capability over compensation

Models improve faster than protocols evolve. We avoid adding permanent
structure to work around limitations that are likely temporary — the
limitation fades, but the complexity remains.

This is not license to ignore today's reality. Optional context that
weaker models lean on and stronger ones ignore costs nothing. But when a
proposal exists primarily because current models struggle without it, we
ask whether they will outgrow the need before we can shed the weight.

## Demonstration over deliberation

AFI values working implementations over theoretical debates. When
evaluating AEPs, we prioritize evidence from real usage over hypothetical
arguments. We encourage contributors to prototype, experiment, and
demonstrate rather than design by committee. Implementation reveals what
discussion cannot.

Every operation in v0.1 exists in ChromaFs, supabase.sh, or AgentFS today.
We shipped what was already working.

## Pragmatism over purity

AFI makes practical tradeoffs in service of adoption and usability. We
don't pursue theoretical elegance at the cost of real-world utility. When
a "correct" design creates friction for implementers, we consider whether
a "good enough" design better serves the ecosystem.

`list` isn't recursive because a recursive `list` would explode response
sizes; agents can walk trees themselves. That's the pragmatic call, even
though a recursive form would be more elegant.

## Standardization over innovation

AFI standardizes patterns that have already proven valuable. We look for
conventions that work across multiple implementations and codify them,
rather than inventing new paradigms and hoping they'll be adopted.

We encourage the use of AFI extensions (`x-` fields) as a way to
experiment with new patterns that may eventually lead to standardization.
