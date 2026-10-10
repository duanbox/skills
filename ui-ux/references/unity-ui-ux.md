# Unity UI and UX Reference Ideas

Sources reviewed on 2026-10-11. These are optional references, not upstream originals of the project-owned `ui-ux` skill or additional execution requirements. Choose, adapt, combine, or skip ideas according to the player's task, approved visual direction, and available evidence. There is no requirement to read every source or run its workflow.

## Unity Official uGUI

- [Official Unity plugin](https://github.com/Unity-Technologies/unity-agent-plugin)
- [ui-ugui skill](https://github.com/Unity-Technologies/unity-agent-plugin/blob/main/skills/ui-ugui/SKILL.md)
- [UI framework router](https://github.com/Unity-Technologies/unity-agent-plugin/blob/main/skills/ui/SKILL.md)

The preferred technical reference for Canvas/uGUI work. The installed plugin provides the maintained skill; external links offer a way to inspect its current source when useful.

Useful ideas for layout diagnosis include identifying whether a parent LayoutGroup, child RectTransform, or ContentSizeFitter controls a dimension, and considering anchors and pivots before offsets. For existing screens, inspecting the relevant hierarchy and correcting a specific property can preserve working references better than rebuilding the UI.

Its general examples need context. EndGods painterly geometry and authored pools may call for different choices than its sample layouts or list population. A ScrollView need not always use the sample Mask + Image hierarchy: [RectMask2D](https://docs.unity3d.com/Packages/com.unity.ugui@2.0/manual/script-RectMask2D.html) is another documented clipping option. The [ScrollRect manual](https://docs.unity3d.com/Packages/com.unity.ugui@2.0/manual/script-ScrollRect.html) explains the actual component contract.

## Community uGUI Authoring

- [unity-ugui-authoring](https://github.com/german-krasnikov/unity-biome-mcp/blob/master/unity-plugin/ClientSkills/skills/unity-ugui-authoring/SKILL.md)

Useful when an interaction question remains after the UI looks correct. Its approach distinguishes visual acceptance from serialized component references and event listeners: a screenshot can show layout, while checking the actual listener or raycast configuration can explain why a button does not respond. Reading back changed RectTransform values can also expose a layout controller overwriting them.

The skill's named commands belong to its own MCP environment. The transferable idea is choosing evidence that answers the current question, not adopting that server, its command sequence, or its validation tools.

## Community UI UX Pro Max

- [Repository](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill)
- [Skill source](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill/blob/main/.claude/skills/ui-ux-pro-max/SKILL.md)

Useful for design questions about information priority, readable text and contrast, interaction feedback, focus, or motion. A player-task perspective can help evaluate whether someone can find the needed information, understand an unavailable action, complete it, and return to the previous context.

The inspected skill primarily targets web and mobile stacks rather than Unity. Its design guidance can inform a game screen, but its CSS, ARIA, framework code, pixel defaults, generated design-system files, and page workflows are not Unity implementation requirements. Artistic choices remain open to the approved direction and the model's judgment.

## Applying a Reference

These sources are supporting material, not a cumulative checklist. Existing EndGods guidance need not be restated as new rules. An external idea is useful when it resolves a concrete uncertainty without narrowing unrelated choices or expanding the task; a direct solution may need none of these references.
