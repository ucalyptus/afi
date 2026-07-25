# AEP-NNNN: <Short name>

- **Status:** Draft | Awaiting Sponsor | In-Review | Accepted | Rejected | Deferred | Final | Withdrawn
- **Type:** Standards Track | Informational | Process | Extensions Track
- **Author(s):** @your-github-handle
- **Sponsor:** @maintainer-github-handle (required before status can move past `Draft`)
- **Created:** YYYY-MM-DD
- **Target spec version:** 0.X or 1.X
- **Superseded by:** (optional) AEP-NNNN
- **Supersedes:** (optional) AEP-NNNN

## Abstract

One short paragraph. What is this AEP proposing and why? Anyone should be
able to read the abstract and immediately understand the scope of the
proposal.

## Motivation

Why do we need this change? What existing use case is not served by the
current spec? Cite concrete implementations or user reports where
possible.

Which of the [design principles](https://ucalyptus.github.io/afi/community/design-principles/)
does this proposal serve? Which does it trade off against?

## Specification

The concrete additions/changes. For Standards Track AEPs, this section is
the source of truth for what will land in the spec. Use RFC 2119 / RFC
8174 keywords (MUST, SHOULD, MAY) exactly as defined there.

For each change:

- **Where in the spec?** Link to the file under
  `docs/specification/draft/` that will be modified or added.
- **What changes?** Show the exact new text (or a diff).
- **What are the invariants?** Bullet the non-negotiable behavioral
  requirements.

## Rationale

Why this design instead of others?

- What alternatives were considered?
- Why was each alternative rejected?
- What tradeoffs does the chosen design make?

Include prior art from analogous features in other standards (POSIX, 9P,
FUSE, MCP, CSI, LSP, S3, GCSFuse, WebDAV, HTTP Range, …).

## Backwards compatibility

- Is this a breaking change? If yes, what's the migration path?
- What does this mean for existing providers targeting the previous
  version?
- What does this mean for existing clients?
- If the change deprecates any existing feature, cross-reference the
  [feature lifecycle policy](https://ucalyptus.github.io/afi/community/feature-lifecycle/).

## Reference implementation

Working code is required for Standards Track AEPs. Link to:

- A branch or fork implementing the proposal
- A commit hash of any reference SDK changes
- A conformance-test scenario demonstrating the behavior

For Process or Informational AEPs, this section may be omitted or replaced
with "N/A — process change" and appropriate rationale.

## Security considerations

Every AEP MUST address security implications. Even if the answer is "none
identified", the AEP author must explicitly consider:

- Does this change affect [access control](https://ucalyptus.github.io/afi/specification/draft/basic/access-control/)?
- Does this change expose new information that could aid an attacker?
- Does this change interact with authentication or capability semantics?
- Does this change enable new denial-of-service vectors?

Security-sensitive AEPs require security review before acceptance — see
the [security policy](https://ucalyptus.github.io/afi/community/security/).

## Privacy considerations

Does this AEP affect what data is exposed, logged, or transferred?
Consider both provider-side and client-side privacy implications.

## Adoption plan

- Which existing providers can implement this? By when?
- Which client libraries need updates?
- How will this be tested in the [conformance suite](https://ucalyptus.github.io/afi/specification/draft/conformance/)?
- What's the timeline from Accepted to shipped in a numbered spec version?

## Unresolved questions

Bullet list. What still needs to be figured out before this can move from
`Draft` → `In-Review` → `Accepted`?

## Changelog

- YYYY-MM-DD — Initial draft
- YYYY-MM-DD — <substantive change>
