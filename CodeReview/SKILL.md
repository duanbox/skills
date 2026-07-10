---
name: CodeReview
description: Review bounded EndGods changes without editing them. Use when the user asks for a code, diff, PR, change-assessment, or merge-readiness review.
---

# Code Review

Review is read-only by default. Do not edit code, update documentation, stage changes, or auto-fix findings unless the user explicitly requests a separate implementation pass.

## Establish scope

1. Read AGENTS.md and route through Docs/INDEX.md.
2. Identify the requested files, feature, card, commit range, or working-tree diff.
3. Preserve unrelated dirty changes and do not switch revisions in the active checkout.
4. Read the relevant subsystem authority and two or three nearby usages or call sites.
5. For UI, assets, TMP, combat, testing, and Unity MCP changes, read the special authority named by AGENTS.md.

## Review order

1. Requirements: confirm the change implements the actual request and does not broaden scope.
2. Correctness: trace data, state, initialization, errors, async flow, disposal, and edge cases.
3. Architecture: check logic/view boundaries, ServiceLocator composition boundaries, namespaces, ownership, reuse, and dependency direction.
4. Project hard rules: check UniTask, GameLogger, TMP_Text, UIDataAsset colors, editor isolation, resource loading, and importer rules as applicable.
5. Serialized and data contracts: inspect Scene, Prefab, CSV, ScriptableObject, resource, and Naninovel references when touched.
6. Tests and evidence: determine whether existing tests protect intent, whether required screenshots or importer evidence exist, and whether the reported verification matches the change risk.
7. Maintainability: flag duplication or complexity only when it creates a concrete defect, regression risk, or material maintenance cost.

## Finding standard

Report only actionable findings. Every finding must include:

- Priority: P0 critical, P1 high, P2 medium, or P3 low
- Exact file and tight line range
- The broken requirement or invariant
- Concrete failure scenario and impact
- Evidence from code, data, or validation state
- Smallest appropriate correction

Do not inflate severity, report formatting handled by tooling, or invent hypothetical problems without a reachable failure path.

## Output

Return findings first, ordered by priority. Then include:

- Reviewed scope and authority documents
- Validation evidence inspected
- Missing or stale evidence
- Residual risks and coverage limits
- Verdict: ready, ready with non-blocking notes, not ready, or inconclusive

If no findings exist, say so directly and still report evidence gaps. Do not modify Docs/rules/rules_code_structure.md merely because a review occurred.
