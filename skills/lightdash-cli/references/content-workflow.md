# Content workflow

Examples below use shell variables chosen for the current task. Keep the inspected snapshot, upload draft, and read-back separate. Check installed help before using flags on a different CLI version.

## 1. Discover only what the task needs

For an edit, select the actual chart or dashboard reference:

```bash
lightdash download --project "$LD_PROJECT" --charts "$CHART_REF" \
  --skip-spaces --path "$SNAPSHOT_DIR"
# Or select a dashboard and its charts:
lightdash download --project "$LD_PROJECT" --dashboards "$DASHBOARD_REF" \
  --skip-spaces --path "$SNAPSHOT_DIR"
```

For a missing-metric search, download the accessible ordinary project content once:

```bash
lightdash download --project "$LD_PROJECT" --skip-spaces --path "$SNAPSHOT_DIR"
```

A default download includes regular charts, SQL charts, and dashboards; it is not a complete organization snapshot. Optional resource types need their own flags when relevant. Do not download permissions, apps, agents, or all optional content just to find a regular metric.

List real files and inspect top-level names, source tables, dimensions, metrics, SQL, and descriptions. Avoid dumping whole dashboards or an unfiltered warehouse catalog into the conversation.

```bash
lightdash warehouse-catalog --database "$DATABASE" --schema "$SCHEMA" --json
lightdash warehouse-catalog --database "$DATABASE" --schema "$SCHEMA" \
  --table "$TABLE" --include-fields --json
```

Catalog types alone may not establish business meaning. Inspect warehouse-native descriptions or model SQL when necessary. For example, on **BigQuery only**, `DATASET.INFORMATION_SCHEMA.COLUMN_FIELD_PATHS` exposes column descriptions, and `TABLE_OPTIONS` exposes table descriptions. Query these through the same working `lightdash sql` connection rather than requiring separate warehouse credentials.

## 2. Check the source and exact SQL

Resolve the intended business outcome before writing the chart title. Verify relevant enum values, source grain, latest data date, nulls, duplicates, and any cohort completion flag. Use bounded metadata queries and a small aggregate profile first; a result-row limit does not limit warehouse scan cost.

Save the reviewed query as a local `.sql` file when that makes iteration easier:

```bash
lightdash sql "$(cat "$QUERY_FILE")" --output "$RESULT_CSV" --limit 100
```

This shell example reads SQL as an argument; do not use `eval` or interpolate credentials. Read-only inspection does not authorize DDL/DML. Use the correct SQL dialect, partition filters, and cost controls available for the warehouse and CLI version.

Check meaningful invariants against actual results. For an exclusive conversion funnel, verify `0 <= converted_users <= eligible_users`, unique cohort grains, completion of the observation window, and the expected treatment of a zero denominator. For a period KPI, divide summed counts. Recompute from counts if stored rates are rounded; rounding differences alone are not evidence of bad source data.

## 3. Author the correct chart type

Prefer a downloaded example from the same instance. Use a YAML parser already available in the environment when editing structured content. Do not write a home-grown YAML parser or install packages merely to inspect a few known fields.

| Kind | Content-as-code shape | Query check |
| --- | --- | --- |
| Semantic/explore chart | `contentType: chart`, `tableName`, `metricQuery`, `chartConfig` | `lightdash run-chart --path FILE --output RESULTS.csv` |
| SQL runner chart | `contentType: sql_chart`, `sql`, `limit`, `chartKind`, `config` | `lightdash sql` with the exact embedded SQL |

The second shape was verified with CLI 2.616.2. See [the synthetic SQL chart](../assets/conversion-rate.sql.yml) for a line chart. SQL chart visualization settings do **not** use the explore chart's `chartConfig` layout. Keep percentage values as fractions when using `format: percent`.

Keep the checked `.sql` file and the embedded YAML SQL identical; after editing either, execute the final embedded query again. If no YAML loader is available, copy that exact scalar into the CLI argument and compare it carefully, or use an existing environment with a YAML loader. Do not treat a stale query result as validation of a later edit.

```bash
lightdash lint --path "$CHART_FILE"
```

Lint checks structure, not data correctness or rendering. CLI 2.616.2 rejects SQL charts in `run-chart`; use the direct SQL path rather than retrying the same unsupported command.

## 4. Publish only the intended objects

Confirm the effective instance and project. For a requested new chart in an **existing** space, place only the reviewed file in the upload draft and select its unique slug:

```bash
lightdash config get-project
lightdash --non-interactive upload --project "$LD_PROJECT" \
  --path "$DRAFT_DIR" --charts "$CHART_SLUG" \
  --skip-spaces --skip-space-create
```

For a dashboard edit, select `--dashboards "$DASHBOARD_SLUG"`. Include chart updates only if the requested change also changes those charts. Re-download shared objects immediately before editing when concurrent changes are possible; review the final diff instead of forcing an old snapshot over newer edits.

`--skip-spaces` skips space definitions/access reconciliation; it does not prove the chart's `spaceSlug` is correct. Preserve or verify the intended existing space. Creating or moving a space, changing access, and deploying models are separate actions, not automatic parts of a chart edit.

Do not use broad `upload --force` or `deploy` to troubleshoot content authoring. If a write's result is uncertain, inspect the selected object before retrying. Optional upload validation is useful where supported, but does not replace executing SQL charts.

## 5. Read back and verify

```bash
lightdash download --project "$LD_PROJECT" --charts "$CHART_SLUG" \
  --skip-spaces --path "$READBACK_DIR"
```

Discover the returned files: SQL charts can be named `charts/SLUG.sql.yml`. Compare the saved SQL, name, description, chart kind/config, and space to the reviewed draft. Ignore serialization-only differences such as key order or download timestamps.

Open the actual saved object through a returned URL or the project's visible content listing. Confirm the correct title, chart type, axis formatting, query results, and absence of an error state. Do not guess a route from an explore chart: SQL charts have a different route. If visual access is unavailable, report the read-back and execution checks separately from the missing visual check.

Return a direct link and the scope actually verified. Keep downloaded content local unless the user has explicitly requested version control for that content.
