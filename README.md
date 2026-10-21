# Lightdash CLI & MCP Skill

A portable, open-source agent skill for inspecting governed metrics and creating or editing Lightdash charts and dashboards through the CLI or an available MCP server. Works with Lightdash Cloud and self-hosted instances; no company-specific configuration is bundled.

It covers a practical workflow: discover existing definitions, validate the query, make a scoped change, and check what was actually saved. The invocation remains `$lightdash-cli` for compatibility.

Independent discovery and verification run in parallel where supported. The skill reuses task evidence and stops expanding the investigation once the requested change is grounded; dependent validation and publishing steps remain ordered.

## Install

With the [Skills CLI](https://github.com/vercel-labs/skills) (requires Node.js 22.20+):

```bash
npx skills add nathankim0/lightdash-cli-skill --skill lightdash-cli
```

Select your agent, such as Codex or Claude Code. Add `-g` for a global installation.

Alternatively, clone this repository and copy `skills/lightdash-cli` into your agent's skills directory. For Codex:

```bash
git clone https://github.com/nathankim0/lightdash-cli-skill.git
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R lightdash-cli-skill/skills/lightdash-cli "${CODEX_HOME:-$HOME/.codex}/skills/"
```

If that skill already exists, update its checkout or replace the installed copy deliberately; do not merge unrelated versions. Restart your agent session if it does not discover the new skill.

## Connect your Lightdash instance

Install the [Lightdash CLI](https://docs.lightdash.com/workflow/cli/install), preferably matching your server version. Authenticate and select your own project:

```bash
LD_URL=https://lightdash.example.com
LD_PROJECT=example-project
lightdash login "$LD_URL" --project "$LD_PROJECT"
lightdash config get-project
```

Use your normal browser login. The skill does not store credentials or grant permissions; your Lightdash account must already have the required access.

Already have MCP connected? Use it without installing the CLI just for this skill. The [MCP guide](skills/lightdash-cli/references/mcp-workflow.md) explains official vs community servers, project scope, query polling, content editing, and capability limits. The official data MCP has deployment/plan requirements; the public Docs MCP supplies documentation, not your organization's data. No MCP server is installed automatically.

## Use it

```text
Use $lightdash-cli to find an important metric missing from this project,
validate its definition, and create one chart in an existing space.
```

```text
Use $lightdash-cli to update this dashboard's date filters without changing
its other charts or space permissions.
```

```text
Use $lightdash-cli to investigate this chart. Inspect it and its SQL only;
do not publish changes.
```

```text
Use $lightdash-cli with my connected MCP server to compare these two charts.
Reconcile definitions and filters before comparing values.
```

```text
Use $lightdash-cli to add a reusable semantic metric to this project.
Update the owning model and validate its downstream charts in a preview.
```

Korean prompts work too:

```text
$lightdash-cli 기존 차트와 겹치지 않는 핵심 지표 하나를 골라,
계산을 검증하고 기존 공간에 새 차트로 만들어줘.
```

## What's included

- [Skill instructions](skills/lightdash-cli/SKILL.md): scoped discovery, metric checks, and publishing boundaries.
- [Setup](skills/lightdash-cli/references/setup.md): version matching and authentication.
- [Content workflow](skills/lightdash-cli/references/content-workflow.md): commands for inspection, SQL checks, and selected uploads.
- [MCP workflow](skills/lightdash-cli/references/mcp-workflow.md): capability discovery, scoped queries, polling, and server-specific writes.
- [Semantic layer](skills/lightdash-cli/references/semantic-layer.md): existing definitions, field discovery, and reusable model metrics.
- [Dashboards and migrations](skills/lightdash-cli/references/dashboards-and-migrations.md): filter/tile preservation, affected-query QA, and parity checks.
- [Ecosystem review](skills/lightdash-cli/references/ecosystem-research.md): 11 upstream repositories, pinned sources, licenses, adopted patterns, and rejected assumptions.
- [Field notes](skills/lightdash-cli/references/field-notes.md): observed pitfalls and faster alternatives.
- [Synthetic SQL chart](skills/lightdash-cli/assets/conversion-rate.sql.yml): an editable line-chart example with safe division.

The skill itself has no runtime dependencies. Use the Lightdash CLI or a suitable connected MCP server, plus browser access when visual verification is available. YAML tools are optional conveniences. Installing all reviewed integrations is unnecessary.

## Compatibility and limits

The CLI content workflow and SQL chart configuration were exercised with Lightdash CLI/server **2.616.2**. The **2026-10-21 ecosystem review** inspected source and documentation; it is not a claim that every MCP server was live-tested. CLI flags, tool contracts, and chart schemas change: check live help/schemas and prefer a freshly read object from your instance over this example when they disagree.

The example SQL uses standard SQL constructs, but it has only been executed against BigQuery. Adapt and verify SQL for your warehouse. A successful lint or upload does not prove that a chart's query or visualization works.

This is an independent workflow skill, not an official Lightdash project. Lightdash also ships a broader [official skill](https://github.com/lightdash/lightdash/tree/main/skills/developing-in-lightdash), installable with `lightdash install-skills` or available through supported MCP skill resources. Use it for detailed schemas and advanced feature documentation. This repository combines practical workflow lessons with attributed ecosystem research; it does not vendor other implementations or their reference libraries.

## Contributing

Keep changes small and reproducible. Include the CLI/server version, a synthetic reproduction, and the command or chart behavior you checked. Never attach credentials, private instance URLs, customer data, or downloaded organization content. Check changed YAML with `lightdash lint --path FILE`; execute changed SQL separately against an authorized test connection. Do not upload the synthetic example to a production project merely to test this repository.

Licensed under [MIT](LICENSE).
