# Field notes: fewer round trips

These are observations from CLI/server **2.616.2**, not promises about every release. Verify local help before applying a version-specific workaround.

| Detour or failure | Faster, correct approach |
| --- | --- |
| Preparing a Homebrew installation before reading a self-hosted server's version | Inspect both versions first. Use an official pinned binary when Homebrew's current formula differs. |
| Considering a global Node upgrade just to install the CLI | Use a self-contained official binary, or an already suitable runtime. Keep unrelated application toolchains intact. |
| Repairing another warehouse connector after Lightdash SQL already worked | Use the working project connection for metadata and SQL. Repair the other connector only if the task needs it. |
| Dumping an entire warehouse catalog or large dashboard YAML into context | Save the snapshot locally, filter metadata to candidate schemas/tables, and inspect only decision-relevant fields. |
| Guessing that a column named `converted_users` means paid conversion | Read the source description first; the outcome may be trial start or another event. Name the metric after the verified outcome. |
| Treating a rounded stored rate as an exact fraction | Recompute from counts; compare stored rates at their documented precision. |
| Running `lightdash sql` without an output file | Supply `--output RESULTS.csv` every time. In this version it was required despite the help's option rendering. |
| Using `run-chart` for a SQL runner chart | Execute its exact SQL through `lightdash sql`; `run-chart` supports metric-query charts in this version. |
| Importing a YAML library that was not installed | Use available tooling or inspect a known scalar directly. Do not create a dependency just for one extraction. |
| Guessing a read-back path ending in `.yml` | List returned files; SQL charts were exported with `.sql.yml`. |
| Investigating individual visualization types before looking for bundled references | Check `lightdash install-skills --help`, then use the official versioned reference or a freshly downloaded chart. |
| Treating lint/upload success as the final check | Execute the SQL, inspect the saved object, and verify the rendered chart when possible. |

The official skill already covers a much broader feature set. This skill keeps the common content loop short: **inspect → define → query → selected upload → read back**. It does not need a new CLI wrapper, credentials store, browser-first inventory, or a separate application.

## Primary references

- [CLI installation](https://docs.lightdash.com/workflow/cli/install)
- [CLI authentication](https://docs.lightdash.com/workflow/cli/authenticate)
- [CLI commands](https://docs.lightdash.com/workflow/cli/reference)
- [Content as code and SQL chart format](https://docs.lightdash.com/workflow/content-as-code)
- [Official Lightdash skill](https://github.com/lightdash/lightdash/tree/main/skills/developing-in-lightdash)
- [Versioned visualization types](https://github.com/lightdash/lightdash/blob/2.616.2/packages/common/src/visualizations/types/index.ts)
- [Versioned official skill installer](https://github.com/lightdash/lightdash/blob/2.616.2/packages/cli/src/handlers/installSkills.ts)
