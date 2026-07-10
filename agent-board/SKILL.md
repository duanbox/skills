---
name: agent-board
description: Manage EndGods Agent Board task cards and subagent prompts. Use when working with Docs/agent_board, Tools/agent_board/board.py, CARD-* task cards, Ready or QA state transitions, role skill configuration, skillshare-distributed subagent skills, or generating Codex/subagent prompts from board cards.
---

# Agent Board

Use this skill to operate the EndGods Markdown-backed Agent Board. The board is project-local; this skill is only the distributed entry point that tells agents where the real workflow and CLI live.

## Source of Truth

- Project root: `D:\Project\EndGods`
- Board guide: `D:\Project\EndGods\Docs\agent_board\README.md`
- Skill registry: `D:\Project\EndGods\Docs\agent_board\skill_registry.md`
- CLI: `D:\Project\EndGods\Tools\agent_board\board.py`
- Cards: `D:\Project\EndGods\Docs\agent_board\cards\`

## Workflow

1. Read `AGENTS.md` and `Docs/INDEX.md` before editing or executing project work.
2. Read `Docs/agent_board/README.md` before changing cards, roles, registry, or CLI behavior.
3. Use `Tools/venv/Scripts/python.exe Tools/agent_board/board.py` for board commands. Do not use bare `python`.
4. Keep cards under `Docs/agent_board/cards/`; do not put board cards under `Docs/todo/`.
5. Treat Backlog cards as ideas only. Only Ready and QA cards are executable.
6. Generate subagent prompts with `board.py prompt`; the prompt must include role defaults, card-requested skills, and effective skills.
7. When a generated prompt lists effective skills, the receiving agent must read each named skill's `SKILL.md` before task actions. If a skill is unavailable, report it and continue only with the safest card-approved fallback.
8. Use fresh local Codex threads by default. Create a Codex worktree only when the user explicitly asks for worktree isolation for that card.

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
- Do not broaden a card's allowed files. If the card is too narrow, return BLOCKED or update the card explicitly before execution.
- QA cards are read-only unless the card explicitly authorizes a fix. QA should report evidence, missing evidence, regressions, and verdict.
- For QA or rerun cards, `board.py prompt` only validates prompt shape. Do not report PASS until every command listed in the card's `## Verifier` succeeds against the current repo state.
- If worktree isolation is requested, use one worktree / branch per card and let the main thread decide merge, rejection, or card state changes after handoff.
- Skill instructions do not override `AGENTS.md`, `Docs/INDEX.md`, subsystem authority docs, or card boundaries.
- After editing this skill source, run `skillshare sync --all --json`, then verify with `skillshare status --json` and `skillshare diff --json`.
