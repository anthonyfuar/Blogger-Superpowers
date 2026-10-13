# Editing an Existing Theme

For a request to change, repair or extend a theme the user already has. The seven stages still run, in order, but each does less: the theme exists, most decisions are already made, and the job is to change what was asked and nothing else. Every stage file applies with the adjustments below.

The rule above all others: **the user's file is never modified.** Work on a copy. The original stays where it is, byte for byte.

## Stage by stage

**01 Discover.** Read the whole file. Record its family and versions, sections, gadgets and their ids, the Blog gadget's includables, the skin's tokens and variables, its scripts, the fonts and CDN files it loads, and how it names things. Note where it differs from this skill's practice (no Blogger-stylesheet cancel block, no safety net on the phone menu, an unguarded script, no `[hidden]` rule, fixed widths). Those notes become offers to the user later; they are not permission to change anything.

**02 Interview.** Do not run the opening sequence. Ask what should change and, for each change, only what is needed to build it:

- where it appears and on which views;
- how it should behave on a phone;
- for an effect, the same points as for a new theme: how it starts, how strong, whether it repeats;
- what must stay exactly as it is.

Ask about the blog itself only when a change depends on it. Take everything else from the theme: its palette, fonts, shapes and tone are the answers to the questions not asked.

**03 Design.** Write a change plan, not a new design contract: one entry per change with its recipe ID, the elements and tokens it touches, the views it affects, its phone and reduced-motion behavior, and what stays untouched. New colors are checked for contrast in every mode the theme has. New motion is counted against the [budget](effects.md#limits-and-combinations) together with what the theme already has.

**04 Resources and 05 Implement.** Change the copy with targeted edits:

- Edit text in place. Never parse and re-serialize the whole XML: that rewrites quoting, CDATA and settings the user did not ask to touch.
- Keep every section and gadget id, type, title and setting, and every includable that is not part of a change.
- Follow the theme's own conventions: its class prefix, its token names, its level of browser fallbacks, its breakpoints.
- Add a recipe's CSS at the end of the skin, under a comment naming the change. Add its script as a new function at the end of the existing body script, or as one new comment-guarded script before the closing body tag. Leave existing scripts as they are.
- Use the theme's existing tokens inside recipe code instead of the recipe's placeholder colors.
- If a recipe needs the [kit](effects.md#the-kit) and the theme has none, add the whole kit and its head gate; otherwise add only the recipe.
- Do not retrofit the [base skin](responsive.md#base-skin) or the [skeleton](blogger-theme.md#the-skeleton) onto a theme that was not built on them. Where a missing piece is the direct cause of a defect the user reported, fix that piece and say so.

Three cases that need care:

- **Adding movement to a still theme.** A theme made for “no motion” may switch every transition and animation off with one global rule. A recipe added later is silently killed by it. Add a narrow exception for the new element only, inside `@media (prefers-reduced-motion: no-preference)`, and confirm with the user that this one movement is wanted.
- **Changing a brand color.** The accent usually appears in more than the skin: the embedded favicon, `theme-color` meta tags, inline SVG, both color modes. Change them together and recheck contrast in each mode.
- **Changing how colors follow the device.** See the notes at the end of [U1](effects-interaction.md#u1-light-and-dark-toggle).

**06 Verify.** Everything in the verification stage applies to the changed parts and to every view they touch. The [syntax conformance pass](blogger-validation.md#syntax-conformance-pass) is run on the whole revised file; where the original already failed an item that the request did not touch, report it as an offer, do not fix it unasked. In addition, compare the result with the original:

- A line diff: report how many original lines changed and how many were added, and account for each group. Nothing outside the requested changes should appear in it.
- Structure: sections, gadgets, ids, settings and untouched includables are equal in both files.
- Layout, when browser tools are available: build the same preview from both files and confirm that elements that existed before sit where they sat, at 320, 375 and 1280 px.
- The responsive check on every view, even those the change was not meant to affect.

**07 Deliver.** One new XML beside the original. Keep the theme's title; name the file after the original with a short suffix for this revision, such as `Paper-and-Sun-Evergreen.xml`, and update the title comment inside it to match. In the chat reply:

- list each change in one line, in the user's words;
- say what was deliberately left alone;
- offer, separately, the improvements noted in discovery that were not asked for, each as a yes-or-no choice;
- give the usual backup, upload and recovery steps, and say the file has not been saved in Blogger.

## Repairs

For “this is broken” rather than “change this”: find the cause before touching anything, using the [diagnosis table](blogger-validation.md#diagnosis-by-symptom). Fix the cause with the smallest change that removes it. Do not redesign, restructure or modernize around a repair. If Blogger refuses the user's own file with a message, that message and its line number are the starting point.

## Snippets

A request for one piece of code gets that piece: the code, the exact place it goes (which file part, before or after which existing line), and what kind of context that is (skin CSS, template markup, guarded script, or a gadget's content). No interview, no new theme, no file.
