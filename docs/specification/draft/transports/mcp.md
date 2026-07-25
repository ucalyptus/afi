---
title: MCP tools binding
description: Expose AFI operations as MCP tools.
---

# MCP tools binding

The recommended transport. Expose AFI operations as MCP tools on your MCP
server. Any MCP-capable agent runtime becomes an AFI client transparently.

## Tool naming

All AFI tools use the `afi.` prefix:

| MCP tool | AFI operation |
|---|---|
| `afi.manifest` | (returns the discovery manifest) |
| `afi.stat` | [`stat`](../operations/stat.md) |
| `afi.list` | [`list`](../operations/list.md) |
| `afi.read` | [`read`](../operations/read.md) |
| `afi.search` | [`search`](../operations/search.md) |

Providers at Level 3 additionally expose `afi.write`, `afi.mkdir`,
`afi.delete`, `afi.move`.

## Tool schemas

Input schemas MUST match the operation parameters defined in the
[Operations](../operations/index.md) section. Providers SHOULD publish
their input schemas via MCP's `inputSchema` field.

Output schemas MUST match the operation response shapes. Where AFI defines
`bytes` (for `read`), the MCP tool result MUST base64-encode the bytes.
The MCP `mimeType` hint SHOULD be set to the response's `content_type`.

## Errors

AFI errors surface as MCP tool errors with the structured
[`Error`](../basic/errors.md) as the tool error payload. The tool call
itself completes; MCP transport-level errors (connection loss, protocol
violation) are reserved for actual transport failures.

## Manifest as MCP resource

Providers SHOULD *also* expose `/.afi/manifest.json` as an MCP resource
with URI `afi://manifest.json`, so clients can retrieve it via MCP's
resource protocol without a special tool call. This is redundant with
`afi.manifest()` but matches MCP's conventions for static metadata.

## Auth

MCP's session-level auth (transport-defined) satisfies AFI's auth
requirements. Providers MUST enforce the [cloaking rule](../basic/access-control.md)
consistently across every tool call.

## Example — end-to-end

An MCP server registers five AFI tools:

```typescript
server.registerTool({
  name: "afi.stat",
  description: "Return metadata for one AFI path.",
  inputSchema: {
    type: "object",
    required: ["path"],
    properties: {
      path: { type: "string", description: "POSIX absolute path" }
    }
  },
  handler: async ({ path }) => afi.stat(path),
});
// ...same for list, read, search, manifest
```

A Claude Code / MCP-aware runtime discovers these tools automatically and
can use them without any AFI-specific code.

## Composition

Providers can ship *both* AFI-shaped MCP tools *and* other MCP tools on
the same server. AFI tools use the `afi.` namespace; provider-specific
tools SHOULD use a different namespace to avoid collision.
