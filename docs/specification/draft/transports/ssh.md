---
title: SSH binding
description: Expose AFI operations over a public SSH shell.
---

# SSH binding

For providers who want the "anyone with a terminal can poke at this" UX —
[supabase.sh](https://supabase.com/blog/supabase-docs-over-ssh) is the
canonical example.

## Model

An SSH server accepts connections (typically unauthenticated for public
data), drops the caller into a sandboxed bash shell over a virtual
filesystem, and interprets standard bash commands as AFI operations.

Recommended runtime: [`just-bash`](https://github.com/vercel-labs/just-bash),
Vercel Labs' TypeScript reimplementation of bash. Any equivalent
sandboxed bash emulator with a pluggable `IFileSystem` interface works.

## Command mapping

| bash | AFI operation |
|---|---|
| `stat PATH` | [`stat(PATH)`](../operations/stat.md) |
| `ls PATH` | [`list(PATH)`](../operations/list.md) |
| `ls -la PATH` | `list(PATH)` with full-detail formatting |
| `cat PATH` | [`read(PATH)`](../operations/read.md) |
| `head -c N PATH` | `read(PATH, {length: N})` |
| `tail -c N PATH` | `read(PATH, {offset: size - N, length: N})` |
| `grep STR PATH` | [`search(STR, {path: PATH})`](../operations/search.md) |
| `grep -E RE PATH` | `search({regex: RE}, {path: PATH})` |
| `grep -r STR PATH` | `search(STR, {path: PATH})` (list-and-read fallback if needed) |
| `find PATH -name GLOB` | `list(PATH, {glob: GLOB})` recursively |

## Manifest

Served as the file `/.afi/manifest.json` in the virtual root. Callers
retrieve it with:

```
$ ssh provider.example cat /.afi/manifest.json
```

## Bootstrap for agents

Providers SHOULD expose a `setup` or `agents` command that emits a
snippet to add to an agent's `AGENTS.md` or `CLAUDE.md`:

```
$ ssh provider.example agents >> AGENTS.md
```

Contents typical:

```markdown
## Provider.example (AFI over SSH)

Before answering questions about provider.example, check the docs:

- `ssh provider.example ls /docs` to see the shape
- `ssh provider.example grep 'STRING' /docs` to search
- `ssh provider.example cat /docs/PATH` to read a specific page
```

## Auth

For public providers: none. For gated providers: standard SSH auth
(public keys, passwords, or an SSO-broker like Tailscale SSH).

Providers with per-user capability scoping MAY key capabilities off the
SSH principal.

## Errors

Errors surface as bash-shaped stderr output plus a non-zero exit code.
The stderr line SHOULD be prefixed:

```
afi: <path>: <code>: <message>
```

Example:

```
$ cat /secret/passwords.txt
afi: /secret/passwords.txt: not_found: no such path
$ echo $?
2
```

## Isolation

The SSH shell MUST NOT expose actual host processes, files outside the
virtual root, or network access. `just-bash` provides this by default —
custom implementations MUST enforce it.

## Rate limiting

Providers SHOULD rate-limit connections and commands. Recommended
defaults: 10 connections per IP per minute, 100 commands per session per
minute.
