---
name: Verify
description: Verify EndGods changes with fresh, risk-based evidence. Use when checking lint, Unity compilation and Console, tests, importers, screenshots, acceptance, or completion claims.
---

# Verify

Evidence precedes every PASS claim. Verification may run the checks authorized by the request, but it does not edit implementation files or silently fix failures.

## Select the gate

1. Read AGENTS.md and Docs/INDEX.md.
2. Read Docs/rules/rules_testing.md, Docs/rules/rules_test_acceptance_matrix.md, and Docs/rules/unitymcp.md.
3. Inspect the actual diff or stated scope and classify it:
   - Documentation only
   - Low-risk mechanical code change
   - Runtime behavior or serialized contract
   - Visual-only UI Scene or Prefab change
   - Runtime UI interaction, binding, entry, service, or resource change
   - Data or importer change
   - Asset, audio, video, or story change
4. Read the relevant subsystem authority and determine the smallest sufficient verifier before running commands.

## Core gates

Use the applicable gates, not a generic build ritual:

- Project rules: Tools/venv/Scripts/python.exe Tools/lint/check_project_rules.py
- Runtime C#: Unity MCP recompile_scripts, then get_console_logs
- Behavior or serialized contracts: smallest relevant EditMode or PlayMode test
- PlayMode tests: detached mode as required by Docs/rules/unitymcp.md
- Visual-only UI: ownership gate, Scene or Prefab write evidence, real Scene or GameView screenshot, parity check, rule scan, recompile, and Console; skip Test Runner with the documented reason
- Runtime UI: focused PlayMode or flow evidence plus required screenshot
- Data: Unified Data Importer, headers, generated assets, dependent load paths, and Console
- Story: Naninovel import, identifiers, variables, commands, focused flow when runtime behavior changed
- Low-risk mechanical changes: rule scan, recompile, and Console; report the permitted test skip explicitly

Unity Test Runner is single-owner. If a job is queued or running, poll it to terminal state before starting another.

## Truth rules

- Do not use dotnet build as Unity compilation authority or as a substitute for Unity Console.
- Do not infer full success from a partial test, lint pass, prompt generation, previous run, or another agent's claim.
- Read complete outputs, exit codes, failure counts, screenshots, and Console state.
- Re-run required evidence in the current verification pass.
- A test must protect intent and should fail when the protected rule is broken.
- Do not mark missing tools or screenshots as PASS.

## Failure handling

If verification fails, report the first actionable failure and its evidence. A request to verify remains read-only. If the user explicitly requested a fix, follow Debug for the smallest root-cause change and then rerun this verification contract.

## Output

For every required gate, report:

- Command or editor action
- Result: PASS, FAIL, BLOCKED, or SKIPPED
- Exact evidence
- Scope protected
- Skip reason and residual risk

Conclude with Verified, Should work, or Skipped / blocked. Claim completion only when every required gate has fresh passing evidence.
