# Security and privacy review

Reviewed **2026-10-21** for the **v0.1.1** release. Scope: this repository's release content, reachable Git history, and published v0.1.0 release notes. This is a maintainer review, not an independent security certification or an audit of linked CLI/MCP implementations.

## Results

| Check | Result |
| --- | --- |
| Gitleaks 8.30.1: full reachable history and working tree | No secrets detected. The official scanner binary's release checksum was verified. |
| Private context: instance addresses, project identifiers, local paths, and organization data | No private context found by targeted marker checks and content review. Git authors use a GitHub noreply email. |
| Package contents | Instructions, optional Codex UI metadata, and a synthetic SQL example. No runtime scripts, hooks, dependencies, or bundled credentials. |
| Tool permissions | No `allowed-tools` grants or host permission overrides. Installing or selecting the skill grants no additional account access. |
| Source and attribution | Original MIT material with links to upstream sources. No upstream code or schemas vendored; linked projects retain their own licenses. |
| Agent packaging | Skills CLI 1.7.4 installed all skill resources for Codex, Claude Code, and GitHub Copilot in separate temporary projects; installed file hashes matched the source. |

## Changes made in v0.1.1

- Explicitly treat retrieved content as untrusted data. Embedded commands or requests cannot authorize credential disclosure, new destinations, or wider access.
- Require an explicitly authorized destination before exporting private content to another service or public repository. Preserve host approvals and reject attempts to bypass denied actions.
- Pin the installation example to a reviewed installer version and a tagged skill release; provide manual installation without a package runner.
- Ignore common local credential and scratch paths to reduce accidental commits. Ignore rules do not detect or remove secrets already tracked by Git.

## Recheck before publishing changes

Run these from the repository root with Gitleaks installed:

```bash
gitleaks git --log-opts=--all --redact .
gitleaks dir --redact .
```

Review staged files and diffs for private data and instructions that broaden scope; a secret scanner cannot recognize every organization-specific value. Keep downloaded charts, query results, authentication files, and production reproductions outside the public repository. Use synthetic fixtures in issues and contributions.

## Remaining limits

Skill instructions are not a sandbox or a guarantee against prompt injection. Effective restrictions come from the agent host, Lightdash account, MCP server, and warehouse permissions. Read-only queries can still expose data and incur warehouse cost. Use a trusted connection with access appropriate to the task.

Claude Code and Copilot installation was checked, but interactive Lightdash tasks in those agents were not exercised. MCP implementations were source-reviewed, not security-audited or live-tested here. A clean scan does not prove the absence of all secrets or vulnerabilities. Never publish tokens or private exports in public reports.
