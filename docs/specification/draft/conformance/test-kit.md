---
title: Test kit plan
description: The planned AFI conformance test harness.
---

# Test kit plan

!!! info "Status: not yet implemented"
    This page describes the planned conformance test kit. The kit itself is
    tracked as [AEP-0003](../../../community/aep-guidelines.md) (proposed).
    Contributions welcome.

## Goal

An AFI provider is **conformant at level N** if it passes every scenario
tagged `level<=N` in the reference test kit. Providers self-report their
level in the manifest; the test kit verifies that self-report is honest.

## Reference implementation

- **Language:** TypeScript (`@afi/conformance`)
- **Runtime:** Node 20+
- **Delivery:** `npx @afi/conformance`
- **Ports welcome:** Rust, Python, Go — coordinated via AEPs

## Invocation

```bash
npx @afi/conformance --target <URL> [--token <TOKEN>] [--profile <N>]
```

- `--target` — AFI provider base URL (HTTP transport) or MCP server URL
- `--token` — capability token, if `auth.required: true`
- `--profile` — max profile level to test (defaults to the manifest's
  declared level)

## Scenario groups (~50 scenarios)

### Discovery (10)
- Manifest is served at the correct URL
- Manifest matches the schema
- `capabilities` is consistent with declared `profile`
- `search.modes` includes `"exact"` if profile ≥ 2
- Unknown fields on the manifest are tolerated by the kit (i.e., the kit
  itself is forward-compatible)

### Path model (8)
- Root `/` is stat-able
- Paths with `..` are rejected with `invalid_path`
- Paths with null bytes are rejected
- Case-sensitivity behavior matches manifest declaration
- Trailing slashes are normalized

### `stat` / `list` (10)
- `stat` on non-existent path returns `not_found`
- `list` on file returns `invalid_path`
- `list` returns entries with valid `Entry` shape
- `list` pagination: cursor round-trip yields consistent ordering
- `list` `glob` filters child names correctly

### `read` (8)
- Whole-file read returns correct bytes
- Byte range with offset/length returns the correct slice
- `offset` past EOF returns empty bytes with `eof: true`
- `content_type` matches `stat`'s value
- Reading a directory returns `invalid_path`

### `search` (8)
- Exact search returns matches with correct `path`, `line`, `text`
- Search with `opts.path` scopes correctly
- Search on inaccessible subtree returns `not_found`
- If regex mode is declared, invalid regex returns `invalid_path`
- If semantic mode is declared, `score` is present and in `[0,1]`

### Access control (4)
- Missing token on auth-required provider returns `unauthorized`
- Wrong-scope token: inaccessible paths return `not_found` (not `unauthorized`)
- Inaccessible entries omitted from `list` results
- Inaccessible matches omitted from `search` results

### Errors (2)
- All errors have the structured shape (no bare strings)
- Rate-limit errors include `retry_after`

## Output

```
$ npx @afi/conformance --target https://provider.example --profile 2

AFI conformance test kit v0.1
Target: https://provider.example
Declared profile: 2 (Search)

Discovery ........................... 10/10 PASS
Path model .......................... 8/8   PASS
stat / list ......................... 10/10 PASS
read ................................ 8/8   PASS
search .............................. 7/8   FAIL
  ✗ search-04: regex mode declared but invalid regex returned "internal"
                expected "invalid_path"
Access control ...................... 4/4   PASS
Errors .............................. 2/2   PASS

Result: 49/50 scenarios passed
Provider does NOT conform to profile 2 (regex sub-capability failing).
Exit: 1
```

## CI integration

Providers SHOULD run the kit against their staging environment on every
PR. The AFI project MAY publish a badge (`AFI Level 2 Conformant`) once
the kit reaches v1.0.
