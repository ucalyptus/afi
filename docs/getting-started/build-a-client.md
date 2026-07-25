---
title: Quickstart — build a client
description: Consume any AFI provider from your agent runtime.
---

# Quickstart — build a client

This walkthrough integrates AFI into an agent runtime so it can transparently
work with any conformant provider.

## The client contract

An AFI client MUST:

1. Fetch `/.afi/manifest.json` before issuing other operations
2. Honor the declared `profile` and `capabilities` — do not call operations
   the manifest didn't declare
3. Tolerate unknown fields on responses (forward compatibility)
4. Treat `not_found` uniformly for missing and unauthorized paths

## Minimal client

=== "TypeScript"

    ```typescript
    import { z } from "zod";

    const ManifestSchema = z.object({
      afi_version: z.string(),
      provider: z.string(),
      profile: z.number(),
      capabilities: z.array(z.string()),
      auth: z.object({ required: z.boolean(), methods: z.array(z.string()) }),
    }).passthrough();  // forward compat

    export class AfiClient {
      private manifest: Manifest | null = null;

      constructor(private baseUrl: string, private token?: string) {}

      async ready(): Promise<Manifest> {
        if (this.manifest) return this.manifest;
        const res = await fetch(`${this.baseUrl}/.afi/manifest.json`);
        this.manifest = ManifestSchema.parse(await res.json());
        return this.manifest;
      }

      async list(path: string, opts: ListOpts = {}) {
        await this.ready();
        const url = new URL(`${this.baseUrl}/afi/list`);
        url.searchParams.set("path", path);
        if (opts.glob) url.searchParams.set("glob", opts.glob);
        if (opts.cursor) url.searchParams.set("cursor", opts.cursor);
        return handle(await fetch(url, { headers: this.headers() }));
      }

      private headers(): Record<string, string> {
        return this.token ? { Authorization: `Bearer ${this.token}` } : {};
      }
    }
    ```

## Expose AFI to your LLM

The simplest path: wrap AFI operations as tools your model can call. If the
provider offers an [MCP binding](../specification/draft/transports/mcp.md),
you can wire it in as an MCP server and get this for free.

Otherwise, register four tools mirroring the operations:

```typescript
agent.registerTool({
  name: "afi_search",
  description: "Search across the provider's tree. Returns matches with path, line, snippet.",
  parameters: { query: "string", path: "string?" },
  handler: (args) => client.search(args.query, { path: args.path }),
});
```

Give the model a system-prompt hint like:

> You have access to a filesystem-shaped data source via `afi_*` tools.
> Start with `afi_list("/")` to see the shape. Use `afi_search` for exact
> string matches. Use `afi_read` to pull specific files into context.

## Handle pagination

Responses may include `next_cursor`. Loop until it's absent:

```typescript
async function *listAll(client: AfiClient, path: string) {
  let cursor: string | undefined;
  do {
    const page = await client.list(path, { cursor });
    yield* page.entries;
    cursor = page.next_cursor;
  } while (cursor);
}
```

## Handle capability gaps

Not every provider offers every operation. Consult the manifest first:

```typescript
const manifest = await client.ready();
if (!manifest.capabilities.includes("search")) {
  // fall back to listing + reading, or refuse the query
}
```

## Next

- Read the [architecture overview](../learn/architecture.md)
- See how [existing providers map onto AFI](../providers/index.md)
- If your client needs an operation AFI doesn't yet standardize,
  [propose an AEP](../community/aep-guidelines.md)
