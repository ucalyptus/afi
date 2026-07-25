---
title: HTTP/JSON binding
description: Expose AFI operations as HTTP endpoints returning JSON.
---

# HTTP/JSON binding

A REST-style binding for direct integration, web frontends, and `curl`.

## Endpoints

| Endpoint | Operation | Content-Type |
|---|---|---|
| `GET /.afi/manifest.json` | Discovery manifest | `application/json` |
| `GET /afi/stat?path=…` | [`stat`](../operations/stat.md) | `application/json` |
| `GET /afi/list?path=…&limit=…&cursor=…&glob=…` | [`list`](../operations/list.md) | `application/json` |
| `GET /afi/read?path=…&offset=…&length=…` | [`read`](../operations/read.md) | *(from response)* |
| `POST /afi/search` | [`search`](../operations/search.md) | `application/json` |

Level 3 additionally: `POST /afi/write`, `POST /afi/mkdir`, `DELETE
/afi/delete?path=…`, `POST /afi/move`.

## Request encoding

- Query parameters for `GET` operations. Multi-value parameters are not
  used.
- JSON bodies for `POST` operations.
- Paths in query strings MUST be percent-encoded per
  [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986).

## Response encoding

For `stat`, `list`, `search`, `manifest`: JSON body matching the operation
response schema.

For `read`: raw bytes as the response body. The `Content-Type` header MUST
be set from the response's `content_type` field. When a byte range was
requested:

- `Accept-Ranges: bytes` MUST be present
- `Content-Range: bytes <start>-<end>/<total>` MUST be present
- HTTP status is `206 Partial Content` when a range was requested and
  fulfilled, `200 OK` when the whole file was returned

## Error responses

Errors are JSON bodies matching the [`Error`](../basic/errors.md) schema.
HTTP status code is set per the mapping in [Errors → HTTP](../basic/errors.md#http).

Example:

```
HTTP/1.1 404 Not Found
Content-Type: application/json

{"code":"not_found","message":"no such path","path":"/does/not/exist"}
```

## Auth

Auth via the `Authorization` header:

```
Authorization: Bearer <token>
```

Providers MAY accept other auth methods and MUST declare them in
`manifest.auth.methods`.

## CORS

Providers targeting browser-based clients SHOULD respond to preflight
`OPTIONS` requests with permissive CORS headers. Providers targeting
server-side integrations only MAY omit CORS support.

## Pagination

Cursors returned in `next_cursor` fields are opaque strings. Clients pass
them back verbatim in the `cursor` query parameter for `list` and in the
JSON body for `search`.

## Rate limiting

Providers throttling requests MUST return HTTP 429 with:

- A `Retry-After` header (seconds)
- A JSON error body with `code: "rate_limited"` and `retry_after` matching
  the header

## Example — end-to-end

```
$ curl https://provider.example.com/.afi/manifest.json
{"afi_version":"0.1","provider":"my-docs","profile":2,...}

$ curl "https://provider.example.com/afi/list?path=/&limit=10" \
    -H "Authorization: Bearer $TOKEN"
{"entries":[{"path":"/api-reference","type":"directory",...}], "next_cursor":"..."}

$ curl "https://provider.example.com/afi/read?path=/api-reference/users" \
    -H "Authorization: Bearer $TOKEN"
# Users

Manage users in your account. ...
```
