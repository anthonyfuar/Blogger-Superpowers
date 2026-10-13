# Agent 03 — Design

## Required inputs and references
Resolved working brief and discovery findings. Read [design contract](../references/design.md), [responsive layout](../references/responsive.md), the [effects index](../references/effects.md), [Blogger design/platform](../references/blogger-design.md) and [resources and branding](../references/resources.md). Use the language docs if editable gadget behavior is uncertain.

## Steps
1. Write an internal design.md-format plan in conversation/private scratch. It is a working contract, never a final attachment. Replace examples with the user's real preferences.
2. Define exact color pairs, font roles/weights/fallbacks, spacing, shapes, breakpoints, reading width and component states. Map decisions back to actual answers or label assumptions.
3. Plan the layout at three sizes for every view: phone (320 to 480 px), tablet (about 768 px) and desktop (1280 px). State the column count, the order of sections, how the navigation behaves and what each large element becomes on a phone. Design the phone layout first. A plan that only describes the desktop page is incomplete.
4. Define homepage/post/page/search/label/archive/error layouts, section order, editable gadgets and real content sources. Preserve native comment and navigation behavior.
5. Add an effect coverage table for headline, navigation/icons, controls, cards/images, section entrances, background and between-page/loading behavior. Record for each row the recipe ID from the [effects index](../references/effects.md#choice-index), whether it is hardcoded or needs a CDN engine, its trigger, intensity, phone behavior, reduced-motion behavior and why an area remains static. Apply the [limits](../references/effects.md#limits-and-combinations): at most three continuous loops including the one moving background, one engine and one 3D element per view. If the brief's choices exceed them, return to the coordinator with the count and the question of which to keep moving; do not drop one silently. Rows the user wants still are recorded as “still by choice”.
6. Include hidden Blogger credit/badges on every public view and a real non-Blogger favicon strategy. Do not confuse platform branding with required third-party attribution.
7. Perform the design reference's structural/token/contrast review using reasoning and already-available tools. No Google CLI or installation is required. Record precisely whether checks were inspected or actually executed; do not claim lint ran when it did not.
8. Choose a distinctive English theme title inspired by the brief and a safe matching filename, for example Midnight-Dispatch.xml. Avoid collisions and invented trademark rights. The title is not permission to rename the user's blog.

## Completion gate
The plan is coherent, traceable and implementable; token references resolve; contrast and states are reviewed; every view has a phone, tablet and desktop layout; every selected effect has a recipe ID; motion and branding are specified. Pass the internal plan and evidence to resources. No design.md or tokens.css remains in the final destination.
