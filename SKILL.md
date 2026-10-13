---
name: blogger-superpowers
description: Create, redesign, migrate, and repair Blogger (Blogspot) XML themes through a tailored English design interview, preference-led animations and icons, integrated Blogger expertise, and one named XML deliverable. Use when the user wants a new Blogger theme or template, wants to edit or fix an existing theme .xml, or needs a Blogger template snippet, widget, or layout fix.
---

# Blogger Superpowers

Turn the user's preferences into a modern Blogger theme with considered motion, expressive icons and native blog functionality. Invoke with `/blogger-superpowers` in Claude Code or `$blogger-superpowers` in Codex. This single skill owns both design and Blogger XML expertise; do not invoke another Blogger skill.

## Starting a theme or editing one

A bare invocation in a new request starts the new-theme interview immediately; no extra creation prompt is required. Open with the user's business, brand or personal project: what it is called, what it offers or publishes, and who it serves; the main visitor goal follows in the next round. Use short, nontechnical rounds of two or three questions in the [opening sequence](references/interview.md#opening-sequence) before choosing the visual direction. Never assume every blog sells a product.

For an explicit snippet-only request, provide the focused snippet with its exact insertion or replacement location and escaping context; do not start a full-theme interview or create an unrelated complete theme.

If the invocation asks to edit an existing theme and provides an XML path, inspect that file first, preserve the original, and ask about the desired changes. Every stage then follows [editing an existing theme](references/editing.md): targeted edits on a copy, no redesign, a comparison with the original, and one new file beside it. Reuse known business and design answers; ask only relevant follow-ups. If the requested XML cannot be read, ask for its correct location instead of replacing it with an example. An invocation within an ongoing task continues that task; skill maintenance does not start a theme interview.

## Non-negotiable defaults

