---
name: SmartFix
description: Route ambiguous, cross-domain EndGods repair requests to the owning workflow. Use when the user explicitly asks for SmartFix or no single problem owner is clear.
---

# Smart Fix Router

SmartFix is a thin compatibility router, not an implementation workflow. Triage read-only by default and avoid duplicating the procedures owned by specialist skills.

## Triage

1. Read AGENTS.md and Docs/INDEX.md.
2. Capture the request, current evidence, affected systems, risk, and requested action: explain, diagnose, verify, or fix.
3. Identify one primary owner:
   - Concrete Unity error, crash, failing behavior, scene or resource defect: Debug
   - Measured FPS, frame time, GC, memory, loading, or rendering problem: PerformanceOpt
   - Whole-repository or module maintainability and refactoring audit: TechDebt
   - Compilation, Console, tests, screenshots, importers, or acceptance evidence: Verify
   - Planning or system design without an implementation defect: Architect
   - Bounded change review or broad deep review: CodeReview or FullReview
   - Narrative scene or dialogue authoring: StoryWeaver
4. Add a secondary workflow only when it owns a distinct downstream gate.
5. Read the owning subsystem authority before recommending action.

Prioritize data loss, security, crashes, and build blockers before functional defects, performance, or debt. Do not treat this priority as permission to broaden scope.

## Action boundary

- For explain, diagnose, review, or verify requests, remain non-mutating.
- If the user explicitly requests a fix, follow the selected specialist workflow and its validation contract.
- Do not launch several competing implementation paths or combine unrelated fixes.
- If evidence cannot identify an owner, return a bounded evidence-gathering step instead of guessing.
- Preserve dirty-worktree changes and stop when required authority or user choice is missing.

## Output

Return:

- Problem class and requested action
- Proven evidence and assumptions
- Primary owner and optional secondary gate
- Authority documents to read
- Smallest next action
- Required completion evidence
- Blockers or residual risk

A successful route is the outcome of SmartFix. Validation and implementation remain owned by the selected specialist skill.
