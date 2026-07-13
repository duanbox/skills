---
name: architecture
description: Apply EndGods architecture rules. Use when changing a C# module or dependency boundary, service or initialization ownership, data source of truth, or cross-system contract; not for work contained within an existing boundary.
---

# Architecture & Code Structure

Use this skill as a router and execution guardrail. Do not treat distributed skill text as a second copy of the repository architecture.

## Authority Order

1. Read `AGENTS.md` and `Docs/INDEX.md`.
2. Read only the routed authority documents for the task:
   - code ownership, namespaces, dependencies: `Docs/rules/rules_code_structure.md`
   - folders and asset placement: `Docs/rules/rules_structure.md`
   - approved libraries and coding rules: `Docs/rules/rules_tech_stack.md`
   - resource-loading decisions: `Docs/rules/rules_resources.md`
   - UI architecture: `Docs/rules/rules_ui.md` and `Docs/Pipeline/ui-vibe-coding-pipeline.md`
   - testing and acceptance: `Docs/rules/rules_testing.md` and `Docs/rules/rules_test_acceptance_matrix.md`
   - domain behavior: the matching file under `Docs/systemspec/`
3. Inspect two or three current implementations in the affected module before proposing a pattern.
4. When docs, code, Scene/Prefab data, or serialized data disagree, surface the conflict and follow the most specific authority. Do not average them into a new architecture.

## Workflow

1. Classify the material boundary change: module ownership, dependency direction, service registration or resolution, initialization owner, data source of truth, or cross-system contract.
2. For complex, loop-worthy, high-risk, delegated, or cross-system work, define the closed-loop contract: objective, allowed files, authority documents, checker(s), required evidence, and done condition. For bounded changes, use the current AGENTS.md FAST or risk lane and a concise scope; do not create extra contract artifacts merely because this skill is active.
3. Walk the minimal-correct ladder: reuse the platform, installed dependency, existing service/interface, or current project pattern before adding code.
4. Keep dependencies explicit. Resolve `ServiceLocator` only at registration, bootstrap, `*Dependencies.cs`, initialization, or test-composition boundaries; cache or inject dependencies for runtime business methods.
5. Keep gameplay state and rules in C# system layers. Keep UI, animation, Naninovel, and VFX as presentation or orchestration layers.
6. Preserve Scene/Prefab and serialized-data authority where the subsystem requires it. Do not add runtime normalization to conceal broken authoring data.
7. If the change crosses systems or exceeds five files, restate scope before editing.

## Root-Cause Guardrail

For bugs, establish the observable failure before changing architecture. Capture the actual state transition, trace the real execution path, fix the smallest proven cause, and remove temporary diagnostics. Delays, cache/restore layers, polling, or defensive fallbacks are not fixes unless the evidence proves they are part of the contract.

## Verification

- Always run `Tools/venv/Scripts/python.exe Tools/lint/check_project_rules.py`.
- For runtime C# changes, recompile through Unity MCP and read Console warnings/errors.
- Run the smallest relevant EditMode or PlayMode test when behavior, serialization, services, async timing, resources, or scene flow changes.
- For low-risk mechanical changes, report `Skipped / low-risk mechanical change` and name the replacement checks.
- Report each result as `Verified`, `Should work`, or `Skipped / blocked`; do not call incomplete evidence done.

## Output

State the selected authority, architecture decision, touched boundary, verification evidence, and any unresolved conflict. Do not create a new architecture document when an existing authority document can absorb the decision.
