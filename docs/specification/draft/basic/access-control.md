---
title: Access control
description: Capability tokens and the cloaking rule.
---

# Access control

AFI does not mandate a specific auth scheme. It does mandate that
authorization filtering is *invisible* to unauthorized clients.

## Capability tokens

- Requests MAY carry a capability token in a transport-defined way:
    - **HTTP:** `Authorization: Bearer <token>`
    - **MCP:** the session's transport-level auth
    - **FUSE:** the mount's process credentials
    - **SSH:** the authenticated SSH session
- Providers MUST enforce that the token's grants cover every path returned
  or read.

## The cloaking rule

This is the single most important access-control rule in AFI:

!!! warning "Providers MUST NOT distinguish 'not found' from 'not authorized'"
    For paths the caller lacks access to:

    - `stat`, `read` MUST return `Error{code: "not_found"}`
    - `list` MUST omit the entry from the returned array
    - `search` MUST omit matches whose containing path is inaccessible
    - `list` on an inaccessible directory MUST return `Error{code: "not_found"}`

Rationale: leaking existence via error codes is a common access-control bug.
A caller who cannot read `/secret/passwords.txt` MUST NOT be able to
distinguish it from `/does-not-exist.txt`. This is enforced by returning
`not_found` uniformly.

## Filtering at query time

Providers SHOULD filter *before* composing results, not after. Practical
consequences:

- For `list`, apply the capability filter as part of the child-enumeration
  query, not as a post-filter that could reveal counts.
- For `search`, incorporate the capability filter into the search backend
  (e.g. a `where_document` clause on a vector DB, an `EXISTS` clause on a
  SQL query) so that `next_cursor` doesn't accidentally leak the total
  filtered-out count.

## Reference implementation pattern

Mintlify's ChromaFs implements this by pre-pruning the in-memory path tree
per session, so unauthorized entries literally do not exist from the
client's perspective. This is the recommended pattern for providers with
a per-session capability model. See the
[ChromaFs adapter note](../../../providers/chromafs.md) for details.

## Anonymous access

Providers that expose fully public data MAY set `auth.required: false` in
the manifest. In that case:

- Clients need not send credentials.
- All paths in the tree are, by definition, accessible.
- The cloaking rule still applies to genuinely non-existent paths.

## Audit logging

AFI does not require providers to log requests. Providers MAY, and
SHOULD when handling non-public data. Log fields SHOULD include: caller
identity (if any), operation name, requested path, response code, byte
count. Log fields SHOULD NOT include full request bodies for `search`
queries containing potentially sensitive text.
