# Setup and version matching

Use this only when the CLI, login, or selected project needs attention. A working installation does not need to be reconfigured for every task.

```bash
command -v lightdash
lightdash --version
lightdash config get-project
```

Find the server version in the Lightdash UI. The [installation guide](https://docs.lightdash.com/workflow/cli/install) recommends matching the CLI to the server. For an already working pair, avoid upgrading just to complete a content task.

## Install only what is missing

- On supported systems, the official Homebrew installation is convenient, but its latest formula may differ from a self-hosted server.
- For a pinned version, use the matching official [release binary](https://github.com/lightdash/lightdash/releases), verify its published checksum before executing it, and place it on the user's PATH. Check that the executable is found in a new shell.
- Alternatively, install `@lightdash/cli@VERSION` through npm after checking that version's Node engine requirement. Do not change an application's pinned Node version merely to install an unrelated CLI.

```bash
# Set VERSION to the verified server version first.
npm view "@lightdash/cli@$VERSION" engines --json
```

Self-contained binaries avoid the npm Node requirement. Use the vendor's available platform assets rather than guessing an unsupported architecture or release filename.

## Authenticate and select the project

```bash
LD_URL=https://lightdash.example.com
LD_PROJECT=example-project
lightdash login "$LD_URL" --project "$LD_PROJECT"
lightdash config get-project
```

Replace the example values with the user's requested destination. Check environment overrides such as `LIGHTDASH_PROJECT` if the effective project is unexpected. Do not print credentials while diagnosing configuration.

Follow [official authentication guidance](https://docs.lightdash.com/workflow/cli/authenticate) and the installed `lightdash login --help` when OAuth or token login differs by version. Never put a real token in a committed command, example, issue, or chat reply.

## Obtain detailed references efficiently

Lightdash includes `lightdash install-skills`. Inspect its help and install the official reference skill into a scratch location when you need detailed schema, model, or advanced feature documentation:

```bash
lightdash install-skills --help
lightdash install-skills --agent codex --path "$REFERENCE_DIR"
```

This is optional: `$REFERENCE_DIR` must be a chosen scratch directory. The official installer can overwrite previously installed official skill files. Do not install or replace unrelated global skills without a reason.

In CLI 2.616.2, the installer's `--source` lookup uses the **CLI version as a Git ref**. A custom repository without that matching ref will fail. Install this repository through the Skills CLI or copy its skill directory instead.
