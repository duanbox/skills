---
name: agent-board
description: Explicitly manage durable EndGods Agent Board cards. Use only when the user invokes $agent-board or asks for a card for cross-day or cross-thread persistence, Unity-exclusive resource coordination, or independent QA records; never for ordinary same-session subagents.
---

# Agent Board

Use this skill to operate the EndGods Markdown-backed Agent Board after its explicit activation gate passes. The board is project-local; this skill is only the distributed entry point that tells agents where the durable workflow and CLI live.

## Activation Gate

- Invoke only after the user explicitly writes `$agent-board`, `/agent-board`, or directly asks to create, use, or update an Agent Board card.
- The requested work must need at least one durable board function: cross-day or cross-thread persistence, coordination of Unity-exclusive resources such as Scene/Prefab writing, Unified Data Importer, or Test Runner ownership, or an independent QA evidence record.
- Use native subagents and the current task plan for ordinary same-session decomposition. Do not create cards, move Ready/QA states, or load role defaults merely because subagents are available or a task has several steps.
- If explicit activation is missing or none of the three durable functions applies, continue without Agent Board.

## Source of Truth

- Project root: `D:\Project\EndGods`
- Board guide: `D:\Project\EndGods\Docs\agent_board\README.md`
- Skill registry: `D:\Project\EndGods\Docs\agent_board\skill_registry.md`
- CLI: `D:\Project\EndGods\Tools\agent_board\board.py`
- Cards: `D:\Project\EndGods\Docs\agent_board\cards\`

## Workflow

1. Confirm the activation gate, then read `AGENTS.md` and `Docs/INDEX.md` before editing or executing project work.
2. Read `Docs/agent_board/README.md` before changing cards, roles, registry, or CLI behavior.
3. Use `Tools/venv/Scripts/python.exe Tools/agent_board/board.py` for board commands. Do not use bare `python`.
4. Keep cards under `Docs/agent_board/cards/`; do not put board cards under `Docs/todo/`.
5. Treat Backlog cards as ideas only. Only Ready and QA cards are executable.
6. Generate subagent prompts with `board.py prompt`; the prompt must include role defaults, card-requested skills, and effective skills.
7. When a generated prompt lists effective skills, the receiving agent must read each named skill's `SKILL.md` before task actions. If a skill is unavailable, report it and continue only with the safest card-approved fallback.
8. For an explicitly activated card, use a fresh local Codex task when durable thread separation helps. Create a Codex worktree only when the user explicitly asks for worktree isolation for that card.

## Core Commands

```powershell
Tools/venv/Scripts/python.exe Tools/agent_board/board.py init
Tools/venv/Scripts/python.exe Tools/agent_board/board.py list --state ready
Tools/venv/Scripts/python.exe Tools/agent_board/board.py show CARD-0001
Tools/venv/Scripts/python.exe Tools/agent_board/board.py validate CARD-0001 --target-state ready
Tools/venv/Scripts/python.exe Tools/agent_board/board.py prompt CARD-0001 --agent qa --out Temp/agent_board/CARD-0001-qa-prompt.md
Tools/venv/Scripts/python.exe Tools/agent_board/board.py claim CARD-0001 --owner codex-main
Tools/venv/Scripts/python.exe Tools/agent_board/board.py move CARD-0001 --state qa
Tools/venv/Scripts/python.exe Tools/agent_board/board.py release CARD-0001
```

## Guardrails

- The CLI manages Markdown cards only. It does not run Codex, Unity, imports, tests, or shell commands for a card.
- Agent Board is not the default subagent scheduler, backlog, or planning ceremony. Native same-session subagents remain the default.
- Do not broaden a card's allowed files. If the card is too narrow, return BLOCKED or update the card explicitly before execution.
- QA cards are read-only unless the card explicitly authorizes a fix. QA should report evidence, missing evidence, regressions, and verdict.
- For QA or rerun cards, `board.py prompt` only validates prompt shape. Do not report PASS until every command listed in the card's `## Verifier` succeeds against the current repo state.
- If worktree isolation is requested, use one worktree / branch per card and let the main thread decide merge, rejection, or card state changes after handoff.
- Skill instructions do not override `AGENTS.md`, `Docs/INDEX.md`, subsystem authority docs, or card boundaries.
- After editing this skill source, run `skillshare sync --all --json`, then verify with `skillshare status --json` and `skillshare diff --json`.
