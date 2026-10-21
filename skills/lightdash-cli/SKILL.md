---
name: lightdash-cli
description: Inspect governed metrics and create or edit Lightdash charts and dashboards using the CLI or an available MCP server. Use for content discovery, metric validation, SQL charts, scoped publishing, and semantic-layer or migration workflows.
license: MIT
---

# Lightdash CLI

Complete the requested Lightdash task using the working connection and the smallest change. Use the user's own instance, project, warehouse, and permissions; this skill contains no default organization or credentials. Keep the `lightdash-cli` invocation name for existing installations; MCP is an optional execution path.

## Start with the working connection

- Honor an explicit CLI or MCP preference. Otherwise use the available connection that supports the task; do not require both. For MCP, read [mcp-workflow.md](references/mcp-workflow.md), discover its live tools, and verify project/agent scope. For CLI, check `lightdash --version` and `lightdash config get-project`; read [setup.md](references/setup.md) only when needed.
- Keep a working connection instead of making an unrelated connector's repair or a new server installation a prerequisite. Docs MCP supplies documentation, not company data.
- Check the relevant CLI command's installed `--help` or the MCP tool schema once. Treat [field-notes.md](references/field-notes.md) as versioned observations, not permanent product guarantees.
- Keep downloads, query results, and drafts in a task scratch directory outside unrelated source repositories. Do not commit private content to this public skill repository.

## Keep execution short

- For another chart in the same active project, start from the latest related saved chart and this task's verified source definition. Refresh only the destination/slug collision check; do not repeat catalog discovery, connection setup, or unchanged join/grain audits. Change the formula, scope, labels, and visualization only as needed. Reopen source discovery when evidence is stale or the requested definition needs different fields, joins, or permissions.
- For that follow-up path, use one bounded source query for the new formula's population, freshness, and invariants; check it against the final chart query. Run lint alongside query execution, then upload and read back. Review the saved object's key fields without inventing a new verification framework. A new scope or a failed check justifies deeper investigation; a passed check does not.
- Batch independent reads: fetch content and relevant source metadata together; combine freshness, grain, null, and count checks into a bounded aggregate query where practical. Inspect every result before using it.
- Reuse this task's snapshots, verified schemas, and working connection. Search/filter locally before fetching more. Stop discovery once the requested gap, definition, scope, and source are established; do not add an unrelated audit or repeat a passed check without new evidence.
- Run independent lint/query checks concurrently. Keep dependent stages ordered: resolve the schema before composing a query, validate before writing, then read back. After a successful write, fetch the saved object and render/export in parallel where supported. Bound concurrency to the service's limits and keep writes to the same object sequential.
- When timing is requested, measure end-to-end wall time, including agent reasoning and tool orchestration. Record discovery/authoring separately from query and publishing time so repeated preparation is visible. Do not promise a duration before measuring this path, or remove correctness checks to meet an arbitrary deadline.

## Inspect before choosing or editing

For CLI content authoring, read [content-workflow.md](references/content-workflow.md) for commands and chart formats.

- For a specific edit, read the referenced chart or dashboard through the chosen interface. For CLI, download it. Include dependencies only when the change reaches them.
- To find a missing metric, inspect existing semantic metrics as well as accessible regular charts, SQL charts, and dashboards. Search relevant terms first; use a broader inventory when absence matters. Follow pagination or report partial coverage. Compare definitions, not just labels; never call an agent/tag-limited search an organization-wide inventory.
- Filter warehouse catalog discovery by known database/schema/table before printing it. Read source descriptions and relevant values before deciding what a metric means.
- Reuse a deployed metric or an existing chart pattern when it fits. A saved SQL chart is not a reusable semantic-layer metric: if the user requests the latter, change the relevant model and use the semantic-layer workflow instead of silently substituting a chart.
- Prefer verified content as a reference when its definition fits. Discover exact field IDs, interval variants, required filters, and filter values; do not derive them from display labels. For governed queries or model edits, read [semantic-layer.md](references/semantic-layer.md).
- For tile/filter changes, chart renames, dashboard QA, or BI migration, read [dashboards-and-migrations.md](references/dashboards-and-migrations.md). Preserve unknown properties and stable IDs in downloaded content.

## Make the measurement reviewable

Before publishing, establish the population, numerator, denominator, grain, dates, timezone, source, freshness, and important exclusions. Put the definition and material limits in the chart description.

- Verify the actual conversion outcome. Page entry, checkout entry, trial start, and paid renewal are different events.
- For windowed conversion or retention, exclude immature cohorts from the comparable rate.
- Do not mix pre-aggregated total rows with their breakdowns. Check the source's real total markers and unique key.
- Calculate a period rate from summed counts, not the mean of daily percentages. Preserve a zero denominator as NULL, not 0%.
- Check duplicate grains, missing dates, freshness, count bounds, and rate arithmetic as relevant. Small counts limit interpretation even when the arithmetic is correct.

## Validate, publish, verify

- Use the chosen path's checks. For CLI files, run `lightdash lint`; execute SQL independently for SQL charts because CLI 2.616.2's `run-chart` rejects them. For MCP, validate the query and follow the server's actual write/preview contract.
- Respect existing authorization. A request to create or upload content authorizes the matching scoped write; do not add a redundant approval step. An inspection request does not authorize publication, access changes, or semantic-layer deployment.
- Upload only the requested chart/dashboard slugs. For an existing space, avoid space creation and access reconciliation. Do not use `deploy` for a content-only edit, bulk `--force` as a retry, or unrelated settings changes.
- If an upload times out, inspect the target slug before retrying. Do not blindly repeat a possibly successful write or bypass a rejected action.
- Read the saved object back through MCP or download it to a separate directory. Compare SQL/query definition, visualization, and space with the reviewed draft. Find the actual filenames; SQL charts can end in `.sql.yml`. Use the returned persisted slug and URL rather than assuming a requested slug survived unchanged.
- When browser access is available and authorized, open the saved object and check rendering and results. If it is unavailable, state that visual verification is incomplete.
- Return the verified object link, what it measures, what changed, the checks performed, and any material limitation. Report a partial result honestly if execution or saving failed.

Use [the synthetic example](assets/conversion-rate.sql.yml) only as an authoring aid. Replace its data and space with verified inputs; installing or testing this skill is not permission to publish the example.

The [ecosystem review](references/ecosystem-research.md) records inspected implementations, licenses, adopted ideas, and rejected assumptions. It is background for choosing integrations, not a requirement to install them.
