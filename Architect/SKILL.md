---
name: Architect
description: Create an implementation-ready EndGods plan without implementing it. Use when the user explicitly requests a planning-only plan, decomposition, estimate, or execution handoff; not as a gate for implementation work.
---

# Architect

Create a closed-loop implementation contract for an explicitly planning-only request. Inspect the repository and produce the requested plan without editing files or running mutating tools. When the user has already authorized implementation in the same request, do not require a second confirmation or block execution; provide only the minimum useful planning context and continue through the owning implementation workflow instead of treating Architect as a gate.

## Authority and discovery

1. Read AGENTS.md, then use Docs/INDEX.md to locate the relevant subsystem authority.
2. Read only the rules, systemspec, Knowledge, pipeline, and acceptance documents required by the task.
3. For TMP, combat, art/assets, UI, testing, or Unity MCP work, honor the special required reads in AGENTS.md.
4. Inspect two or three similar implementations before proposing a new pattern.
5. Surface conflicts between code, serialized data, and documentation. Do not invent a blended contract.

## Planning workflow

1. State the objective and observable user outcome.
2. Record assumptions and ask only questions whose answers materially change the result.
3. Define allowed files, forbidden files, affected systems, and whether Scene, Prefab, CSV, asset, or story files are involved.
4. Map dependencies and implementation order. Prefer vertical, independently verifiable slices.
5. Apply the minimal-correct ladder: reuse platform behavior, installed dependencies, shared project primitives, and existing services before proposing new code.
6. Identify docs that must change only when implemented behavior changes. Do not create a new Docs file unless the AGENTS.md nearest-doc test is satisfied.
7. Define verification before implementation, including exact commands or editor actions and required evidence.
8. If the change exceeds five files or crosses systems, add an explicit scope checkpoint before execution.

## Required plan output

- Objective and non-goals
- Authority documents
- Assumptions and unresolved conflicts
- Allowed and forbidden files
- Existing patterns to reuse
- Ordered implementation slices with exact file paths
- Data, service, Scene, Prefab, Naninovel, and importer boundaries where relevant
- Verification matrix with expected pass signals
- Required screenshots, Console state, importer output, tests, or other evidence
- Done condition and residual risks
- Safe execution handoff

## Self-review gate

Before returning the plan:

- Re-read the request and map every requirement to a plan item.
- Remove placeholders, speculative systems, filler work, and unsupported file paths.
- Confirm each step has a verifier and each verifier protects the intended behavior.
- Confirm the plan preserves dirty-worktree changes and repository ownership boundaries.
- Label unavailable evidence or tooling as blocked instead of implying completion.
