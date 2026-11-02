# EndGods UI art direction and agreed references

The user agreed to these sources and this workflow in the 2026-10-23 chat “选择 UI Toolkit 试点界面” (`01a15a21-967e-7150-a836-5b45ddc94259`), then explicitly asked to put it into the workflow after migration. This reference preserves that agreement across chats; it is not approval to copy a site's visual identity or replace accepted game art.

## Fixed reference sources

| Source | What to inspect | EndGods use |
| --- | --- | --- |
| [Beautiful UI](https://www.beautifului.dev/) | Information cards, task/status presentation, filters, search, list hierarchy | Quest journal, intelligence board, gallery details; borrow information organization from its app-oriented examples |
| [beUI](https://beui.dev/) | Tabs, buttons, dialogs, tooltips, toasts, card feedback | Gallery tabs, artifact type filters, confirmations, reward feedback; the agreed starting example is [motion tabs](https://beui.dev/components/motion/tabs) |
| [Rare UI](https://www.rareui.com/) | Distinctive animated components and energy/fluid effects | Small spirit-power, realm, or artifact-energy accents when legibility and Unity cost justify them |
| [Transitions.dev](https://transitions.dev/) | Tab indicators, expansion, page transitions, number changes, entrance/exit rhythm | Coherent navigation and feedback; the agreed example is [sliding tabs](https://transitions.dev/studio.html?lib=tabs-sliding) |
| [shadcn/ui](https://ui.shadcn.com/docs) | Component composition, full interaction states, keyboard/focus behavior | Shared buttons, tabs, inputs, dialogs and tooltips, translated to the existing Unity architecture |
| [Dribbble game UI](https://dribbble.com/tags/game-ui) | Composition, material treatment, typography hierarchy, panel/card details | Game art direction; also search [fantasy game UI](https://dribbble.com/tags/fantasy-game-ui) and [game inventory](https://dribbble.com/tags/game-inventory) |

The second source is **beui.dev**, not `belui.dev`. Verify current concrete pages when using them; a remembered URL is not proof that a component is available or was viewed. If a source cannot be inspected, state that limit and continue with relevant accessible sources without silently deleting the agreed source from the list.

Concrete starting pages checked on 2026-11-03: Beautiful UI [Task Rows](https://www.beautifului.dev/#task-rows) and [Filter Table](https://www.beautifului.dev/#filter-table); Rare UI [Task list](https://www.rareui.com/components/tasklist) and [Animated counter](https://www.rareui.com/components/animatedcounter); Transitions [Tooltip open/close](https://transitions.dev/studio.html?lib=tooltip-open-close); shadcn [Item](https://ui.shadcn.com/docs/components/base/item) and [Tooltip](https://ui.shadcn.com/docs/components/base/tooltip). The check covered page text, component documentation and example code, not live animation playback. Reinspect only the relevant sample when using its motion as a target.

Actual released-game screenshots/videos and [Interface In Game](https://interfaceingame.com/) complement these sources for game-specific density, structure, and flows. Dribbble also contains marketing pages and presentation-only concepts: distinguish those from usable game screens. Supplemental sources do not replace the user's fixed list.

## From references to a reviewable game result

1. Inspect the current affected screen and existing shared components first. Start from its real content, accepted art, and the player's immediate decision.
2. For a new page or important redesign, choose **2–3 relevant examples**, not every website. Open the actual component/image; use a live demo when motion is the borrowing. Give its link, the specific detail to borrow, and a short Unity implementation-cost judgment. Do not claim to have seen interaction from a static screenshot or search snippet.
3. Translate the references into one coherent EndGods direction. Use current project fonts, colors, textures, icons and surface rules. Preserve the distinction between world atmosphere, readable content, and active controls. Web references supply organization, state grammar and motion; their SaaS cards, generic sans-serif, glass blur, or neon decoration are not the game's default skin.
4. For a genuinely new visual direction, produce a reviewable concept through the project's concept/art route. Validate a material motion idea with a bounded runnable sample. An already approved direction enters a small real UI trial directly; do not ask for approval again or require new concepts for routine spacing/state refinements.
5. Compare the affected region in the real GameView with the chosen target, using actual Chinese text and realistic density. Review the relevant default, selected/focused, unavailable, empty, long-text or scrolling state; choose only states that protect this change. Compilation proves neither visual quality nor natural gameplay behavior.
6. Reuse a successful component across appropriate consumers under the project's shared-component ownership rules. Keep the accepted detail and source link in the existing screen brief or component authority; do not create another universal design document or expand a one-screen trial automatically.

Check the actual license before copying code or assets; inspiration does not require importing web dependencies. React, Tailwind and Motion code cannot run directly in Unity UI Toolkit. Translate authored structure to UXML, visual rules to USS, and state/input/lifecycle to C# within existing owners. Check the installed Unity version and official API documentation for the exact mechanism; do not substitute a browser screenshot for the final UI.

## Art-producer focus for the current game

Use these as screen-specific judgment prompts, not a mandatory checklist for every edit:

| Family | Details that make the framework feel finished |
| --- | --- |
| Quest | Separate task selection, tracking and completion; make the next objective scan faster than narrative prose; align progress/status/reward groups; explain automatic settlement or unavailable actions accurately from the existing service; keep paper/ink texture clear of dense text |
| Character | Establish portrait/name/build hierarchy; align labels and comparable numbers; group related attributes; keep equipped, available, locked and empty slots distinct; avoid letting repeated locked grids overpower the character or the current action |
| Combat | Make acting unit, target, cost and availability unambiguous; show preview versus committed result distinctly where those states already exist; keep status stacks and durations readable; make damage, recovery and turn changes legible without competing motion |
| Shared controls | Keep hit area, spacing, baseline, hover, press, selection, keyboard focus and disabled behavior coherent; use more than hue alone for important distinctions; preserve the project's existing navigation and reduced-motion behavior where supported |

Do not invent new gameplay or data just to make a mock look complete. Read the owning system before changing a displayed claim. Resolve typography through `Docs/Knowledge/fonts.md`, art language through current `DESIGN.md` / `rules_ui_guide.md` and approved screen evidence, and implementation through `ui-ux`. Do not copy stale token values or snapshot completion claims into this skill.

The original concrete precedent is the complaint “法宝界面下面的类型标签太丑”: retain the four top gallery tabs and page content, inspect beUI's segmented tabs and Transitions' sliding indicator, then refine the artifact type filters' spacing, hit targets, selected/hover/disabled states and restrained movement using EndGods fonts/materials. This is a transferable method, not a command to rework that screen in every task.
