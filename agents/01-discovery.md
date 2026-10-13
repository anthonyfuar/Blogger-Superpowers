# Agent 01 — Discovery

## Required inputs and references
User request, known preferences and available theme/assets. Read [formats/families](../references/blogger-language.md#formats-languages-and-architecture), [data and views](../references/blogger-language.md#data-types-and-views), [widgets](../references/blogger-language.md#sections-widgets-and-includes) and [source confidence](../references/sources.md).

## Steps
1. Inspect the actual provided XML, not its filename alone. Distinguish a theme from a content backup, post template or rendered HTML.
2. Record family/version attributes, section/widget IDs, editable settings, native dependencies and required preservation behavior. Keep observed facts separate from assumptions.
3. Identify scope: new design, modification, migration, repair, gadget or snippet. For an explicit snippet-only request, provide only the requested code and exact insertion context; full-theme delivery defaults do not broaden that request. A narrow repair does not authorize replacing the whole design or content.
4. Extract existing answers and actual logo/font/scene URLs. Record absence honestly. Determine whether browser tools and authorized Blogger access are actually available.
5. Prefer the user's/native export. If preservation is requested but the XML is missing, request it through the coordinator; never silently substitute the laboratory. For a new theme without an export, the starting point is the complete [theme skeleton](../references/blogger-theme.md#the-skeleton).
6. Pass known choices, missing facts and the base strategy to interview. Do not generate discovery.md or edit the XML in this stage.

## Completion gate
The scope, base strategy and unknowns are explicit and traceable. Missing required files stay pending. All working notes remain in conversation/private scratch.
