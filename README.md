# Lightdash CLI Skill

A small, open-source agent skill for creating and editing Lightdash charts and dashboards with the CLI. Works with Lightdash Cloud and self-hosted instances; no company-specific configuration is bundled.

It covers a practical workflow: discover existing content, verify metric definitions and SQL, upload only the intended objects, and check what was actually saved.

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

Korean prompts work too:

```text
$lightdash-cli 기존 차트와 겹치지 않는 핵심 지표 하나를 골라,
계산을 검증하고 기존 공간에 새 차트로 만들어줘.
```

## What's included

- [Skill instructions](skills/lightdash-cli/SKILL.md): scoped discovery, metric checks, and publishing boundaries.
- [Setup](skills/lightdash-cli/references/setup.md): version matching and authentication.
- [Content workflow](skills/lightdash-cli/references/content-workflow.md): commands for inspection, SQL checks, and selected uploads.
- [Field notes](skills/lightdash-cli/references/field-notes.md): observed pitfalls and faster alternatives.
- [Synthetic SQL chart](skills/lightdash-cli/assets/conversion-rate.sql.yml): an editable line-chart example with safe division.

The skill itself has no runtime dependencies. Its workflows use the Lightdash CLI, and browser access when visual verification is available. YAML tools are optional conveniences, not a reason to install a new dependency for a single edit.

## Compatibility and limits

The content workflow and SQL chart configuration were exercised with Lightdash CLI/server **2.616.2**. CLI flags and chart schemas change: check your installed version's help and prefer a freshly downloaded chart from your instance over this example when they disagree.

The example SQL uses standard SQL constructs, but it has only been executed against BigQuery. Adapt and verify SQL for your warehouse. A successful lint or upload does not prove that a chart's query or visualization works.

This is an independent workflow skill, not an official Lightdash project. Lightdash also ships a broader [official skill](https://github.com/lightdash/lightdash/tree/main/skills/developing-in-lightdash), installable with `lightdash install-skills`; use it for detailed semantic-layer and advanced feature documentation. This repository adds a compact workflow informed by operational mistakes rather than copying that reference library.

## Contributing

Keep changes small and reproducible. Include the CLI/server version, a synthetic reproduction, and the command or chart behavior you checked. Never attach credentials, private instance URLs, customer data, or downloaded organization content. Check changed YAML with `lightdash lint --path FILE`; execute changed SQL separately against an authorized test connection. Do not upload the synthetic example to a production project merely to test this repository.

Licensed under [MIT](LICENSE).
