---
title: Prior art
description: What AFI borrowed, what it deliberately didn't.
---

# Prior art

AFI is not a new idea. It is a small refactoring of ideas that already work,
adapted for the specifics of AI-agent context. This page credits and
differentiates.

## POSIX

**Borrowed:** paths (`/foo/bar`), file/directory/symlink distinction, `stat`
metadata shape (mode, size, timestamps), permission model semantics.

**Not borrowed:** the actual POSIX API surface (~30 syscalls beyond the
essentials). AFI is deliberately smaller.

## 9P (Plan 9)

**Borrowed:** the idea that everything is a file — including operations
themselves (see the FUSE binding's `/.afi/search` synthetic file).

**Not borrowed:** 9P's message-oriented wire protocol. AFI is transport-
agnostic; if you want a wire, ride HTTP or MCP.

## FUSE

**Borrowed:** the operation set (`getattr`, `readdir`, `read`), the mental
model that a filesystem is a set of callbacks against a namespace.

**Not borrowed:** the kernel-userspace round-trip model. Most AFI providers
run entirely in userspace, and the [FUSE binding](../specification/draft/transports/fuse.md)
is one of four — not the primary one.

## WebDAV

**Borrowed:** the notion of a filesystem over HTTP with structured metadata
and access control.

**Not borrowed:** WebDAV's XML-heavy protocol, PROPFIND/PROPPATCH semantics,
and lock model. AFI is a JSON world.

## MCP (Model Context Protocol)

**Borrowed:** the tool-calling model, the pattern of a small transport
grammar with rich extensibility, the discipline of "small spec, generous
extensions".

**Not competing with:** MCP is a transport/RPC framework; AFI is a data-shape
spec. AFI's [recommended transport binding](../specification/draft/transports/mcp.md)
is MCP itself.

## CSI (Container Storage Interface)

**Borrowed:** the entire naming pattern (interface, not implementation), the
conformance-testing culture, the vendor-neutrality strategy of "seek a
neutral home once we have ≥2 implementations".

**Not borrowed:** CSI's gRPC-only transport, Kubernetes-specific concepts
(volumes, mounts, node/controller split). AFI is not Kubernetes-shaped.

## LSP (Language Server Protocol)

**Borrowed:** the discipline of a small operation set, capability
negotiation via a startup handshake, and the "editor-agnostic, language-
agnostic" positioning that let LSP outlive its Microsoft origins.

**Not borrowed:** LSP's JSON-RPC framing (AFI is transport-plural), or
LSP's text-document-centric worldview.

## RAG (Retrieval-Augmented Generation)

**Not borrowed at all.** RAG assumes the retrieval is invisible to the
agent; AFI assumes the agent explicitly navigates. These are complementary,
not competing — a RAG system's underlying corpus can be *exposed* as an AFI
provider, letting the agent choose between opaque retrieval and explicit
exploration.

## What's genuinely new

Nothing about AFI is technically novel. The contribution is:

1. **A shared shape** for the "virtual FS over data" pattern the ecosystem
   was going to converge on anyway
2. **Explicit conformance profiles** so providers can honestly declare what
   they support
3. **A discovery manifest** that decouples client capability from provider
   capability
4. **Vendor-neutral governance from day one** so the spec doesn't calcify
   around any one company's needs
