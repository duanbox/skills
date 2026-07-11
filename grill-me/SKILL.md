---
name: grill-me
description: Pressure-test EndGods plans and designs before implementation. Use automatically for vague, high-risk, design-heavy, cross-system, authority-conflicting, or acceptance-ambiguous work; use deep mode when the user invokes $grill-me or asks to grill, interrogate, challenge, or pressure-test a plan. Do not use for clear low-risk mechanical work or questions with no material decision.
---

# Grill Me

Resolve material decisions before implementation while keeping ordinary EndGods work low-friction. This skill is a decision gate, not an implementation workflow.

## Select the mode

- Use `DEEP` when the user explicitly invokes `$grill-me` or `/grill-me`, says `grill me`, or asks to interrogate, challenge, or pressure-test a plan or design.
- Use `MICRO` when the skill triggers automatically because the request is vague, high-risk, design-heavy, cross-system, authority-conflicting, or unclear about acceptance.
- In `DEEP`, do not implement until the user explicitly confirms shared understanding.
- In `MICRO`, ask only questions whose answers materially change the result. Record non-blocking uncertainty as visible assumptions and continue once the gate passes.

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

## Run the gate

Before the first question, emit exactly one visible opening signal:

```text
Grill gate: active — <MICRO|DEEP>; <N> material decision(s) unresolved.
```

If no material decision remains, emit:

```text
Grill gate: passed — no material decision remains; assumptions: <none or concise list>.
```

Then continue with the appropriate owning skill when the user already requested implementation.

For each unresolved decision:

1. Ask one question at a time and wait for the answer.
2. Lead with a concrete recommended answer and why it best fits current EndGods authority.
3. Give alternatives only when they are genuinely viable, with the consequence of each choice.
4. State what files, systems, evidence, or scope the answer changes.
5. Do not disguise a discoverable fact as a user decision.

Use this compact question form:

```text
Grill <N> — Recommendation: <answer and reason>
Decision: <single question>
Impact: <what changes based on the answer>
```

## Complete the mode

For `MICRO`:

- Stop asking once no blocking material decision remains.
- State remaining assumptions explicitly.
- Form or update the task's closed-loop contract, then continue the authorized workflow.

For `DEEP`:

- Walk every material branch, including failure behavior, boundaries, acceptance, and non-goals.
- End with a concise decision record and closed-loop contract: objective, non-goals, decisions, assumptions, allowed and forbidden files, authority documents, checkers, required evidence, and done condition.
- Ask the user to confirm shared understanding. Do not implement before that confirmation.

## Documentation boundary

- Do not create `CONTEXT.md`, a parallel ADR tree, or a new project authority layer.
- Do not edit project files while a material decision remains unresolved.
- After approval, route durable terminology or behavior updates to the nearest existing document found through `Docs/INDEX.md`; apply the repository's nearest-doc and authorization rules.
