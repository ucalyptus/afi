# Contributing to AFI

Thanks for your interest. AFI is a specification, not a codebase (yet), so
most contributions are text: RFCs, provider adapter notes, or clarifications
to the spec.

## Kinds of contributions

### 1. Issues
Open an issue for: bugs in the spec, ambiguities, missing capabilities,
questions. Label with `question`, `bug`, or `proposal`.

### 2. Provider adapter notes
Wrote or discovered an implementation that fits the AFI shape? Add a file
under [`providers/`](./providers/) following the pattern in the existing
adapter notes. This does not require an RFC.

### 3. RFCs
Substantive changes to `SPEC.md` — new operations, new schemas, new
transports, new conformance levels — go through the RFC process in
[GOVERNANCE.md](./GOVERNANCE.md).

To open an RFC:

1. `cp rfcs/TEMPLATE.md rfcs/NNNN-my-proposal.md` (next unused number)
2. Fill in every section
3. Open a PR titled `RFC NNNN: <short name>` with status `Draft`
4. Wait at least 14 days before asking for merge

### 4. Spec editorial fixes
Typos, formatting, broken links, clarifications that don't change behavior —
open a PR directly, no RFC needed. Tag `editorial`.

## Style

- Use RFC 2119 keywords (MUST, SHOULD, MAY, …) precisely in normative text
- Prefer examples in fenced code blocks over prose descriptions
- Every operation should have a request shape, a response shape, and at least
  one failure mode documented
- Provider adapter notes should include: capability level, transport, auth
  model, and one worked example

## Code of Conduct

Be direct, kind, and technical. If we need a formal CoC before this project
has any real contributors, we've done something wrong. Once we have >5
contributors from >2 orgs, we'll adopt the Contributor Covenant.

## License

By contributing you agree that your contribution is licensed under Apache-2.0
(see [LICENSE](./LICENSE)) and that you have the right to license it that way.
