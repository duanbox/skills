---
name: TechDebt
description: Audit EndGods technical debt read-only and produce file-cited priorities. Use when assessing code health, architecture decay, maintainability, refactoring backlog, or module quality.
---

# Technical Debt Audit

Audit first and separate remediation from implementation. Do not edit code, documentation, cards, or configuration unless the user explicitly authorizes a later bounded cleanup task.

## Orient before judging

1. Read AGENTS.md and Docs/INDEX.md.
2. Define the requested repository, subsystem, module, time horizon, and exclusions.
3. Read the applicable architecture, structure, testing, systemspec, and Knowledge authorities.
4. Map entry points, ownership boundaries, dependencies, high-churn files, and large files.
5. Inspect two or three accepted patterns before labeling a different pattern as debt.
6. Run existing project checks before inventing new scanners.

On this Windows project, use Get-ChildItem, Select-String, and Get-Content rather than rg or grep. Use Tools/venv/Scripts/python.exe for project Python commands and do not install tools or dependencies without approval.

## Evidence sources

Prefer:

- Git history and current working-tree state
- Tools/venv/Scripts/python.exe Tools/lint/check_project_rules.py
- Existing architecture and acceptance tests
- Unity Console, importer, profiler, and screenshot evidence when relevant
- Concrete file and line references
- Repeated defects, churn, validation gaps, and ownership violations

A messy-looking file is not debt by itself. Establish actual maintenance cost, regression risk, rule conflict, or blocked delivery.

## Audit dimensions

Assess only material findings across:

- Architecture and dependency boundaries
- Project hard-rule and consistency drift
- Type, state, async, lifecycle, and error-handling debt
- Test quality, missing intent protection, stale diagnostics, and acceptance-matrix debt
- Scene, Prefab, CSV, ScriptableObject, resource, and Naninovel contract drift
- Dependency, tooling, importer, and build-pipeline debt
- Performance and resource hygiene supported by measurements
- Documentation and INDEX drift
- UI, art, audio, or asset-pipeline debt when in scope

## Finding standard

Every finding must include:

- ID and priority
- Exact file and line or serialized asset location
- Evidence and affected invariant
- User or development impact
- Confidence
- Relative effort: small, medium, or large
- Smallest remediation and required verifier

Do not fabricate hours, ROI, coverage percentages, or performance gains. Include a Things that look risky but are intentional section and open questions to expose possible false positives. Do not pad empty categories.

## Remediation boundary

Rank the highest-value items, but do not auto-fix them. For accepted debt, create a separate closed-loop task with allowed files, authority, verifier, evidence, and done condition. Avoid repository-wide cleanups, speculative rewrites, new abstractions, or new dependencies without explicit approval.

## Output

Return scope and mental model, evidence sources, prioritized findings, top remediation queue, intentional exceptions, open questions, missing evidence, and recommended bounded follow-up tasks.
