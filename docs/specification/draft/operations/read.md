---
title: read
description: Read file contents, optionally a byte range.
---

# `read`

Reads file contents, optionally a byte range.

## Signature

```typescript
read(path: string, opts?: {
  offset?: uint64          // default 0
  length?: uint64          // default: whole file from offset
}) -> {
  bytes:        bytes      // transport-specific representation
  content_type: string     // MUST match Entry.content_type
  eof:          boolean    // true iff bytes[bytes.length-1] is the last byte of the file
} | Error
```

## Parameters

| Field | Type | Required | Notes |
|---|---|---|---|
| `path` | string | yes | Must resolve to a file (`type: "file"`) |
| `opts.offset` | uint64 | no | Byte offset to start reading from |
| `opts.length` | uint64 | no | Max bytes to return. Providers MAY cap. |

## Response

```typescript
{
  bytes:        bytes
  content_type: string
  eof:          boolean
}
```

## Error codes

| Code | When |
|---|---|
| `not_found` | Path does not exist OR caller lacks access |
| `invalid_path` | Path is a directory or symlink, or syntactically invalid |
| `internal` | Backing store failed to serve the byte range |

## Example

**Request:**

```
read("/api-reference/create-customer.md", { offset: 0, length: 4096 })
```

**Response (over HTTP):**

```
HTTP/1.1 200 OK
Content-Type: text/markdown
Content-Length: 4096
Content-Range: bytes 0-4095/4213

# Create Customer

Creates a new customer in your account. ...
```

## Semantics

- `read` MUST NOT succeed on a directory. Return `Error{code: "invalid_path"}`.
- `read` on a symlink MUST return `Error{code: "invalid_path"}` — clients
  should `stat` the symlink first and `read` its `target` explicitly.
- `content_type` MUST match the `content_type` returned by `stat` on the
  same path.
- `eof: true` MUST be set iff the returned bytes include the last byte of
  the file. When `offset + bytes.length == file.size`, `eof` is `true`;
  otherwise `false`.
- If `offset` exceeds the file size, `read` MUST return an empty `bytes`
  with `eof: true` — not an error.
- If `length` is `0`, `read` MUST return an empty `bytes` with `eof`
  reflecting whether `offset` is at end-of-file.

## Transport notes

Over HTTP, `read` returns raw bytes as the response body with the
`Content-Type` header set from the response's `content_type` field.
When a range is requested, the provider MUST also set `Content-Range` and
`Accept-Ranges: bytes`.

Over MCP, `bytes` is base64-encoded in the tool result. The MCP tool
result MAY set a MIME hint on the tool response so downstream tooling
displays it correctly.
