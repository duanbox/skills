---
name: grill-me
description: Deeply pressure-test an EndGods plan. Use when the user explicitly invokes $grill-me or asks to grill, challenge, or pressure-test it; never auto-trigger from risk or ambiguity alone.
---

# Grill Me

Resolve material decisions before implementation when the user explicitly requests a deep challenge. This skill is a manual decision gate, not an implementation workflow or an automatic prerequisite for ordinary EndGods work.

## Explicit invocation only

- Run this workflow only after the user explicitly invokes `$grill-me` or `/grill-me`, says `grill me`, or asks to interrogate, challenge, or pressure-test a plan or design.
- Do not trigger automatically because a task is vague, high-risk, design-heavy, cross-system, authority-conflicting, or unclear about acceptance. Handle ordinary ambiguity through the current `AGENTS.md` rules for discoverable facts, visible assumptions, and blocking questions.
- Do not implement until the user explicitly confirms the resulting shared understanding.

## Establish facts first

1. Read `AGENTS.md`, then route through `Docs/INDEX.md` and the relevant subsystem authority.
2. Inspect the current repository state and resolve discoverable facts yourself. Do not ask the user for information available in code, serialized data, documentation, git state, or live tools.
3. Surface conflicts between authorities instead of averaging them.
4. Preserve dirty-worktree ownership and do not broaden the authorized scope.

## Identify material decisions

Treat a decision as material when it changes one or more of:

- observable player or user outcome;
- scope, non-goals, delivery order, or destructive/external action;
- architecture, ownership, dependency direction, or source of truth;
- Scene, Prefab, CSV, ScriptableObject, Naninovel, asset, service, or save contracts;
- verification evidence, acceptance threshold, or done condition.

Do not grill over facts the repository can answer, choices fixed by authority, wording preferences with no downstream effect, or reversible local details already inside the user's approved scope.

## Run the deep review

Before the first question, emit exactly one visible opening signal:

```text
Grill gate: active - DEEP; <N> material decision(s) unresolved.
```

If no material decision remains, emit:

```text
Grill gate: passed - no material decision remains; assumptions: <none or concise list>.
```

For each unresolved decision:

1. Ask one question at a time and wait for the answer.
2. Lead with a concrete recommended answer and why it best fits current EndGods authority.
3. Give alternatives only when they are genuinely viable, with the consequence of each choice.
4. State what files, systems, evidence, or scope the answer changes.
5. Do not disguise a discoverable fact as a user decision.

Use this compact question form:

```text
Grill <N> - Recommendation: <answer and reason>
Decision: <single question>
Impact: <what changes based on the answer>
```

## Complete the review

- Walk every material branch, including failure behavior, boundaries, acceptance, and non-goals.
- End with a concise decision record and closed-loop contract: objective, non-goals, decisions, assumptions, allowed and forbidden files, authority documents, checkers, required evidence, and done condition.
- Ask the user to confirm shared understanding. Do not implement before that confirmation.

## Documentation boundary

- Do not create `CONTEXT.md`, a parallel ADR tree, or a new project authority layer.
- Do not edit project files while a material decision remains unresolved.
- After approval, route durable terminology or behavior updates to the nearest existing document found through `Docs/INDEX.md`; apply the repository's nearest-doc and authorization rules.
