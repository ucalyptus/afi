---
title: Supabase docs over SSH
description: How supabase.sh maps onto AFI as a Level 2 SSH provider.
---

# Provider: Supabase docs over SSH (supabase.sh)

Informative adapter note. Not part of the normative spec.

- **System:** [supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh)
- **Backing store:** Markdown source files (same content as supabase.com/docs)
- **Native transport:** SSH server + [`just-bash`](https://github.com/vercel-labs/just-bash)
  sandboxed JavaScript runtime
- **Conformance target:** Level 2 (search)
- **Auth:** none (public docs)

## Manifest

```json
{
  "afi_version":      "0.1",
  "provider":         "supabase-sh",
  "provider_version": "1.x",
  "profile":          2,
  "capabilities":     ["stat", "list", "read", "search"],
  "search": {
    "modes":       ["exact", "regex"],
    "max_results": 200
  },
  "path":             { "case_sensitive": true, "max_length": 512 },
  "auth":             { "required": false, "methods": [] }
}
```

Served as a synthetic `/.afi/manifest.json` file in the virtual root.

## Operation mapping

| AFI | supabase.sh (bash surface) |
|---|---|
| [`stat(path)`](../specification/draft/operations/stat.md) | Underlying `IFileSystem.stat` in just-bash |
| [`list(path)`](../specification/draft/operations/list.md) | `ls PATH` → `IFileSystem.readdir` |
| [`read(path)`](../specification/draft/operations/read.md) | `cat PATH` → `IFileSystem.readFile` |
| [`search(str)`](../specification/draft/operations/search.md) | `grep -r STR /supabase/docs` |

## Bootstrap for agents

Supabase already ships an agent-onboarding line:

```bash
ssh supabase.sh agents >> AGENTS.md
```

An AFI-aware equivalent would be:

```bash
ssh supabase.sh afi >> AGENTS.md
```

…that emits the manifest URL + a snippet telling the agent to prefer the
AFI binding when available, falling back to the raw bash surface
otherwise.

## Notes

- supabase.sh is the SSH-transport reference case for AFI. Everything the
  [SSH binding](../specification/draft/transports/ssh.md) says is
  calibrated against its shape.
- Because the surface is bash-first, `grep`/`find` semantics are already
  well-defined; the AFI adapter is purely additive.
- Same underlying library (`just-bash`) as ChromaFs — a shared AFI shim
  above `IFileSystem` would serve both.
