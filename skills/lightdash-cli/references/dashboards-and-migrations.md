# Dashboard edits, QA, and migration

Read for tile/filter changes, renames, broken dashboards, or BI migration. Use the chosen [CLI](content-workflow.md) or [MCP](mcp-workflow.md) publishing path; this guide adds reference and measurement checks.

## Preserve the graph, not just the picture

- Read the complete current dashboard and changed chart before editing. Preserve unknown fields, schema versions, filter IDs, tile IDs, tabs, and unaffected properties. A partial list or preview is not a complete replacement body.
- Resolve each chart slug, explore, and field target. A label is not an identifier. A title change usually keeps the slug; an intentional slug rename also updates dashboard references using the installed version's supported workflow.
- Dashboard tile titles may override a chart's name. Update those overrides only when the requested rename should appear there too.
- Map dashboard filters to the actual tile explores and required date fields. Explicitly exclude or correctly map incompatible tiles; changing a filter's label does not retarget it. Check defaults, completed periods, timezone, and parameter values.
- Preserve the instance's grid and tab structure. For 36-column dashboards, ensure `x + w <= 36`, positive sizes, and non-overlapping tiles on the same tab. Check the active schema rather than imposing that grid on a different format.
- New charts must exist before tiles referencing them are saved. Upload unchanged dependencies only when necessary. Do not automatically create a dashboard for a request that asks for one chart.

The [official content reference](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/skills/developing-in-lightdash/resources/content-as-code-reference.md) and [DJ's edit guidance](https://github.com/Workday/dj/blob/ffe0d66384eea86b5e76f68e0de9582c18569e8d/templates/skills/dj-edit-lightdash-yaml/_SKILL.md) document these identity and dependency concerns. Keep framework-only commands and approval rules confined to that framework.

## Verify the affected behavior

Execute changed tiles with the dashboard's real filters and overrides, not only each saved chart's standalone defaults. Community servers may provide tile-specific or bulk execution; inspect their schemas and run a small affected subset with bounded concurrency. Preserve individual failures instead of treating a partially successful batch as a pass. See the [Python MCP tool inventory](https://github.com/poddubnyoleg/lightdash_mcp/blob/a0e98202b50fcdf3ce5656f94c671d8d72eedd13/README.md).

Read back the saved dashboard. Check cross-filters, hidden/visible columns, pivots, date grain, axis units, and totals only where the change could affect them. Render and inspect affected tabs/tiles when possible. If a verification badge changes on edit, report that result; do not re-verify the content without a request and permission.

## Migrate with a parity record

Use a machine-readable source when available: Tableau `.twb`/`.twbx`, exported definitions, or the original queries and filter specifications. A screenshot can establish visual layout; it cannot establish hidden formulas, joins, or filter logic. With screenshots only, make a scoped visual reconstruction and identify the measurement inputs still missing instead of claiming exact parity.

Build an explicit mapping of source worksheets/metrics/calculations/filters to target models, fields, charts, and tiles. Record unsupported features rather than silently dropping them. Compare source and target over identical dates, population, timezone, and filters; check totals plus representative rows, not just chart appearance. Define any numeric tolerance before judging parity. Include source/target identifiers, differences, and untested items in the handoff.

For full Tableau parsing and formula translation, the [specialized migration skill](https://github.com/Endiruslan/tableau-to-lightdash-skill/blob/29ecb4ef012c2cff074fa94e6078e1596ae1ffcc/SKILL.md) provides deeper references. Read it when the task requires it rather than installing or copying its entire schema library for every dashboard edit. Validate its CLI advice against the installed version; a blanket requirement for `--force` is not portable.
