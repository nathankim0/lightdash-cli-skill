---
name: lightdash-cli
description: Create, inspect, and edit Lightdash charts and dashboards through the CLI. Use for content discovery, metric definition checks, SQL chart authoring, selected uploads, and verification on Cloud or self-hosted instances.
license: MIT
---

# Lightdash CLI

Complete the requested Lightdash content change with the smallest scoped upload. Use the user's own instance, project, warehouse, and permissions; this skill contains no default organization or credentials.

## Start with the working connection

- Check `lightdash --version` and `lightdash config get-project`. If setup is missing or versions conflict, read [setup.md](references/setup.md).
- Prefer the requested CLI when it already provides the needed catalog, SQL, and content operations. Do not make an unrelated connector's authentication repair a prerequisite.
- Check the relevant command's installed `--help` once. Treat [field-notes.md](references/field-notes.md) as versioned observations, not permanent product guarantees.
- Keep downloads, query results, and drafts in a task scratch directory outside unrelated source repositories. Do not commit private content to this public skill repository.

## Inspect before choosing or editing

Read [content-workflow.md](references/content-workflow.md) for concrete commands and chart formats.

- For a specific edit, download the referenced chart or dashboard. Include its dependencies only when the change reaches them.
- To find a missing metric, inventory the current project's accessible regular charts, SQL charts, and dashboards once. Compare names **and definitions**; report the inspected scope rather than claiming absence across an organization.
- Filter warehouse catalog discovery by known database/schema/table before printing it. Read source descriptions and relevant values before deciding what a metric means.
- Reuse a deployed metric or an existing chart pattern when it fits. A saved SQL chart is not a reusable semantic-layer metric: if the user requests the latter, change the relevant model and use the semantic-layer workflow instead of silently substituting a chart.

## Make the measurement reviewable

Before publishing, establish the population, numerator, denominator, grain, dates, timezone, source, freshness, and important exclusions. Put the definition and material limits in the chart description.

- Verify the actual conversion outcome. Page entry, checkout entry, trial start, and paid renewal are different events.
- For windowed conversion or retention, exclude immature cohorts from the comparable rate.
- Do not mix pre-aggregated total rows with their breakdowns. Check the source's real total markers and unique key.
- Calculate a period rate from summed counts, not the mean of daily percentages. Preserve a zero denominator as NULL, not 0%.
- Check duplicate grains, missing dates, freshness, count bounds, and rate arithmetic as relevant. Small counts limit interpretation even when the arithmetic is correct.

## Validate, publish, verify

- Run `lightdash lint` as a structural check. Execute the exact SQL independently for SQL charts; CLI 2.616.2's `run-chart` rejects them.
- Respect existing authorization. A request to create or upload content authorizes the matching scoped write; do not add a redundant approval step. An inspection request does not authorize publication, access changes, or semantic-layer deployment.
- Upload only the requested chart/dashboard slugs. For an existing space, avoid space creation and access reconciliation. Do not use `deploy` for a content-only edit, bulk `--force` as a retry, or unrelated settings changes.
- If an upload times out, inspect the target slug before retrying. Do not blindly repeat a possibly successful write or bypass a rejected action.
- Download the saved object to a separate directory and compare its SQL, definition, visualization, and space with the reviewed draft. Find the actual returned filenames; SQL charts can end in `.sql.yml`.
- When browser access is available and authorized, open the saved object and check rendering and results. If it is unavailable, state that visual verification is incomplete.
- Return the verified object link, what it measures, what changed, the checks performed, and any material limitation. Report a partial result honestly if execution or saving failed.

Use [the synthetic example](assets/conversion-rate.sql.yml) only as an authoring aid. Replace its data and space with verified inputs; installing or testing this skill is not permission to publish the example.
