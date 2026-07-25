<div align="center">

# Agent Filesystem Interface (AFI)

**A vendor-neutral specification for exposing any data source to AI agents as a filesystem-shaped interface.**

[![Docs](https://img.shields.io/badge/docs-ucalyptus.github.io%2Fafi-000?logo=readthedocs&logoColor=white)](https://ucalyptus.github.io/afi/)
[![Spec](https://img.shields.io/badge/spec-draft-blue)](https://ucalyptus.github.io/afi/specification/draft/)
[![License: Apache-2.0](https://img.shields.io/badge/license-Apache--2.0-green)](LICENSE)
[![Docs License: CC BY 4.0](https://img.shields.io/badge/docs-CC%20BY%204.0-lightgrey)](https://creativecommons.org/licenses/by/4.0/)

**[Read the spec →](https://ucalyptus.github.io/afi/specification/draft/)** &nbsp;·&nbsp;
**[Build a provider →](https://ucalyptus.github.io/afi/getting-started/build-a-provider/)** &nbsp;·&nbsp;
**[Governance →](https://ucalyptus.github.io/afi/community/governance/)** &nbsp;·&nbsp;
**[AEPs →](aeps/)**

</div>

---

## What is AFI?

LLM agents are converging on filesystems as their primary interface for
exploring information. The same `ls`, `cat`, `grep`, `find` skills the
model learned from code work equally well for docs, tickets,
transcripts, and agent state.

Several teams have built essentially the same thing in isolation —
Mintlify's [ChromaFs](https://www.mintlify.com/blog/how-we-built-a-virtual-filesystem-for-our-assistant),
Supabase's [supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh),
Turso's [AgentFS](https://github.com/tursodatabase/agentfs) — with
slightly different shapes and no interoperability.

**AFI is the shared shape.** Any store — SQLite, vector DB, object
store, HTTP API — can implement AFI and become a
[**provider**](https://ucalyptus.github.io/afi/providers/) that any
AFI-aware agent can talk to.

## Repository layout

| Path | Contents |
|---|---|
| [`docs/specification/`](docs/specification/) | The normative specification (draft) |
| [`docs/community/`](docs/community/) | Governance, contributor ladder, AEP process |
| [`docs/providers/`](docs/providers/) | Adapter notes for existing systems |
| [`aeps/`](aeps/) | AFI Enhancement Proposals |
| [`mkdocs.yml`](mkdocs.yml) | MkDocs Material site configuration |
| [`.github/workflows/`](.github/workflows/) | Docs deploy + CI |

Everything is served as a searchable site at
**[ucalyptus.github.io/afi](https://ucalyptus.github.io/afi/)**.

## Getting started

- **I want to add AFI support to my product** → [Build a provider](https://ucalyptus.github.io/afi/getting-started/build-a-provider/)
- **I'm building an agent that consumes AFI** → [Build a client](https://ucalyptus.github.io/afi/getting-started/build-a-client/)
- **I want to understand the design** → [Learn](https://ucalyptus.github.io/afi/learn/)
- **I want to propose a change** → [AEP guidelines](https://ucalyptus.github.io/afi/community/aep-guidelines/)

## Status

- ✅ **v0.1 draft** — specification, four transport bindings, conformance profiles
- ✅ **Adapter sketches** — AgentFS, ChromaFs, supabase.sh, Cloudflare D1
- ⏳ Reference MCP transport binding
- ⏳ Conformance test kit
- ⏳ Two independent implementations
- ⏳ Cut v1.0, donate to a neutral home (LF AI & Data, CNCF Sandbox, or a new LF sub-foundation)

See the [charter](https://ucalyptus.github.io/afi/community/charter/#v10-gate)
for the exact v1.0 gating criteria.

## Contributing

Read [`CONTRIBUTING.md`](CONTRIBUTING.md) and the
[contributor ladder](https://ucalyptus.github.io/afi/community/contributor-ladder/).
Substantive changes ship via [AFI Enhancement Proposals](https://ucalyptus.github.io/afi/community/aep-guidelines/).

Please read the [Code of Conduct](CODE_OF_CONDUCT.md) — we're serious about a
welcoming project.

## Prior art

- **CSI** (Container Storage Interface) — the model of "an interface, not an implementation"
- **LSP** (Language Server Protocol) — proof that a small, well-scoped interface can outlive its originating vendor
- **MCP** (Model Context Protocol) — the transport AFI happily rides
- **9P / FUSE / POSIX** — the mental model everyone already shares

## License

- Specification and reference code: [Apache License 2.0](LICENSE)
- Documentation prose (`docs/` non-normative pages): [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)

## Security

To report a security issue, please use the
[private security advisory](https://github.com/ucalyptus/afi/security/advisories/new)
process. See [SECURITY.md](SECURITY.md) for the full policy.
