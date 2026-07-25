# AEPs — AFI Enhancement Proposals

This directory contains **AFI Enhancement Proposals (AEPs)** — the design
documents that drive substantive changes to the AFI specification,
governance, and process.

The AEP process, workflow, and acceptance criteria are documented in
**[AEP guidelines](https://ucalyptus.github.io/afi/community/aep-guidelines/)**.

## Contents

| AEP | Title | Status | Type |
|---|---|---|---|
| [AEP-0001](AEP-0001-charter.md) | Project charter | Accepted | Process |

## Proposing a new AEP

1. Copy [`AEP-TEMPLATE.md`](AEP-TEMPLATE.md) to `AEP-NNNN-short-name.md`
   (next unused number).
2. Fill in every section.
3. Open a PR titled `AEP-NNNN: <short name>` with status `Draft` in the
   AEP frontmatter.
4. Follow the [AEP guidelines](https://ucalyptus.github.io/afi/community/aep-guidelines/).

## Numbering

- **0001–0099** — foundational (charter, governance, process)
- **0100+** — Standards Track (spec features, transport bindings)

Numbers are assigned when the PR is opened by whoever the sponsoring
Maintainer prefers — do not renumber existing AEPs.
