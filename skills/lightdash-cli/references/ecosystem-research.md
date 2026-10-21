# Lightdash ecosystem review

Reviewed **2026-10-21**. Discovery used the official documentation, GitHub repository/code searches for Lightdash MCP and skills, npm package metadata, and primary source files. The table records **11 upstream repositories**; mirrors were deduplicated. This is a dated public-source survey, not a claim to cover private integrations or every unpublished project. Source inspection is not a live compatibility or security audit.

Commit links below pin the inspected revision. Follow upstream for newer releases, but recheck the installed tool schema before relying on a new capability.

| Project / inspected revision | License at that revision | Evidence and what this skill adopts |
| --- | --- | --- |
| [Official Lightdash](https://github.com/lightdash/lightdash/tree/848af49f45b9cb0be73d10f7822427982c556087) | MIT outside stated exceptions; backend `ee` uses a separate Source Available License | CLI skill, analytics plugin, MCP service/tool contracts, and content/metric references. Adopt capability discovery, semantic queries, polling, content read/patch/create, and loading versioned skill resources. |
| [syucream/lightdash-mcp-server](https://github.com/syucream/lightdash-mcp-server/tree/952db23a8f1269b01372e332120cc2a2fd68247d) | MIT | README and `src/mcp.ts` expose project, space, chart, dashboard, catalog, and as-code reads. Adopt metadata-first discovery; this server is not a substitute for a query/write surface it does not expose. |
| [poddubnyoleg/lightdash_mcp](https://github.com/poddubnyoleg/lightdash_mcp/tree/a0e98202b50fcdf3ce5656f94c671d8d72eedd13) | MIT | Python tool registry plus chart-query, SQL-chart, and dashboard-filter implementations. Adopt tile-specific execution/QA and separate chart/layout/filter operations. Never accept its first-project default without resolving the user's target. |
| [niccoloalexander/lightdash-mcp](https://github.com/niccoloalexander/lightdash-mcp/tree/e6aa5ff00f694d4d2056f5aa4835bf1329dd4dab) | MIT | README/package metadata describe saved-chart reads and semantic queries. Adopt a read-only connection as sufficient for inspection. Package metadata points to another repository; see provenance caveat below. |
| [yu-iskw/lightdash-tools](https://github.com/yu-iskw/lightdash-tools/tree/6f5522523a47a04c30e9823991ee2af5a97d47b9) | Apache-2.0 | MCP README, scope/write ADRs, and reader/developer/analyst playbooks. Adopt profile-aware routing, immutable preview payloads, stale-baseline recovery, result limits, and definition comparison. Its preview protocol is implementation-specific. |
| [yu-iskw/lightdash-mcp-demo](https://github.com/yu-iskw/lightdash-mcp-demo/tree/334fbbc55bc3cdcf9961668205b5001fea81c896) | Apache-2.0 | README and server setup illustrate per-user OAuth forwarding; the demonstrated primary tool is authenticated-user lookup. Useful auth reference, not evidence of a complete analytics server. |
| [Endiruslan/tableau-to-lightdash-skill](https://github.com/Endiruslan/tableau-to-lightdash-skill/tree/29ecb4ef012c2cff074fa94e6078e1596ae1ffcc) | MIT; bundled upstream schemas have their own attribution | Migration entrypoint and README. Adopt source-definition discovery, explicit unsupported-feature mapping, and numerical parity checks. Link its specialized parsing references instead of duplicating them. |
| [Workday/dj](https://github.com/Workday/dj/tree/ffe0d66384eea86b5e76f68e0de9582c18569e8d) | Apache-2.0 | Agent-skill index and Lightdash create/edit templates. Adopt preserving unfamiliar keys, stable IDs, required filters, tile references, and upstream generator ownership. DJ-only commands and restrictions stay DJ-only. |
| [joaoviana/sundial](https://github.com/joaoviana/sundial/tree/666f3ae2f2999abae954cb72ccb2237c35a0a227) | AGPL-3.0 | README and HTTP MCP client illustrate explicit session handling and query-result-driven visuals. Retain the distinction between data and a rendered artifact; music/WebGL features are outside this skill. No code imported. |
| [yu-iskw/lightdash-ops](https://github.com/yu-iskw/lightdash-ops/tree/46cdfa566decbe285ad33b29fa0506a8912305ff) | Apache-2.0 | README describes organization/user/space administration via an alternative CLI. Recognize administration as a separate requested task; do not add it to ordinary chart authoring. |
| [yu-iskw/lightdash-client-python](https://github.com/yu-iskw/lightdash-client-python/tree/3d8a31af76216ce4355178d618d3921f0582b671) | Apache-2.0 | README describes generated API bindings and incomplete coverage. Prefer a working supported client; generated bindings do not establish endpoint/version compatibility. No additional SDK dependency needed here. |

## Implementation map

| Finding | Where it changes the skill |
| --- | --- |
| Official data MCP and public Docs MCP serve different purposes | [MCP setup and routing](mcp-workflow.md#select-the-actual-service) |
| Tool names, project context, and query result envelopes differ by server | [Live capability discovery](mcp-workflow.md#discover-capabilities-and-scope) |
| Running queries must be polled, not resubmitted | [Query and render flow](mcp-workflow.md#discover--query--render) |
| Patch writes and preview-token writes are different contracts | [MCP editing](mcp-workflow.md#edit-through-the-servers-contract) |
| A semantic metric can already exist without a saved chart | [Semantic-layer workflow](semantic-layer.md) |
| Unknown properties and references can be lost in whole-object rewrites | [Dashboard preservation](dashboards-and-migrations.md#preserve-the-graph-not-just-the-picture) |
| Migration needs measurement parity as well as visual similarity | [Migration checks](dashboards-and-migrations.md#migrate-with-a-parity-record) |

## Do not import these assumptions

- **“Every Lightdash deployment has official data MCP.”** Current [self-host documentation](https://docs.lightdash.com/self-host/enterprise-features/mcp) requires a licensed, enabled deployment. Docs MCP remains a separate public documentation service. Do not bypass deployment restrictions or change licensing/settings as part of this skill.
- **“All community MCP tools match official names and payloads.”** For example, the Python server's `run-raw-query` takes a metric query; it is not the official server's `run_sql`. Read the advertised schema.
- **“A new chart always requires `--force`.”** Some framework guidance says so; CLI 2.616.2 created a selected new SQL chart without it in the exercised workflow. Do not promote a framework/version observation into a universal requirement.
- **“Every write needs a new approval conversation, or a dashboard shell.”** Some upstream playbooks choose those policies. Honor the actual user's scope and the active host's approval rules; a one-chart request does not require another dashboard.
- **“Read-only mode means no query cost, or a safe warehouse credential.”** Distinguish metadata access from query execution and actual upstream permissions. Tool annotations and package descriptions are not independent enforcement guarantees.
- **“Package and repository names prove source identity.”** npm's `lightdash-mcp` metadata points to `timeleft-dev/lightdash-mcp`, which returned 404 during this review. The inspected `niccoloalexander` checkout has matching package metadata but was not proven identical to the published tarball. Do not recommend blind execution of that package based on this review.
- **“Documentation never disagrees.”** Official pages described different default SQL row limits; server version/configuration can change them. Use the live schema, response truncation, and deployment settings rather than a hard-coded default.

## Attribution and verification boundary

This repository adds original workflow instructions and links to primary sources. It does **not** vendor upstream implementations, enterprise source, schemas, or copied skill text. Its own material remains MIT; that does not relicense any linked project. Before incorporating actual upstream code later, review that file's applicable license and preserve its required notices. Official licensing is recorded in the [root license](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/LICENSE) and [enterprise-directory license](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/packages/backend/src/ee/LICENSE).

The earlier CLI content workflow was exercised on 2.616.2. The newly documented MCP implementations were source-reviewed, not installed against a private instance. No credentials, organization data, or private URLs belong in this review. Revalidate live tools and permissions when using one of these integrations.