- Keep all instructions, questions, authored UI copy, working notes and final explanations in English. Preserve user-owned post content, official identifiers and brand names.
- Ask about animation amount and type, icon style and behavior, parallax, between-page transitions, real loading cues, backgrounds, mobile behavior and motion boundaries early. Ask every relevant follow-up; there is no fixed question limit, and no topic is asked twice. Reuse known answers and honor explicit requests to skip.
- Use as many appropriate resources from the supplied source list as the user's choices support. Never equate visual richness with loading every library. Explicit no-motion/low-data preferences win.
- A new theme starts from the [theme skeleton](references/blogger-theme.md#the-skeleton), never from memory: its Blog gadget, gadget data paths and comments include are the ones to use.
- **Every theme follows Blogger's documented syntax.** Before any XML is written, the implementation stage reads the Blogger language, design, verification, skeleton and responsive references in full. The finished file must pass the 36-item [syntax conformance pass](references/blogger-validation.md#syntax-conformance-pass) there and again in verification; a theme with a failed item is not delivered.
- A theme is static. Build every animation, icon, parallax, 3D, background, transition and loading choice from its recipe in the [effects index](references/effects.md): hardcoded CSS, SVG and JavaScript, or one pinned CDN file with its integrity hash. Never use React, JSX, Tailwind classes, npm packages or a build step, never invent a CDN address, and never improvise an effect that has no recipe without settling it with the user.
- **Every theme is responsive, always.** This is not an interview question. No view may scroll sideways from 320 px up, navigation must work by touch and keyboard, and nothing is hidden on phones. Start every skin from the [responsive](references/responsive.md) base and pass its check before delivery.
- Blogger's visible platform attribution and badges are hidden by default on every generated public view. Implement the scoped fallback rule even when the original credit is omitted, then verify it. Replace its standard favicon with a real brand/theme icon. Preserve owner copyright and required third-party notices.
- Deliver **exactly one UTF-8 `.xml` file** with a distinctive English title chosen from the brief, such as `Midnight-Dispatch.xml`. Do not deliver `theme-output`, design files, manifests, reports, previews, assets folders, archives or installation documents. Give concise setup/check results in chat. Do not overwrite unrelated existing files.
- This skill is **Markdown instructions only**: this file, `agents/` and `references/`. It bundles no scripts, runtimes, npm packages, upstream repositories, binary assets, build files or installable helper dependencies. Browser code snippets inside Markdown are recipes to adapt into the XML, not a local toolchain. Use original versioned browser CDNs or selected plain HTML/CSS/JS/SVG inline. No Python/Node/npm installation is part of using this skill.
- Every inline script in a generated theme wraps its code in comment-guarded CDATA (`//<![CDATA[` and `//]]>` on their own lines), checks that its library and target element exist, and, when it animates anything, honors reduced motion. Never ship a bare `<script><![CDATA[…]]></script>` or an empty script: it parses locally as XML and fails in the served page. See [XML contexts](references/blogger-language.md#xml-and-escaping-contexts).

## Ordered agent workflow

Start with [Coordinator](agents/00-coordinator.md). Each role is a separate instruction file, not an automatically registered process. Delegate bounded independent work only when agent tools and useful parallel work exist; otherwise execute the role locally and say so. Dependent stages stay sequential. Handoffs live in conversation or private scratch; no handoff files are delivered.

| Stage | Role | Working outcome | Primary references |
|---|---|---|---|
| 01 | [Discover](agents/01-discovery.md) | Real scope/base and unknowns | [Language](references/blogger-language.md), [sources](references/sources.md) |
| 02 | [Interview](agents/02-interview.md) | Actual preferences and effect choices | [Questions](references/interview.md), [effects index](references/effects.md#choice-index), [resources](references/resources.md) |
| 03 | [Design](agents/03-design.md) | Internal design.md-format contract with a layout per screen size | [Design contract](references/design.md), [responsive](references/responsive.md), [effects index](references/effects.md), [Blogger design](references/blogger-design.md) |
| 04 | [Integrate resources](agents/04-resources.md) | Working code for every selected effect | [Effects index and kit](references/effects.md) and the recipe files it points to, [resources](references/resources.md), [XML contexts](references/blogger-language.md#xml-and-escaping-contexts) |
| 05 | [Implement XML](agents/05-implementation.md) | Complete responsive candidate XML that passes the syntax conformance pass | [Theme skeleton and gadget data](references/blogger-theme.md), [responsive base](references/responsive.md), [language/data/widgets](references/blogger-language.md), [Blogger recipes](references/blogger-validation.md) |
| 06 | [Verify](agents/06-verification.md) | Honest checks and resolved local defects | [Verification](references/blogger-validation.md), [syntax conformance pass](references/blogger-validation.md#syntax-conformance-pass), [local preview](references/blogger-validation.md#building-a-local-preview), [responsive check](references/responsive.md#checking), [effect checks](references/effects.md#checking-an-effect), [design/performance](references/blogger-design.md) |
| 07 | [Deliver](agents/07-delivery.md) | Only the final named XML remains | [Recovery/delivery](references/blogger-validation.md), [branding](references/resources.md#branding-defaults) |

## The complete knowledge base is always available

Read the referenced section whenever uncertain, regardless of stage. If it does not establish a fact, inspect a current authoritative source or an actual user export. Mark the result observed, documented, assumed or unknown. Never invent Blogger properties, include names, API endpoints, exported scenes, user answers or test results.

- [Blogger language](references/blogger-language.md): formats/families, namespaces, escaping, tags/operators, views, data scope, sections, widgets, includes, comments and Layout.
- [Blogger design/platform](references/blogger-design.md): editable variables, responsiveness, accessibility, SEO, performance, feeds/API, integrations, limits, domains and retired services.
- [Theme skeleton and gadget data](references/blogger-theme.md): the complete starting theme for every new theme, the data each gadget offers, comments, owner-editable content, favicon, designer variables, and the fallback for each construct if Blogger refuses the file.
- [Blogger verification/recipes](references/blogger-validation.md): structural checklist, diagnostic matrix, how to build a local preview, real-view testing, installation/recovery, original recipes and laboratory starting point.
- [Editing an existing theme](references/editing.md): how each stage changes for an edit, a repair or a snippet.
- [Responsive layout](references/responsive.md): the contract every theme meets, Blogger's own stylesheet and how to cancel it, the base skin, phone navigation, post-content safety, fonts and the overflow check.
- [Effects index and kit](references/effects.md): every interview choice mapped to a recipe, the shared motion kit, how each named tool reaches a static theme, pinned CDN engines, limits and checks. Recipes: [text and entrances](references/effects-text.md), [interaction and icons](references/effects-interaction.md), [backgrounds, depth and 3D](references/effects-backgrounds-3d.md), [navigation and loading](references/effects-navigation-loading.md).
- [Sources and confidence](references/sources.md): dated evidence, official contracts versus demonstrations, currency and uncertainty.

Preserve native posts, comments, pagination, head dependencies and useful gadget IDs. Distinguish local source inspection, an executed parser, a browser mockup, Blogger saving and live public behavior. A local preview does not certify a theme.

## Model portability

These are plain Markdown instructions that Claude, Codex, Gemini, or another capable LLM can follow when given this folder and the necessary file/browser tools. Skill discovery and invocation differ by host. In a host without automatic skill loading, open SKILL.md and provide the linked references as needed. Do not claim that every model has file access, browser tools, or identical output quality.

## Repository showcase is not part of the skill

The public repository also holds an `examples/` folder and a README gallery. They exist only to show visitors what a finished theme can look like, and the install commands leave them out. They are never an input to this workflow. If an `examples/` folder happens to sit beside this file, do not open, search, quote, copy from or imitate it, and do not use it as a base, a template or a style reference. Every theme starts from the user's own answers or the user's own XML.
