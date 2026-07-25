---
title: Communication
description: Where AFI technical discussion happens.
---

# Communication

## Async — the default

**GitHub Issues** (`ucalyptus/afi/issues`) is the primary async channel:

- Bug reports on the spec
- Questions about intent or edge cases
- Provider adapter proposals

**GitHub Discussions** (`ucalyptus/afi/discussions`) for:

- Broader design conversations
- "Is this a good use case for AFI?" questions
- Community announcements

**GitHub Pull Requests** for:

- AEP drafts (formatted per [AEP guidelines](aep-guidelines.md))
- Editorial fixes
- New provider adapter notes

All substantive decisions MUST be recorded in writing on GitHub. If a
decision was made in a synchronous channel, it MUST be summarized and
posted to the relevant issue or discussion before it counts.

## Sync — coming soon

Once the project has ≥ 5 contributors from ≥ 2 organizations, we will
establish a shared communication channel. Candidates in preference order:

1. **Matrix / IRC** — open, federated, terminal-friendly. Matches AFI's
   "developer tools" aesthetic.
2. **Discord** — familiar to modern OSS contributors; MCP uses this.
3. **A Slack workspace** — familiar to enterprise contributors.

Whichever channel is chosen, transparency rules apply: all substantive
decisions get logged back to GitHub.

## Meetings

None currently scheduled. Once the Core Maintainer group has ≥ 3
members, an async-first meeting cadence will be adopted, with recorded
summaries posted publicly.

## Response times

We aim for:

- **Issues:** first triage within 7 days
- **PRs:** first review within 14 days (longer for AEP-scale changes)
- **AEP In-Review:** editor comments within 7 days of formal comment
  period opening

These are best-effort, not SLAs. All maintainers are volunteers or
work on AFI as one project among many. Ping if something's stalled.

## Announcements

Major announcements (new spec versions, new maintainers, governance
changes, neutral-home donation) will be posted on:

- The repository README's "Status" section
- GitHub Discussions under the `announcements` category
- The project's public communication channel, once established

## Off-topic

Please don't file AFI issues for:

- Support requests for specific providers (contact the provider directly)
- General LLM agent tooling questions (better forums: r/LocalLLaMA, LM
  discussion boards, MCP community)
- Product feedback for tools that use AFI (contact the tool's maintainer)
