---
name: PerformanceOpt
description: Measure and optimize confirmed EndGods Unity bottlenecks. Use when investigating FPS, frame-time, hitches, GC, memory, loading, or rendering.
---

# Performance Optimization

Measure first. A request to analyze or profile is read-only; edit code or project settings only when the user explicitly asks to optimize or fix the measured bottleneck.

## Authority and benchmark contract

1. Read AGENTS.md, Docs/INDEX.md, Docs/Knowledge/performance_baseline.md, and the relevant subsystem rules.
2. Read Docs/rules/unitymcp.md and Docs/rules/rules_testing.md before editor validation.
3. Define target platform and hardware, Unity Editor versus player build, scene or save, exact reproduction route, quality settings, warm-up, sample duration, and target budget.
4. Record existing profiler captures, player logs, screenshots, or benchmark numbers. Keep confidence low when evidence is thin.

## Profiling workflow

1. Reproduce the symptom on a fixed route.
2. Capture the cheapest evidence that can separate CPU, GPU, memory and GC, loading or IO, physics or animation, UI rebuild, and platform-specific causes.
3. Name one primary bottleneck family and an optional secondary hypothesis.
4. Trace the hotspot to concrete code, assets, Scene state, or configuration.
5. Inspect nearby working patterns only after the bottleneck is localized.
6. Propose the smallest change likely to improve the measured budget.
7. Re-run the same route with the same settings and compare before and after.

Do not start with a generic repository scan or assume that Update methods, allocations, pooling, ECS, batching, Addressables, or Canvas structure are the cause.

## Implementation gate

When optimization is explicitly requested:

- Change one bottleneck at a time.
- Preserve behavior, ordering, error handling, and serialized references.
- Do not merge unrelated Update loops, add a new manager, introduce ECS, or create custom pooling without evidence and approval.
- Prefer Unity and installed project capabilities before custom infrastructure.
- Document a known ceiling and upgrade path when choosing a deliberate local simplification.
- Do not add dependencies without approval.

## Verification

- Capture before and after metrics on the same route and target.
- Run the project rule scan.
- For runtime C# changes, run Unity MCP recompile_scripts and inspect get_console_logs.
- Run the smallest focused behavior or performance regression check required by the change.
- Verify no new memory growth, visual regression, loading failure, or gameplay change.
- Report editor-only evidence as editor-only; do not generalize it to target hardware.

## Output

Return a performance brief with scope, baseline, evidence, primary hypothesis, proposed or implemented change, before-and-after numbers, confidence, regression evidence, and the next useful capture. Never claim an improvement without measured comparison.
