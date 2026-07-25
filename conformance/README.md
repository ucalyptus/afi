# Conformance

The conformance test kit is not yet written. This directory reserves the
namespace and sketches the plan.

## Goal

An AFI provider is *conformant at level N* if it passes every scenario tagged
`level<=N` in the test kit. Providers self-report their level in the manifest;
conformance testing checks that self-report is honest.

## Plan (v0.1)

- One TypeScript test kit as the reference. Other language ports welcome once
  the v0.1 kit is stable.
- Kit talks to a provider over any AFI transport binding (§7 of the spec).
- ~50 scenarios across:
  - **Discovery** (manifest well-formed, capabilities match declared profile)
  - **Path model** (POSIX rules, root, invalid paths, case sensitivity)
  - **stat/list** (basic, pagination, glob, non-existent, non-directory)
  - **read** (full, offset, length, EOF, content-type consistency)
  - **search** (exact required, regex/semantic if declared, ordering, limits)
  - **Access control** (capability filtering, not_found for unauthorized)
  - **Errors** (structured shape, retry_after semantics)

## Reference invocation (proposed)

```bash
npx @afi/conformance --target https://your-provider.example --token $TOKEN
# → prints pass/fail per scenario, exits non-zero on any failure
```

## Status

Placeholder. Kit will be scoped by RFC 0003 once the v0.1 spec has settled.
