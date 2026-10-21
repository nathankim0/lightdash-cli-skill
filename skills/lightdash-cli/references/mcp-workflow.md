# MCP workflow

Use this for a connected MCP server or a request to choose/configure one. This skill does not install a server, mint a token, or enable an enterprise feature automatically.

## Select the actual service

| Surface | What it provides | Setup boundary |
| --- | --- | --- |
| Official data MCP | Project data, governed queries, and permitted content operations | Obtain the instance's configuration from Settings → MCP → Connect. |
| Docs MCP: `https://docs.lightdash.com/mcp` | Public documentation | Not a warehouse connection; no company metrics. |
| Community MCP | Its implemented API subset | Inspect tools, version, auth, and project behavior; see [the source review](ecosystem-research.md). |
| Official CLI | Catalog, SQL, YAML content workflow | Works independently of an MCP installation; see [setup](setup.md). |

The official data endpoint is normally `https://lightdash.example.com/api/v1/mcp`, using Streamable HTTP and browser OAuth. Current self-hosted documentation requires an Enterprise license and `MCP_ENABLED=true`; changing deployment settings is not part of routine content work. Keep using an authorized working CLI if MCP is unavailable. See [official connection guidance](https://docs.lightdash.com/agents/lightdash-mcp) and [self-host prerequisites](https://docs.lightdash.com/self-host/enterprise-features/mcp).

Do not mix credential conventions: the reviewed community implementations use different names (`LIGHTDASH_API_KEY`, `LIGHTDASH_TOKEN`) and URL meanings. Follow that implementation's documented schema. Prefer the host's credential facilities; do not paste tokens into shared configs. Never forward an instance credential to the public Docs MCP.

## Discover capabilities and scope

Inspect live tool schemas once; names below describe the official server, not a portable contract. Community names can be prefixed or hyphenated, and `run_raw_query` can mean a **metric query**, not warehouse SQL.

- Resolve the requested project. Official clients may use `list_projects` / `set_project` / `get_current_project`; newer schemas may also require `projectUuid` on individual operations. Use the schema actually advertised.
- If a trusted client pins `X-Lightdash-Project`, verify that project. Project-switching tools may be hidden intentionally. Do not remove a pin to make a query work.
- Preserve current agent, tag, and user-attribute scope. `route_agent`, when available and relevant, can supply domain context; inspect that context before interpreting search coverage. Do not clear it to widen access or assert that scoped absence proves global absence.
- Load only relevant built-in references through MCP resources or the advertised `list_skills` / `read_skill` / `read_skill_resource` fallback. Missing tool access is a capability limit, not evidence that the data is empty.

## Discover → query → render

1. Find existing content (`find_content`, `list_content`, or `list_verified_content`) and suitable explores/fields. Resolve exact field IDs and filter values from metadata. Follow pagination and record search scope.
2. Prefer `run_metric_query` for an existing governed definition. Use `run_sql` only when needed and permitted; it requires the SQL Runner capability. Keep warehouse queries read-only and bounded. A returned row limit does not cap scanned bytes.
3. Inspect the response contract: distinguish `isError`, running, failed, completed-empty, and completed-with-rows. If a `queryUuid` represents a running job, poll `get_query_result` at the server's suggested interval with a task deadline. Do not resubmit the query. On a deadline, report its status and identifier rather than inventing results.
4. Prefer returned structured rows when provided. Otherwise parse the documented CSV/artifact shape; do not guess fallbacks across implementations. Preserve NULL and truncation metadata.
5. Official `render_chart` uses a completed **metric** query; it does not render SQL Runner results. A successful call is visible only in a compatible MCP App host. Use returned rows for another authorized visual surface or state that rendering was not observed.

The [official analytics skill](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/plugins/lightdash/skills/lightdash-analytics/SKILL.md) and [MCP tool-contract snapshot](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/packages/backend/src/ee/services/McpService/__snapshots__/mcpToolContracts.snapshot.test.ts.snap) support this capability-driven approach; installed servers may differ.

## Edit through the server's contract

**Official server:** where advertised, `read_content` fetches the current object, `edit_content` takes an RFC 6902 patch, and `create_content` accepts a full content-as-code object. Inspect the schema for supported kinds, including SQL charts. Read immediately before editing; use small patches and preconditions where accepted to avoid overwriting concurrent work. After a conflict, re-read and reconcile. Use the returned saved slug/URL, then read back and inspect results. Do not assume a preview visualization was saved.

**`@lightdash-tools/mcp`:** select the profile matching the task. `content-reader` inspects/runs saved content, `data-analyst` runs unsaved semantic queries, and `content-developer` writes content but does not execute warehouse queries. Its write contract requires `preview_*` → `confirm_preview` → apply with the returned token and unchanged proposed payload. A stale preview requires a fresh baseline and preview; bound retries and stop on continuing conflict. The protocol confirmation is not blanket user authorization.

These are distinct products. The community package's preview tokens are not an official-server feature, and its CLI safety-mode/dry-run flags do not protect its MCP surface. For its stdio transport a profile is required; read-only profiles are enough for inspection. Choose SQL authoring via the official server/CLI when a community profile excludes it. See [profile documentation](https://github.com/yu-iskw/lightdash-tools/blob/6f5522523a47a04c30e9823991ee2af5a97d47b9/packages/mcp/README.md), [write boundaries](https://github.com/yu-iskw/lightdash-tools/blob/6f5522523a47a04c30e9823991ee2af5a97d47b9/docs/adr/0014-mcp-content-developer-profile-mutation-boundary.md), and [scope rules](https://github.com/yu-iskw/lightdash-tools/blob/6f5522523a47a04c30e9823991ee2af5a97d47b9/docs/adr/0008-mcp-request-scope-and-hardening.md).

Do not route a rejected or unauthorized write through a different interface to evade the restriction. Schedules, recipients, permissions, verification badges, model writeback/PRs, data apps, and organization administration require their own requested scope. For asynchronous writeback/build jobs, follow the returned job status; do not create duplicate jobs when a response times out.
