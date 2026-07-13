---
name: skillshare
description: Manage cross-client skills and agents with Skillshare. Use when the user explicitly mentions Skillshare or requests install, sync, uninstall, collect, audit, or target/extras diagnosis. Do not use for generic skill design or ordinary subagent work.
---

# Skillshare

Treat the configured Skillshare source as authority and target directories as distributed outputs.

## Establish the mode

1. Detect project mode from `.skillshare/config.yaml`; otherwise use global mode. Use `-p` or `-g` when ambiguity matters.
2. Inspect configuration, source Git state, target modes, include/exclude filters, and extras mappings before mutation.
3. Prefer structured, non-interactive commands such as `--json`, `--dry-run`, `--force`, `--yes`, or explicit selectors when the command supports them.
4. Run `skillshare <command> --help` before an unfamiliar operation instead of guessing flags or relying on copied recipes.

For EndGods, the shared source is `C:\Users\duanb\AppData\Roaming\skillshare\skills`. Edit that source, never `.codex/skills`, `.claude/skills`, or another target copy. Project rules and workflows are separate extras under the configured extras source.

## Read-only diagnosis

Use the smallest useful set:

```powershell
skillshare status --json -g
skillshare doctor --json -g
skillshare diff --json -g
skillshare analyze --json --verbose -g
skillshare extras list --json -g
```

Interpret target mode before treating differences as damage. In EndGods, Grok uses merge mode and intentionally retains Grok-local skills; local-only Grok entries are not candidates for collection into the shared source.

## Mutations

1. Check the source repository status and preserve unrelated work.
2. Modify or install into the authoritative source. Use `skillshare uninstall ... --json -g` for safe removal; it moves skills to trash. Never delete distributed links manually.
3. Use `collect` only when the target-local change is intentional and newer than source authority.
4. After any mutation, run `skillshare sync --all --json -g` so skills and extras converge.
5. Verify with `status`, `doctor`, and `diff`; explain expected merge-mode or local-only results.
6. Commit and push the source repository only when the user authorizes it or the active project contract explicitly requires an accepted central-source change.

## Safety boundaries

- Do not run an interactive TUI from an agent workflow.
- Do not collect tool-exclusive or machine-local skills into a shared repository without explicit approval.
- Do not overwrite a newer project-owned extras file merely to make status green; reconcile source and target first.
- Do not bypass audit findings silently. Report the finding and require explicit approval for a supported override.
- Keep central-skill commits separate from project docs, extras, and tool-local configuration.
