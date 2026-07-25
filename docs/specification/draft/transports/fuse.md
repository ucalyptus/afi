---
title: FUSE binding
description: Expose AFI operations as a real mounted filesystem.
---

# FUSE binding

For providers targeting local mounts — the "look like a real disk" UX.
Used by Turso AgentFS on Linux, and by any provider that wants agents to
run `grep`, `cat`, `find` unchanged through their process's filesystem
syscalls.

## Callback mapping

| FUSE callback | AFI operation |
|---|---|
| `getattr(path)` | [`stat(path)`](../operations/stat.md) |
| `readdir(path)` | [`list(path)`](../operations/list.md) with pagination unrolled |
| `read(path, offset, size)` | [`read(path, {offset, length: size})`](../operations/read.md) |
| `write(path, buf, offset)` | *(Level 3)* `write(path, buf, {offset})` |
| `mkdir(path)` | *(Level 3)* `mkdir(path)` |
| `unlink(path)`, `rmdir(path)` | *(Level 3)* `delete(path)` |
| `rename(src, dst)` | *(Level 3)* `move(src, dst)` |

`stat` errors map to `errno` values per [Errors → FUSE](../basic/errors.md#fuse).

## Search over FUSE

FUSE has no native "search" primitive, so providers expose search via a
synthetic control file:

- **Path:** `/.afi/search`
- **Write** to the file: JSON body matching [`search`](../operations/search.md)'s
  request shape (query + opts)
- **Read** from the file: NDJSON-encoded [`Match`](../schemas/match.md)
  objects, one per line, terminated by an empty line

Providers MAY additionally expose `search` via `ioctl` on the mount root.
The ioctl code is `AFI_SEARCH = 0xAF10` (reserved but not yet formally
allocated in the Linux ioctl registry).

## Manifest

Served as a regular file at `/.afi/manifest.json`. Contents: the manifest
JSON per [Discovery](../basic/discovery.md).

## Mount options

Providers SHOULD support these mount options:

- `ro` — refuse Level 3 operations even if the provider supports them
- `token=<file>` — path to a file containing the capability token
- `subtree=<path>` — mount only the specified subtree

## Platform support

- **Linux:** FUSE 3.x
- **macOS:** [macFUSE](https://osxfuse.github.io/) or the NFS alternative
  (see below)
- **Windows:** [WinFSP](https://winfsp.dev/)

## NFS fallback (macOS)

macOS 11+ deprecates third-party kexts, which affects macFUSE. Providers
targeting macOS SHOULD offer an NFS-based fallback:

- Serve NFS on `localhost` at a random port
- Map NFS RPCs onto AFI operations equivalent to the FUSE callbacks above
- Mount with `mount -t nfs -o …` from the client side

Turso AgentFS's `agentfs mount --backend nfs` is the reference NFS-fallback
implementation on macOS.

## Access control

Filesystem-level permissions (uid/gid) MAY be used but are advisory —
authoritative access control still flows from the AFI capability token
per [Access control](../basic/access-control.md). Providers SHOULD map
capability-denied paths to `ENOENT` (matching the AFI cloaking rule).

## Performance

- Providers SHOULD implement chunk-level caching for `read` calls issued in
  sequence at the same path
- Providers SHOULD support directory-entry batching in `readdir` to avoid
  N round-trips for large directories
