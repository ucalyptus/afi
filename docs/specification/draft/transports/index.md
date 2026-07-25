---
title: Transports
description: How AFI operations travel — MCP, HTTP, FUSE, SSH.
---

# Transports

AFI operations are transport-agnostic. This section defines four
concrete bindings; any implementation that preserves AFI semantics is
conformant regardless of transport.

| Binding | Best for | Discovery method |
|---|---|---|
| **[MCP tools](mcp.md)** *(recommended)* | Agent runtimes that already speak MCP | `afi.manifest()` tool + `/.afi/manifest.json` as MCP resource |
| **[HTTP/JSON](http.md)** | Web-native providers, direct API integration | `GET /.afi/manifest.json` |
| **[FUSE](fuse.md)** | Local mounts, "look like a real disk" UX | Manifest served as regular file |
| **[SSH](ssh.md)** | Public terminal-accessible providers (à la supabase.sh) | `cat /.afi/manifest.json` |

## Multi-binding providers

A single provider MAY expose multiple bindings simultaneously — commonly
MCP + HTTP. Each binding MUST advertise the same profile and capabilities.

## Choosing a binding

For a new provider, pick based on your primary consumer:

- **Agent runtimes only:** MCP alone is enough. Ship it as an MCP server.
- **Agent runtimes + human tools + curl:** MCP + HTTP.
- **Local tools + mount into shells:** FUSE.
- **"Anyone with a terminal should be able to poke at this":** SSH.

Providers can always add more bindings later without breaking existing
consumers, as long as the underlying operations preserve their semantics.
