# Governed metrics and model changes

Use this when answering a business question from existing explores, checking whether a metric exists, or defining a metric reusable across charts. A one-off SQL chart does not add a semantic-layer metric.

## Query existing definitions first

Use connected metadata tools to inspect explores, metrics, dimensions, required filters, and joins. Copy real field IDs, including date interval variants; do not generate IDs from a human-readable label. An agent scope or hidden explore can explain a missing field. Resolve the gap without broadening permissions.

Prefer a matching verified chart as a definition example. Before comparing two charts, reconcile their source, population, metrics, dimensions, filters, completed/current periods, timezone, parameters, pivots, sorts, limits, and table calculations. Identical titles do not establish identical measures. Execute comparable queries only after resolving these differences.

For a new query, select the minimum dimensions needed: extra dimensions change grouping and can change the answer. Check distinct counts and totals at the intended grain. Examine join cardinality before trusting sums across one-to-many relationships. Use existing project conventions for primary keys and join relationships rather than trying to repair fanout with an unexplained DISTINCT.

These practices combine the [official analytics workflow](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/plugins/lightdash/skills/lightdash-analytics/SKILL.md), [metric reference](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/skills/developing-in-lightdash/resources/metrics-reference.md), and [comparison playbook](https://github.com/yu-iskw/lightdash-tools/blob/6f5522523a47a04c30e9823991ee2af5a97d47b9/packages/mcp/src/profiles/content-reader/v1/resources/playbooks/compare.md).

## Change the authoritative source

1. Identify the project type and source of truth. A dbt project, a native Lightdash YAML project, and a generator-managed project are different authoring environments. Read the relevant official versioned reference before editing.
2. For dbt, follow the project's existing metadata placement and installed version: older projects commonly use `meta`, while dbt 1.10+/Fusion use `config.meta`. Native Lightdash models use their own model schema. Do not copy one shape into the other.
3. In generator-managed projects, edit the owning input, not generated YAML. For example, DJ manages model metadata upstream in `.model.json`; use its existing generation and lineage tools only when that framework is present.
4. Add the smallest reusable metric/dimension change with a definition, aggregation, formatting, and required filters. Ratios reference aggregate counts with zero-denominator handling. Keep metric definitions in the model when reuse is requested; chart `additionalMetrics` remain chart-local.
5. Inspect downstream fields, charts, dashboard filters, and rename references. Preserve compatibility or update all affected consumers in the requested scope. Compile and test in the project's preview workflow when available; a model deployment is a separate external change from uploading a chart.
6. Verify query results against a bounded warehouse aggregate or trusted baseline at the same grain. Include zeros, NULLs, duplicate joins, partial periods, and source freshness where relevant. Report unvalidated dependencies instead of claiming deployment is safe from lint alone.

For implementation detail, use the installed server's skill resources or [official model/CLI skill](https://github.com/lightdash/lightdash/blob/848af49f45b9cb0be73d10f7822427982c556087/skills/developing-in-lightdash/SKILL.md). The [DJ edit workflow](https://github.com/Workday/dj/blob/ffe0d66384eea86b5e76f68e0de9582c18569e8d/templates/skills/dj-edit-lightdash-yaml/_SKILL.md) explains its framework-specific source boundary; its project restrictions are not universal Lightdash CLI rules.

## Explain unavailable results precisely

Separate a missing definition, missing tool, insufficient access, invalid query, incomplete result, and a valid empty result. When query execution is unavailable, inspect existing saved content before declaring that no answer exists. Label an estimate or proxy explicitly; do not rename a different outcome to fill a gap.
