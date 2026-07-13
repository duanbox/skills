---
name: Verify
description: Verify EndGods changes with fresh, risk-based evidence. Use when checking lint, Unity compilation and Console, tests, importers, screenshots, acceptance, or completion claims.
---

# Verify

Evidence precedes every PASS claim. Verification may run the checks authorized by the request, but it does not edit implementation files or silently fix failures.

## Select the gate

1. Read AGENTS.md and Docs/INDEX.md.
2. Inspect the actual diff or stated scope and apply the current verification lane and escalation triggers from AGENTS.md:
   - Documentation only
   - Low-risk mechanical code change
   - Runtime behavior or serialized contract
   - Visual-only UI Scene or Prefab change
   - Runtime UI interaction, binding, entry, service, or resource change
   - Data or importer change
   - Asset, audio, video, or story change
3. Use `FAST` for work that meets the current AGENTS.md FAST contract. Escalate only when a concrete risk named by AGENTS.md enters scope; do not add tests, matrices, parity reports, recompilation, or harness receipts merely because this skill is active.
4. Read only the authorities needed by the selected verifier. Read Docs/rules/rules_testing.md and the acceptance matrix when tests or test lifecycle are involved; read Docs/rules/unitymcp.md when Unity MCP is required.
5. Determine the smallest sufficient verifier before running commands.

## Core gates

Use the applicable gates, not a generic build ritual:

- Project rules: Tools/venv/Scripts/python.exe Tools/lint/check_project_rules.py
- Documentation-only work: run only the applicable text, encoding, link, index, or focused rule checks; do not invoke Unity solely for documentation.
- FAST visual-only adjustment to an existing Scene or Prefab: require the scoped serialized write, narrow diff, real screenshot of each affected state, direct comparison with the existing visual authority, rule scan, Console check, and the read-only Scene safety audit when the persistent EndGods.unity Scene changed. Do not require a generated parity report, strict-final, registry regeneration, multi-resolution capture, Unity recompile, or Test Runner unless an AGENTS.md escalation trigger entered scope.
- Runtime C#: Unity MCP recompile_scripts, then get_console_logs
- Behavior or non-FAST serialized contracts: smallest relevant EditMode or PlayMode test
- PlayMode tests: detached mode as required by Docs/rules/unitymcp.md
- Runtime UI: focused PlayMode or flow evidence plus required screenshot
- Data: Unified Data Importer, headers, generated assets, dependent load paths, and Console
- Story: Naninovel import, identifiers, variables, commands, focused flow when runtime behavior changed
- Low-risk mechanical changes: use the replacement checks required by AGENTS.md and report the permitted test skip explicitly; recompile only when the changed file type or risk requires it

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
