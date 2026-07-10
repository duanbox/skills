---
name: game-playtest
description: Design or analyze evidence-based human playtests for EndGods gameplay, narrative, onboarding, time actions, navigation, dungeons, combat, UI, or the D1-D10 demo. Use for protocols, observations, feedback analysis, or experience hypotheses; not for machine verification, simulated-player claims, debugging, or automatic tuning.
---

# Game Playtest

Use `PLAN`, `ANALYZE`, or `DELTA` mode. Do not edit the game, tune values, or convert feedback directly into backlog work.

## Evidence boundary

- Human playtest evidence requires a real participant. Agent navigation, automated tests, screenshots, and developer self-play must be labeled accurately and never presented as independent human proof.
- Record observation separately from interpretation and recommendation.
- Do not collect personal data, recordings, or telemetry beyond the participant's consent and the authorized study scope.

## Authority router

1. Read `AGENTS.md` and `Docs/INDEX.md`, then the authority for the tested flow.
2. For the demo, include `Docs/Todo/demo/demo_content_systems_plan.md`, `Docs/systemspec/time_system.md`, affected systemspecs, and narrative rules.
3. Read `Docs/rules/rules_testing.md` and the relevant acceptance matrices to identify machine-protected contracts. Route machine failures to `Verify` or `Debug` instead of relabeling them as playtest findings.
4. Use the tested build, commit, data, and starting save as evidence; do not assume the current editor state matches the participant's build.

## PLAN mode

1. Define one falsifiable player-experience hypothesis.
2. Record participant profile, first-time or returning status, sample source, build or commit, platform, input method, starting state, route, stop condition, and known contaminating issues.
3. Select only observations that can test the hypothesis: time to orient, decisions, pauses, backtracking, misclicks, help requests, exits, retries, choice comprehension, or time-cost misunderstanding.
4. Write neutral tasks and non-leading follow-up questions. Do not teach the intended solution while testing comprehension.
5. Define the verdict rule before the session and state what one session cannot establish.

## ANALYZE mode

1. Normalize notes into timestamped observations and retain direct evidence references.
2. Record every moderator intervention because it changes the result.
3. Classify findings as bug, comprehension, navigation, interaction, narrative, design, content, balance, performance, or unknown.
4. Evaluate frequency, severity, representativeness, competing explanations, and confidence without inventing statistical significance.
5. Return `SUPPORTED`, `MIXED`, `NOT_SUPPORTED`, or `INCONCLUSIVE` for the hypothesis.
6. Recommend the smallest next experiment or owning diagnostic task; do not prescribe a broad redesign from one opinion.

## DELTA mode

Compare two explicitly identified builds under equivalent starting conditions. Report changed observations, unchanged problems, new regressions, confounders, and whether the original hypothesis moved. Do not compare memory of an old session with instrumented evidence from a new one as if the data quality were equal.

## Guardrails

- Do not fabricate player quotes, reactions, timings, or sample sizes.
- Do not import generic D1/D7/D30 retention, NPS, session-length, monetization, gacha, or live-operations thresholds.
- Do not infer a system need merely because a player had idle time.
- Do not change balance, content, UI, or story without a separate implementation request.

## Output

- Mode, hypothesis, build identity, participant and environment
- `PLAN`: protocol, observation schema, intervention rules, and predefined verdict rule; findings are not required before sessions occur
- `ANALYZE` or `DELTA`: evidence log and findings with observation, interpretation, severity, confidence, and evidence reference
- Verdict and limitations
- Minimal next experiment or owning handoff

## Method provenance

This workflow adapts focused hypothesis, observation, and report practices from the MIT-licensed [gstack-game playtest skill](https://github.com/fagemx/gstack-game/blob/main/skills/playtest/SKILL.md) and Donchitos' [playtest-report](https://github.com/Donchitos/Claude-Code-Game-Studios/blob/main/.claude/skills/playtest-report/SKILL.md). Retention, monetization, recruitment-budget, and generic studio-path assumptions are excluded.
