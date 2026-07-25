---
title: Quickstart — build a provider
description: Wrap your data source as an AFI provider in ~30 minutes.
---

# Quickstart — build a provider

This walkthrough exposes a corpus of local Markdown files as an AFI provider
over HTTP. Substitute your own backing store where noted.

## What you'll build

A single HTTP endpoint that responds to:

- `GET /.afi/manifest.json`
- `GET /afi/stat?path=…`
- `GET /afi/list?path=…`
- `GET /afi/read?path=…`
- `POST /afi/search`

…and passes the [Level 2 (Search)](../specification/draft/conformance/profiles.md)
conformance profile.

## 1. Pick a profile

Providers declare a conformance level:

| Level | Name | Required |
|-------|------|----------|
| 0 | Discovery | `stat`, `list` |
| 1 | Read | + `read` |
| 2 | Search | + `search` (exact mode minimum) |
| 3 | Read/Write | + `write`, `mkdir`, `delete`, `move` |

For a docs provider, Level 2 is the sweet spot. For an agent-state
provider, target Level 3.

## 2. Serve the manifest

```json title="/.afi/manifest.json"
{
  "afi_version":      "0.1",
  "provider":         "my-docs",
  "provider_version": "0.1.0",
  "profile":          2,
  "capabilities":     ["stat", "list", "read", "search"],
  "search":           { "modes": ["exact"], "max_results": 100 },
  "path":             { "case_sensitive": true, "max_length": 4096 },
  "auth":             { "required": false, "methods": [] }
}
```

Clients fetch this before any other request. See
[Discovery manifest](../specification/draft/basic/discovery.md) for the full
schema.

## 3. Implement the operations

The four operations, with the shapes clients expect:

=== "TypeScript"

    ```typescript
    // stat(path) — return metadata for one path
    app.get("/afi/stat", (req, res) => {
      const entry = fs.statSync(resolve(req.query.path));
      const isDir = entry.isDirectory();
      res.json({
        path: req.query.path,
        type: isDir ? "directory" : "file",
        size: isDir ? 0 : entry.size,
        // toISOString() emits RFC 3339 in UTC ("…Z")
        mtime: entry.mtime.toISOString(),
        ctime: entry.ctime.toISOString(),
        content_type: isDir
          ? "inode/directory"
          : (mimeLookup(req.query.path) ?? "application/octet-stream"),
      });
    });
    ```

=== "Python"

    ```python
    import os
    import stat as stat_ops        # POSIX bit helpers (S_ISDIR, ...)
    import mimetypes
    from datetime import datetime, timezone

    @app.get("/afi/stat")
    def stat(path: str):
        s = os.stat(resolve(path))
        is_dir = stat_ops.S_ISDIR(s.st_mode)
        return {
            "path": path,
            "type": "directory" if is_dir else "file",
            "size": 0 if is_dir else s.st_size,
            # Timezone-aware UTC → RFC 3339 with "+00:00" suffix
            "mtime": datetime.fromtimestamp(s.st_mtime, tz=timezone.utc).isoformat(),
            "ctime": datetime.fromtimestamp(s.st_ctime, tz=timezone.utc).isoformat(),
            "content_type": (
                "inode/directory" if is_dir
                else (mimetypes.guess_type(path)[0] or "application/octet-stream")
            ),
        }
    ```

Then implement `list`, `read`, `search` following the same shape.
See [Operations](../specification/draft/operations/index.md) for the full
schemas.

## 4. Handle errors uniformly

Return structured errors, never bare 500s:

```json
{ "code": "not_found", "message": "no such path", "path": "/does/not/exist" }
```

Return `not_found` for *both* missing paths and paths the caller isn't
authorized to see — clients rely on this cloaking (see
[Access control](../specification/draft/basic/access-control.md)).

## 5. Test against the conformance kit

```bash
npx @afi/conformance --target http://localhost:3000 --profile 2
```

(The conformance kit is still under construction; see the
[test kit plan](../specification/draft/conformance/test-kit.md).)

## 6. Register your provider

Publish an adapter note under [`docs/providers/`](../providers/index.md) so
agents can discover you.

## Next

- Add an [MCP transport binding](../specification/draft/transports/mcp.md)
  alongside HTTP — most agent runtimes prefer it
- If you need mutation, [add Level 3 operations](../specification/draft/conformance/profiles.md#level-3-readwrite)
- Read the [design principles](../community/design-principles.md) before
  proposing extensions
