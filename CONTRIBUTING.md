# Contributing to AFI

Thank you for your interest in improving the Agent Filesystem Interface!

## Where to start

- **Small changes** (typos, editorial fixes, provider adapter notes) —
  open a pull request directly.
- **Substantive spec or process changes** — start with an
  [AEP proposal issue](https://github.com/ucalyptus/afi/issues/new?template=aep_proposal.md),
  then follow the
  [AEP guidelines](https://ucalyptus.github.io/afi/community/aep-guidelines/).
- **Questions** — use
  [GitHub Discussions](https://github.com/ucalyptus/afi/discussions).
- **Security issues** — see [`SECURITY.md`](SECURITY.md).

## Roles and ladder

Contributions of any size welcome. Sustained contributions lead to
Member, then Maintainer, then Core Maintainer roles — see the
[contributor ladder](https://ucalyptus.github.io/afi/community/contributor-ladder/).

## Code of conduct

Participation in the AFI project is governed by our
[Code of Conduct](CODE_OF_CONDUCT.md). Please read it.

## Repo conventions

### Commit messages

Use imperative mood ("Add", "Fix", "Update"). Reference AEP numbers where
applicable (e.g. `AEP-0002: initial draft of write operations`).

### Pull request checklist

Every PR should:

1. Have a clear title and one-paragraph description of the change.
2. Reference the relevant AEP (for spec changes) or issue.
3. Build cleanly locally: `mkdocs build --strict`.
4. Include cross-links to related pages where relevant.
5. Not introduce new normative requirements unless the underlying AEP
   is at least in `In-Review`.

### Style

- **Prose**: sentence-case headings, no trailing punctuation on
  headings. Prefer active voice.
- **Normative language**: RFC 2119 / RFC 8174 keywords (MUST, SHOULD,
  MAY) — use them exactly as defined.
- **Code samples**: real code, not pseudocode, wherever possible.
  Prefer TypeScript for illustrative examples unless a specific
  transport binding requires otherwise.

## Building the docs locally

```bash
pip install mkdocs-material pymdown-extensions mkdocs-material-extensions
mkdocs serve
```

Then open http://127.0.0.1:8000/.

To verify the same strict build the CI runs:

```bash
mkdocs build --strict
```

## License

By contributing, you agree that your contributions will be licensed
under the [Apache License 2.0](LICENSE) for spec text and reference
code, and under
[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) for
non-normative documentation prose.

We do not require a Contributor License Agreement (CLA).
