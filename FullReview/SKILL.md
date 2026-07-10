---
name: FullReview
description: Run a deep, read-only EndGods review across implementation and delivery evidence. Use when the user explicitly asks for a full, deep, or comprehensive review.
---

# Full Review

Use this as the explicit deep-review mode above CodeReview, not as a second daily-review workflow. The review is read-only unless the user later requests a separate fix task.

## Scope contract

1. Read AGENTS.md and Docs/INDEX.md.
2. Define the feature, subsystem, card, commit range, or file set being reviewed.
3. List applicable authority documents and required evidence.
4. State coverage limits before reviewing a very large scope.
5. Preserve the working tree; do not edit, stage, switch revisions, or generate persistent reports by default.

## Review dimensions

Evaluate only dimensions relevant to the scope:

1. Requirements and acceptance criteria
2. Correctness, edge cases, failure handling, and async behavior
3. Architecture, ownership, dependency direction, and shared primitive reuse
4. Security, trust boundaries, data loss, and destructive behavior
5. Performance risks supported by reachable hot paths or profiler evidence
6. Tests that protect intent, not merely execution
7. Unity Scene, Prefab, ScriptableObject, CSV, resource, and Naninovel contracts
8. UI visual authority, runtime binding, accessibility, and screenshot evidence
9. Documentation and INDEX synchronization required by implemented behavior
10. Delivery evidence: lint, Unity recompile, Console, focused tests, import results, and screenshots

Independent read-only dimensions may be delegated in parallel when the client supports subagents. The main reviewer must verify and deduplicate their findings rather than trusting summaries.

## Evidence rules

- Use the CodeReview finding standard: P0 through P3, exact file and line, broken invariant, failure scenario, evidence, and smallest correction.
- Distinguish an implementation defect from missing proof.
- Do not infer TDD history, test coverage, security, or performance from filenames or claims.
- Do not treat a successful build, prompt generation, or lint pass as proof of unrelated behavior.
- Report conflicts instead of averaging code and documentation.

## Consolidated output

- Review scope and authority documents
- Coverage by dimension
- Findings ordered by priority
- Missing or stale evidence
- Confirmed compliant contracts that materially reduce risk
- Residual risks and unreviewed areas
- Verdict: ready, ready with non-blocking notes, not ready, or inconclusive
- Recommended next bounded task

Do not fix findings, update architecture docs, or move Agent Board cards during the review unless separately authorized.
