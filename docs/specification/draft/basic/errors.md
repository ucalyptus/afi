---
title: Errors
description: Structured error shape and canonical error codes.
---

# Errors

All AFI errors are structured. Providers MUST NOT return unstructured
error messages, bare HTTP status codes without a body, or free-form strings
where an error object is expected.

## Error shape

```typescript
Error {
  code:         ErrorCode          // machine-readable, from the table below
  message:      string             // human-readable
  path?:        string             // the path being operated on, if applicable
  retry_after?: number             // seconds, present iff code == "rate_limited"
  [key: `x-${string}`]: any        // vendor extensions
}

ErrorCode =
  | "not_found"        // path does not exist, OR caller lacks access (see cloaking rule)
  | "invalid_path"     // path is malformed or escapes root
  | "unsupported"      // operation not in the manifest's capabilities
  | "rate_limited"     // provider throttling; retry_after MUST be set
  | "internal"         // provider bug or backing-store failure
  | "unauthorized"     // caller failed auth entirely (missing/invalid token)
  | "conflict"         // (Level 3 only) target already exists / concurrent mod
```

## When to return which code

| Situation | Code |
|---|---|
| Client omits or presents invalid credentials on an auth-required provider | `unauthorized` |
| Client is authenticated but lacks capability for the target path | `not_found` (see [cloaking](access-control.md)) |
| Path syntactically invalid (`\0`, escapes root, exceeds `max_length`) | `invalid_path` |
| Path syntactically valid but nothing exists there | `not_found` |
| Client calls `search` on a Level 1 provider | `unsupported` |
| Client calls `write` on a Level 2 provider | `unsupported` |
| Backing store throws (DB error, disk full, network partition to backend) | `internal` |
| Provider is throttling this client | `rate_limited` |
| (Level 3) `mkdir` on an existing path | `conflict` |

## Transport mapping

Providers MUST map error codes to transport-appropriate signaling *and*
return the structured error body:

### HTTP

| Code | HTTP status |
|---|---|
| `not_found`, `invalid_path` | `404` |
| `unauthorized` | `401` |
| `unsupported` | `405` |
| `rate_limited` | `429` (MUST include `Retry-After` header) |
| `conflict` | `409` |
| `internal` | `500` |

The response body is the structured `Error` as JSON with `Content-Type:
application/json`.

### MCP

Errors surface as MCP tool errors with the structured `Error` as the tool
error payload. The tool call itself completes; MCP transport-level errors
are reserved for actual transport failures.

### FUSE

Codes map to `errno` values: `not_found` → `ENOENT`, `invalid_path` →
`EINVAL`, `unsupported` → `ENOSYS`, `unauthorized` → `EACCES`,
`rate_limited` → `EAGAIN`, `conflict` → `EEXIST`, `internal` → `EIO`.

### SSH

Errors surface as bash-shaped stderr output plus a non-zero exit code.
The stderr line SHOULD be prefixed `afi: <path>: <code>: <message>`.

## Retries

Only `rate_limited` and (in some cases) `internal` are retriable. Clients
SHOULD honor `retry_after`, use exponential backoff for `internal`, and
NOT retry `not_found`, `invalid_path`, `unsupported`, or `unauthorized`.
