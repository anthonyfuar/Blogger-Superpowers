# Internal Design Contract

Use the [Google design.md format](https://github.com/google-labs-code/design.md) as the structure for a working design contract. This is an original summary of the alpha format inspected on September 24, 2026, not an official certification or a bundled Google tool. Read the current upstream specification if a format detail is uncertain. No CLI, parser library or dependency installation is required.

The working plan may be Markdown in conversation or a temporary scratch design.md. It is never part of the final deliverables. Preserve the design reasoning while working; discard run-created temporary files after the one XML has been verified.

## Format

An optional YAML front matter carries exact design values; this workflow normally uses it for consistency. The Markdown body explains how to apply them. Tokens are authoritative: prose must not quietly contradict them.

Top-level fields: name; optional version, description and omitted; colors; typography; spacing; rounded; components. Use quoted hex values for predictable checking. Dimensions use px/em/rem; lineHeight and spacing may also use numeric values as specified. Do not confuse unitless ratios with pixel lengths.

Typography properties: fontFamily, fontSize, numeric fontWeight, lineHeight, letterSpacing, fontFeature and fontVariation. Component properties: backgroundColor, textColor, typography, rounded, padding, size, height and width. Describe additional browser details such as shadows, responsive rules and motion in prose instead of silently inventing schema guarantees.

Token references use `{colors.primary}`. Resolve the full path. Primitive tokens point to primitive values; component typography can reference a whole typography definition. Missing or circular references are errors, not occasions to invent values. Express hover/active/focus variants as separate components when appropriate.

Canonical H2 order:

1. Overview (or Brand & Style)
2. Colors
3. Typography
4. Layout (or Layout & Spacing)
5. Elevation & Depth (or Elevation)
6. Shapes
7. Components
8. Do's and Don'ts

Additional sections follow the canonical sequence: Blogger Views, Motion and Loading, Branding, Acceptance. Keep headings unique. Unknown custom headings are preserved; they are not proof of an upstream error. Optional groups can be intentionally omitted with a reason.

## Working example

These are sample values, not user answers. Replace them with the current brief.

```markdown
---
version: alpha
name: Paper and Sun
colors:
  primary: "#242424"
  surface: "#FAF8F4"
  accent: "#9C3324"
  on-accent: "#FFFFFF"
typography:
  heading:
    fontFamily: Georgia
    fontSize: 48px
    fontWeight: 700
    lineHeight: 1.1
  body:
    fontFamily: system-ui
    fontSize: 18px
    fontWeight: 400
    lineHeight: 1.65
spacing:
  sm: 8px
  md: 16px
  lg: 32px
rounded:
  card: 12px
components:
  page:
    backgroundColor: "{colors.surface}"
    textColor: "{colors.primary}"
    typography: "{typography.body}"
  button:
    backgroundColor: "{colors.accent}"
    textColor: "{colors.on-accent}"
    rounded: "{rounded.card}"
---
## Overview
Explain the actual audience and desired feeling.
## Colors
Explain each chosen role and tested foreground/background pair.
## Typography
Define real families, available weights, fallback and reading comfort.
## Layout
Define section order and reading width, then the layout of every view at phone, tablet and
desktop width: columns, order, navigation and what each large element becomes on a phone.
## Elevation & Depth
Describe shadows, layering and focus visibility.
## Shapes
Use a consistent radius/border language.
## Components
Define normal, hover, focus, active, empty and error behavior.
## Do's and Don'ts
List the decisions that prevent drift from the brief.
## Blogger Views
Map real native data, sections and gadgets to each required view.
## Motion and Loading
Use the actual interview decisions and resource coverage table.
## Branding
Hide Blogger credit/badges; preserve site copyright and resource notices.
Use a real brand favicon integrated into the XML where compatible.
## Acceptance
Record actual source, browser and Blogger checks with honest limits.
```

## Motion coverage table

Create a working row for headline/hero, navigation icons, buttons/links, cards/images, section entrances, background, page transition and actual content loading. Each row records the chosen effect or intentional stillness, its recipe ID from the [effects index](effects.md#choice-index), whether it is hardcoded or loads a CDN engine, trigger, intensity, repeat behavior, phone behavior, reduced-motion behavior, failure fallback and observed implementation status. An effect without a recipe ID is not designed yet. An area the brief wants still gets a row too, with “still by choice” in place of an ID. Count the rows that loop against the [budget](effects.md#limits-and-combinations) and note which element gives way if the budget is exceeded.

Make a visually rich design when requested. Use several appropriate sources when they serve different needs; do not fill the table with unnecessary libraries just to increase the count. Source unavailable, licensing unresolved or no real scene means unknown/pending, not implemented. Return a material design compromise to the coordinator.

## Review without installed tools

1. Verify front matter boundaries, values/types and every token reference; reject missing/cyclic references. Confirm a deliberate primary color and meaningful typography when colors are defined.
2. Check canonical order, duplicate headings, unsupported keys/properties and whether omitted groups have a reason. Treat unused tokens as a review signal, not automatic proof of failure.
3. Check each normal/hover/focus/disabled text pair in both modes. For sRGB, linearize each normalized channel c using c/12.92 when c ≤ 0.04045, otherwise ((c + 0.055)/1.055)^2.4 (the exponent is 2.4, not 2). Relative luminance is 0.2126R + 0.7152G + 0.0722B; contrast is (lighter + 0.05)/(darker + 0.05). Account for transparency against its actual backdrop. Use a tool already available for exact arithmetic; do not invent a measured ratio.
4. Aim for 4.5:1 normal text and 3:1 large text, with actual context and non-text focus/control contrast considered. Isolated ratios do not certify complete accessibility. See [Blogger design](blogger-design.md).
5. Compare prose, tokens and actual theme styles. Review all views, native features, editable controls, motion preferences and branding requirements. Confirm the Layout section covers phone, tablet and desktop for every view and meets the [responsive contract](responsive.md#the-contract).
6. On changes, compare the real prior and current plans. Do not claim Google's linter ran: this package provides an instruction-based review, not its executable. Record inspected versus executed checks accurately.
7. Convert settled values into plain CSS inside the XML. No tokens.css or design.md is delivered. Required documentation and validation limits go in the final chat, not extra files.
