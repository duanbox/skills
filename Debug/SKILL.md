---
name: Debug
description: Diagnose concrete EndGods failures from live evidence and fix only when requested. Use when handling crashes, exceptions, Console errors, broken behavior, missing references, or failing tests.
---

# Debug

Build the feedback loop before changing the implementation. A diagnosis request is read-only; a request to fix or implement authorizes only the smallest root-cause change needed for the proven defect.

## Authority and live state

1. Read AGENTS.md and use Docs/INDEX.md to locate the subsystem rules.
2. Read Docs/rules/unitymcp.md before Unity MCP work and the relevant testing or subsystem authority.
3. Start from live Unity state when available:
   - get_console_logs without unnecessary stack traces
   - get_scene_info
   - get_gameobject for the relevant object, including inactive hierarchy checks where needed
4. Use LastUnityError.txt only when live MCP evidence is unavailable or insufficient. Treat it as stale until corroborated.
5. Inspect recent diffs and two or three working examples before proposing a pattern change.

## Root-cause loop

1. Reproduce or define the smallest reliable pass/fail signal.
2. Separate proven facts from assumptions.
3. Trace the bad value, state, reference, resource path, or lifecycle transition back to its source.
4. State one hypothesis and why the evidence supports it.
5. Test one variable at a time with the smallest safe probe.
6. If the hypothesis fails, return to evidence collection instead of stacking fixes.
7. After three failed fix attempts, stop and question the architecture or missing authority with the user.

Do not use delays, retries, polling, swallowed exceptions, defensive defaults, or broad null checks to hide an unknown cause.

## Fix gate

When a fix is explicitly requested:

- Patch the narrowest source of truth.
- Preserve unrelated working-tree changes.
- Reuse existing services, registries, Scene or Prefab ownership, and project tooling.
- Use GameLogger for durable diagnostics and remove temporary instrumentation before handoff.
- Do not introduce new dependencies or broad cleanup without approval.
- Do not use dotnet build as Unity compilation authority.

## Validation

For modified runtime C#:

1. Run Tools/venv/Scripts/python.exe Tools/lint/check_project_rules.py.
2. Trigger Unity MCP recompile_scripts.
3. Read get_console_logs and resolve non-expected errors or warnings.
4. Run the smallest relevant EditMode or PlayMode test when behavior or serialized/runtime contracts changed.
5. Capture the affected screen or state when UI is involved.

For data, assets, scenes, prefabs, or Naninovel changes, run the matching importer, reference, screenshot, or scenario validation required by AGENTS.md.

Report each result as Verified, Should work, or Skipped / blocked, with the exact evidence. Never claim the defect is fixed solely because code changed.
