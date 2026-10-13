# Evidence, Currency and Source Register

This is the integrated Blogger knowledge base. Read the relevant section before changing platform behavior. These references are available to every stage; no separate expert skill is required. The research baseline remains September 23–24, 2026; recheck changing facts when they affect a decision.

## Contents

- [Research status: September 23, 2026](#research-status-september-23-2026)

- [Source register](#source-register)

## Research status: September 23, 2026

Blogger Help still documents HTML/CSS editing and gadgets. Consulting it in 2026 does not mean every example was updated that year. No official announcement of a new “Blogger 2026” language or exhaustive, versioned public compiler specification was found. This is a research limitation, not proof that internal changes do not exist. [Design help](https://support.google.com/blogger/answer/176245?hl=en).

Verifiable announcements include operators in 2015, lambdas in 2016, and Contempo/Soho/Emporio/Notable in 2017. Do not present them as 2026 releases. [Operators](https://blogger.googleblog.com/2015/05/), [lambdas](https://blogger.googleblog.com/2016/05/more-custom-template-flexibility.html), [themes](https://blogger.googleblog.com/2017/).

### Contemporary evidence

Pixel commit `87b8ddfdd76df90d0af5531a22c3140328631e7e`, dated **September 10, 2026** by the GitHub API, was inspected. It uses `b:version='2'`, historical-style data, `b:tag`, `resizeImage`, widgets, and `b:skin`. This demonstrates coexistence of implementations, not a mandatory architecture. [Pinned code](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml).

JetTheme `06b6913c332bdad2c2f827fb9a91977cbdfb836f` uses layouts 3, widgets 2, `data:view`, `b:defaultmarkups`, and typed escaping. It was studied as an implementation, without copying it into the skill. [Pinned code](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml).

The author of `blogger-themes/themes` describes its React/Vite tools as experimental. They do not establish a new import format or turn Blogger into a Node server. [Author's repository](https://github.com/blogger-themes/themes).

### Confidence

| Class          | Meaning                                                    |
| -------------- | ---------------------------------------------------------- |
| Official       | Blogger Help, team announcements, API                      |
| Standard       | Specifications and browser documentation                   |
| Demonstration  | Author-provided code and examples; test at the destination |
| Recommendation | Engineering judgment, not a Google restriction             |
| Unconfirmed    | No current contract or reproducible test                   |

`template-data.blogspot.com` provides examples and dictionaries from 2016–2017. Institutional authorship was not formally verified; do not call it official documentation. Pages and their public feed were consulted to work around web-reader failures. There are errors and retired references: cross-check rather than copy blindly.

### Tutorials that need updating

- `FollowByEmail` lost email subscriptions in 2021; this does not mean all feeds disappeared. [Official notice](https://support.google.com/blogger/answer/10477563?hl=en).
- Recipes involving Google+, OpenID, and abandoned services need specific migration work. [Official announcements](https://blogger.googleblog.com/).
- Content backups may be Takeout `feed.atom`; historical XML content backups remain accepted. The theme has a separate backup. [Backup](https://support.google.com/blogger/answer/41387?hl=en).
- Images may be compressed; current help says they do not count against Google storage. Do not apply old Picasa/Google+ quotas. [Images](https://support.google.com/blogger/answer/41641?hl=en).
- INP replaced FID in Core Web Vitals. [Web Vitals](https://web.dev/articles/vitals).
- Google removed the sitelinks search box starting November 21, 2024. Do not sell `SearchAction` as a requirement for obtaining it. [Search Central](https://developers.google.com/search/blog/2024/10/sitelinks-search-box).

### What was verified

Documentary research, public source inspection, local XML, and historical analyzer behavior before the Markdown-only consolidation. No automated analyzer is shipped now. No user blog or exported XML was available for import/save verification. The [laboratory example](blogger-validation.md#laboratory-starting-point) is not certified for production. No claim is made to know all unpublished internal behavior.

The English translation and project-local relocation were completed on September 24, 2026; this does not change the original research cutoff.

## Source register

**Consulted:** September 23, 2026, America/Mexico_City. Document dates are distinguished from access dates. Search-engine “crawled” labels do not establish content updates. Links were consulted directly or, where noted, through the corresponding public feed.

### Blogger: contract and operation

| Source                                                                               | Use in the skill                                          |
| ------------------------------------------------------------------------------------ | --------------------------------------------------------- |
| [Change the design](https://support.google.com/blogger/answer/176245?hl=en)          | Theme editing, CSS, gadgets                               |
| [Page elements](https://support.google.com/blogger/answer/46888?hl=en)               | Sections, widgets, attributes, IDs                        |
| [Widget tags](https://support.google.com/blogger/answer/46995?hl=en)                 | Control, includes, scope, expressions                     |
| [Layouts data](https://support.google.com/blogger/answer/47270?hl=en)                | Historical widget data; not a complete v2 schema          |
| [Layouts CSS](https://support.google.com/blogger/answer/46871?hl=en)                 | Designer variables                                        |
| [Back up and import](https://support.google.com/blogger/answer/41387?hl=en)          | Theme separate from content; Takeout/Atom                 |
| [Settings](https://support.google.com/blogger/answer/9691230?hl=en)                  | Permissions, posts, comments, feeds, robots, monetization |
| [Permissions](https://support.google.com/blogger/answer/42673?hl=en)                 | Documented member limit; English-language help link       |
| [Posts and post templates](https://support.google.com/blogger/answer/154172?hl=en)   | Difference from themes, labels, jump breaks               |
| [Images and video](https://support.google.com/blogger/answer/41641?hl=en)            | Compression, storage, management                          |
| [Blogger SEO](https://support.google.com/blogger/answer/41373?hl=en)                 | Visibility, titles, links, redirects                      |
| [HTTPS](https://support.google.com/blogger/answer/6284029?hl=en)                     | HTTPS and CAA                                             |
| [Mixed content](https://support.google.com/blogger/answer/6286127?hl=en)             | Theme, post, and gadget resources                         |
| [Custom domains](https://support.google.com/blogger/answer/1233387?hl=en-uk)         | Domain/DNS configuration                                  |
| [FollowByEmail retirement](https://support.google.com/blogger/answer/10477563?hl=en) | Notice updated April 13, 2021                             |

Community references of Blogger's default gadget markup, used for the native includable names only: [Blogger Template Documentation](https://github.com/nikahmadz/Blogger-Template-Documentation/blob/master/blog-widget.md) and [bloggercode.orbiona.com](https://bloggercode.orbiona.com/1993/09/xml-v2-blog-commentPicker.html). Demonstration level.

### Language history and API

| Source                                                                                  | Date/value                                           |
| --------------------------------------------------------------------------------------- | ---------------------------------------------------- |
| [Operators](https://blogger.googleblog.com/2015/05/)                                    | May 22, 2015 announcement                            |
| [New tags](https://blogger.googleblog.com/2015/06/)                                     | June 25, 2015 announcement                           |
| [Lambdas](https://blogger.googleblog.com/2016/05/more-custom-template-flexibility.html) | May 24, 2016 announcement                            |
| [Theme families](https://blogger.googleblog.com/2017/)                                  | March 20, 2017 announcement                          |
| [Official blog](https://blogger.googleblog.com/)                                        | Announcement comparison; not an exhaustive changelog |
| [API v3 reference](https://developers.google.com/blogger/docs/3.0/reference)            | Public resources; no theme resource                  |
| [Usage and authorization](https://developers.google.com/blogger/docs/3.0/using)         | OAuth/API keys                                       |
| [API introduction](https://developers.google.com/blogger/docs/3.0/getting_started)      | Data model and testing environment                   |
| [posts.list](https://developers.google.com/blogger/docs/3.0/reference/posts/list)       | Pagination and filters                               |

### Author-provided data and markup examples

These examples are not labeled as institutional guarantees. The source contains historical material and errors corrected in the skill. Read them as syntax evidence and cross-check against actual exports.

- [data](https://template-data.blogspot.com/p/data.html), [b:if](https://template-data.blogspot.com/p/bif.html), [b:loop](https://template-data.blogspot.com/p/bloop.html), [b:with](https://template-data.blogspot.com/p/bwith.html).
- [b:class](https://template-data.blogspot.com/p/bclass.html), [b:attr](https://template-data.blogspot.com/p/battr.html), [b:tag](https://template-data.blogspot.com/p/btag.html).
- [b:eval](https://template-data.blogspot.com/p/beval.html), [b:message](https://template-data.blogspot.com/p/bmessage.html), [b:comment](https://template-data.blogspot.com/p/bcomment.html).
- [Operators](https://template-data.blogspot.com/p/operators_22.html), [demonstration source](https://template-data.blogspot.com/p/sauce.html), [responsiveImage](https://template-data.blogspot.com/p/snippets.html).
- [Pages feed](https://template-data.blogspot.com/feeds/pages/default?alt=json&max-results=150): alternative reading path for examples the web reader could not retrieve.
- [Dictionary feed](https://template-data.blogspot.com/feeds/posts/default?alt=json&max-results=150): pagination was followed through 223 entries; fields and original links were inspected. The entire catalog is not redistributed. Used fields are linked in Data.

### Code from its authors

| Pinned source                                                                                                                            | Evidence                                                                 | Limitation                                            |
| ---------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ | ----------------------------------------------------- |
| [Pixel XML](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml) | September 10, 2026 commit; namespaces, widgets, helpers, historical data | Do not treat its architecture as a Google requirement |
| [JetTheme XML](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml)                           | Modern versions, defaults, escaping, views                               | No redistribution permission inferred                 |
| [blogger-themes/themes](https://github.com/blogger-themes/themes)                                                                        | Author describes React/Vite and experimentation                          | Do not claim production stability                     |

The GitHub API was consulted to pin commits; user account access was not required. Temporary inspected sources are not included in the package.

### Web standards and quality

| Source                                                                                                              | Use                                        |
| ------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| [W3C XML 1.0](https://www.w3.org/TR/xml/)                                                                           | Well-formedness, entities, CDATA, comments |
| [W3C WCAG 2.2](https://www.w3.org/TR/WCAG22/)                                                                       | Accessibility and verifiable criteria      |
| [MDN script](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/script)                           | Script execution and attributes            |
| [MDN XSS](https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/XSS)                                        | Output contexts                            |
| [MDN service worker](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)              | Origin, MIME, scope                        |
| [Web Vitals](https://web.dev/articles/vitals)                                                                       | LCP, INP, CLS, lab/field distinction       |
| [Optimize LCP](https://web.dev/articles/optimize-lcp)                                                               | Image discovery and priority               |
| [Canonicalization](https://developers.google.com/search/docs/crawling-indexing/canonicalization)                    | Duplicates and signals                     |
| [Pagination](https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading) | Links, URLs, incremental loading           |
| [Article structured data](https://developers.google.com/search/docs/appearance/structured-data/article)             | Editorial metadata                         |
| [Sitelinks search box retirement](https://developers.google.com/search/blog/2024/10/sitelinks-search-box)           | Effective November 21, 2024                |
| [Search AI features](https://developers.google.com/search/docs/appearance/ai-features)                              | Avoid invented AI SEO requirements         |

### Maintaining the research

Update what affects a concrete decision first: dashboard options, available gadgets, data/versions, API quota, integrations, Search, and dependencies. Record URL, publication date where available, access date, observation, and confidence level. Prefer a reproducible export and minimal case to a list of untested assumptions.

Do not treat old forum limit lists, commercial pages with changing years, “free premium” theme packages, Lighthouse scores without URLs/circumstances, or arbitrary Blogspot-hosted snippets as contractual evidence. This skill's recommendations are original synthesis; references are not full manual reproductions.

## Consolidation and no-install design review

Blogger Superpowers incorporates the former Blogger XML expertise directly into the four integrated Blogger reference chapters. Its language/data/widget rules, design/SEO/platform guidance, examples, validation logic and source register remain available without invoking a separate skill. The former analyzer is now an explicit structural checklist; its executable and test runner are not shipped. The laboratory is a fenced Markdown example, not a separate asset file.

The internal design contract follows the [Google design.md format](https://github.com/google-labs-code/design.md) as inspected in September 2026. The Markdown-only workflow does not run or claim to bundle Google's linter. See [design review](design.md) for structure, references, contrast and consistency checks.

Resource URLs and example hashes come from dated original-source inspections, recorded in [resources](resources.md); verify selected live endpoints before integration. Browser navigation transitions and pageshow behavior were rechecked against MDN on September 24, 2026. Source availability or a saved URL does not establish permissions, working integration or all-library parity.



## Effects and responsive references: evidence of October 12, 2026

The [effects](effects.md) and [responsive](responsive.md) references were written and checked on this date. What stands behind them:

- **Live Blogger, observed.** A public Blogger blog (`blogger.googleblog.com`) served its inline scripts with the `//<![CDATA[` lines intact in the HTML, and loaded Blogger's widget stylesheet `css_bundle_v2.css` ahead of its skin. The rules listed in [Blogger's own stylesheet](responsive.md#bloggers-own-stylesheet) were read from that file. One blog on one day is a demonstration, not a contract.
- **CDN files, fetched and hashed.** GSAP 3.15.0 core, ScrollTrigger and SplitText; Anime.js 4.5.0; simple-parallax-js 7.0.0 from jsDelivr; the Spline viewer 2.0.76 from Spline's CDN, byte-identical to the npm copy. Their globals and the calls named in [Engines](effects.md#engines-from-a-cdn) were exercised in Chrome 154.
- **Package facts, read from the published packages.** `boneyard-js` 1.10.0 ships framework adapters and a capture CLI and no browser file. `@shadergradient/react` and `shadergradient` declare React as a peer dependency. Its Hover's repository describes React components built on `motion/react`. Originkit's distribution could not be established from its public pages.
- **Recipes, executed.** Every code block in the four recipe files, the kit and the responsive reference was extracted as written and run in headless Chrome 154 with Blogger's stylesheet loaded first, at 1280, 375 and 320 px, with reduced motion, with JavaScript off and with the CDN blocked: no script errors, no sideways scroll, no content left hidden. The phone menu, table wrapping and the related-posts request (success, hostile titles and links, failure) were exercised. The Spline recipe loaded a real public scene on demand and stayed a still image on a phone and with a placeholder address.
- **Page transitions, observed.** Each of N1 to N4 was run between two pages on one local origin in Chrome 154: the browser animated the old and new page with the recipe's keyframes, and did nothing with reduced motion. The N5 line turned on for a plain same-site click, ignored a same-page anchor and a modified click, and was clear on the new page.
- **Gadget data, read from a pinned source.** The JetTheme file pinned below was read again for the [theme skeleton](blogger-theme.md): it uses `data:post.date format …`, `date.iso8601`, `data:posts.size`, `data:links` with `link.href`, `link.title` and `link.isCurrentPage`, `data:label.name`, `label.url` and `label.count`, `data:i.post-count`, `cond` on `b:section` and `b:widget`, and `b:widget-setting name='content'`. It defines an empty includable with the id `commentPicker`, which is the basis for treating that id as Blogger's native comments include. It is a third-party theme: a demonstration, not a contract.
- **Whole themes, generated and checked.** Three independent runs of the full workflow, each for a different fictional blog and with no access to the example gallery, produced a still reading theme, a dark theme with a GSAP opening, CSS 3D and pointer effects, and a warm editorial theme with a ribbon, parallax and related posts. Each was rendered through an approximate local simulation of Blogger with Blogger's stylesheet loaded, on six views at 320, 375, 768, 1024 and 1280 px, with JavaScript off, reduced motion, a blocked CDN and dark mode: no sideways scroll, no script errors, no content left hidden. Those runs reported the gaps and recipe defects that the theme skeleton, the preview instructions and the corrected recipes now address. The skeleton itself passes the same checks.
- **A second round, after the corrections.** Three more independent runs on the corrected skill: a new playful theme with an Anime.js opening, particles, an orbiting badge, a gallery, a light and dark toggle and the reading recipes; the first round's editorial brief again, for comparison; and an edit of an existing theme that added four features and changed its accent color. All three passed the same checks with no failures, and the edit left the original file byte for byte unchanged. In the first round each run wrote its own Blog gadget, comments include and workarounds from memory; in the second, the code written without a source in these references was the theme's own look, roughly an eighth to a fifth of each file. Their remaining findings were applied afterwards and are covered only by the recipe checks, not by a third round of whole themes.
- **Blogger Help, reread on October 13, 2026.** The four contract pages (page elements, widget tags, data tags, fonts and colors) were read again to write the [structural contract](blogger-language.md#structural-contract) table and the [syntax conformance pass](blogger-validation.md#syntax-conformance-pass): ids of letters and numbers only, the recommended section classes and what they are for, the values of each gadget attribute, the rule that an include names an includable of the same gadget, the self-closing separators, the documented operators and examples, the fields of each gadget, and the form of a skin variable. Those pages describe the first generation of the language; their examples also contain small typographic slips, which is why the pass tests rules rather than copying examples.
- **Blogger's default Blog markup, from community references.** Two independent references that reproduce the default version-2 Blog gadget ([an includable list](https://github.com/nikahmadz/Blogger-Template-Documentation/blob/master/blog-widget.md) and [a page per includable](https://bloggercode.orbiona.com/1993/09/xml-v2-blog-commentPicker.html)) list `commentPicker` among its includables, with `var='post'`, choosing between threaded and plain comments, and show Blogger's own `postCommentsAndAd` calling it with `data='post'` on single-item views. They are not Google publications. Together with the third-party theme that overrides that id, they move the comments include from inferred to documented by demonstration.
- **Conformance, checked.** The skeleton, the six generated test themes and the 20 gallery themes were run through an automated version of the conformance pass. The skeleton and all six test themes had no failure. The gallery themes had three sections each inside a condition and no section classes; both were corrected, and all 20 now pass.
- **A third round, October 13, 2026.** One more independent run, for a theme with a GSAP opening, a flowing background, a count-up, an image-reveal gallery, a glowing card, a parallax band, a light and dark toggle and a print layout, measured each effect instead of only checking that it existed, and found five defects that the earlier checks had passed: the scroll-driven parallax of D1 and D2 never moved, because the frame's `overflow: hidden` made the frame the scrolling area the animation followed; with reduced motion the mirrored “previous” arrow turned to point forward under the pointer; the print layout kept light text when the device was in dark mode; entrances that had not yet been scrolled into view were still fading in at the moment of printing; and embedded frames without a `frameborder` attribute showed the browser's default border. Each was reproduced in Chrome 154 from the recipes as written, corrected, and confirmed by the same measurement. The run also found, by measuring, that its header bar needed a higher menu breakpoint than the suggested one; the header-row check in the [responsive checks](responsive.md#checking) comes from that.
- **A fourth run, on the corrected references, October 13, 2026.** The same brief was run again from scratch. It measured each effect doing its job (the parallax photo moving from one end of its range to the other across the scroll, the mirrored arrow staying mirrored with reduced motion, print black on white in all three color modes), passed the conformance pass with no failure, and wrote about an eighth of its lines without a source, all of them the theme's own look. Its eight remarks were gaps and unclear wording, not broken recipes: the photo zoom's `height: auto` fought a fixed-ratio card frame; nothing said how to seat a search form in the bar, and the suggested breakpoint was too low for a bar that also holds a mode button; the glowing border did not say how to sit on a skeleton card; the reading-time icon had no pattern; the head order after the skin was stated in three places; the stalled-CDN check asked for more than the kit promises; and the canonical post address was missing from the data table. Each was corrected, the ones with code were measured in Chrome 154, and the run then revised its theme to follow the corrected sections as written. That final theme passed the automated conformance check and the browser checks on six views at five widths with no failure, and its parallax, card photos, header row, phone menu, print layout, arrows and reading time were measured again independently.
- **Embedded video on a live blog, observed.** Posts on `blogger.googleblog.com` embed uploaded videos as `iframe` elements whose address contains `blogger.com/video.g`, and YouTube videos from `youtube.com/embed/`; both carried `frameborder='0'`. The responsive video rule matches both addresses. Frames pasted by hand do not always carry that attribute.
- **The Safari engine, October 13, 2026.** After a last pass that made the fourth run's theme follow the final wording of every corrected section, that theme, every recipe page and the regression measurements were run again in WebKit, the engine of Safari 26.2, through the system web view of macOS 26.2 at 320 to 1280 px wide. This is not Safari's own window and not an iPhone. The theme passed 34 of 34 checks (six views at five widths, JavaScript off, dark mode), the recipe pages 41 of 41, and each measurement matched Chrome: the scroll-driven parallax moved across its range, the glowing border turned, print came out black on white in dark mode, card photos filled their frames and the header stayed on one row. Reduced motion and a blocked or stalled CDN cannot be emulated in that web view and were checked in Chrome only.
- **A live Blogger blog, reported by the owner, October 13, 2026.** The skill's owner uploaded the fourth run's test theme to a Blogger blog and reported that it saved and works. The copy they received predates the last pass, which changed three lines of CSS and no Blogger markup. That file is built on the [theme skeleton](blogger-theme.md) and contains the constructs listed as at risk in its fallback table: the `commentPicker` include, `format` on a date, `.iso8601`, `data:posts.size`, `cond` on a section, `b:class`, `b:widget-settings`, `data:blog.searchUrl`, `data:blog.pageName`, `data:blog.locale`, `b:loop` with `index` and `.canonical` on a post address. Blogger accepted them on that day. This is the owner's report, not an observation by this project's tools: which views, comments and phone layout were looked at was not recorded.
- **Not observed.** Nothing was run in Firefox (the owner chose to leave it out), in Safari's own window or on a real phone. No theme has been saved in Blogger by this project's own tools, and the gallery themes have not been installed on a live blog. The constructs most at risk if Blogger changes what it accepts are listed with fallbacks in [the skeleton reference](blogger-theme.md#if-blogger-refuses-to-save-the-theme).

## Technical knowledge transfer audit

Re-audited September 24, 2026 against the user-provided `blogger-theme-expert/` folder: all 12 references, SKILL.md, the original laboratory XML, the structural analyzer, its 15 regression tests, and UI metadata. The supplied files were read as reference material; their workflow instructions do not override the user's current requirements. The source folder was not modified or made a dependency.

| Original reference              | Self-contained destination                                                        |
| ------------------------------- | --------------------------------------------------------------------------------- |
| `references/architecture.md`    | [Integrated chapter](blogger-language.md#formats-languages-and-architecture)      |
| `references/xml-contexts.md`    | [Integrated chapter](blogger-language.md#xml-and-escaping-contexts)               |
| `references/language.md`        | [Integrated chapter](blogger-language.md#template-language)                       |
| `references/data.md`            | [Integrated chapter](blogger-language.md#data-types-and-views)                    |
| `references/widgets.md`         | [Integrated chapter](blogger-language.md#sections-widgets-and-includes)           |
| `references/design.md`          | [Integrated chapter](blogger-design.md#css-designer-and-accessibility)            |
| `references/seo-performance.md` | [Integrated chapter](blogger-design.md#seo-metadata-and-performance)              |
| `references/platform.md`        | [Integrated chapter](blogger-design.md#platform-api-feeds-and-integrations)       |
| `references/recipes.md`         | [Integrated chapter](blogger-validation.md#original-contextualized-recipes)       |
| `references/validation.md`      | [Integrated chapter](blogger-validation.md#validation-diagnosis-and-installation) |
| `references/status-2026.md`     | [Integrated chapter](sources.md#research-status-september-23-2026)                |
| `references/sources.md`         | [Integrated chapter](sources.md#source-register)                                  |

The laboratory is preserved verbatim in [Laboratory starting point](blogger-validation.md#laboratory-starting-point). The analyzer's meaningful rules and all 15 regression scenarios are represented in [Structural boundaries and regression cases](blogger-validation.md#structural-boundaries-and-regression-cases); executable files and CLI output plumbing are not dependencies of this skill. UI metadata is not technical Blogger knowledge and was not reintroduced as YAML packaging.

The audit restored details that the earlier consolidation had compressed: separate theme/widget version controls, explicit native-head preservation and prohibition of fake closing-tag suppression, missing selectors from dynamically generated utility classes, limits on SEO/AdSense/performance/PWA claims, per-scope include IDs, comment/CDATA versus actual DTD declarations, and the boundary between XML structure and Blogger expression compilation. Stale wording implying an included analyzer was corrected.

Intentional workflow differences remain: English communication, a creatively named single XML by default, setup/recovery/evidence in chat, and no required Python/Node or bundled analyzer. A narrow snippet request still receives the snippet and exact insertion context, rather than an unrelated full-theme workflow. No technical reference chapter was discarded. This audit verifies transfer from the supplied material, not completeness of all Blogger knowledge or live Blogger acceptance.

Source inventory fingerprint (sorted relative paths and SHA-256 file digests; excludes `.DS_Store`): `336cf019123bdb9320fda02488cdc24978855c9cf88473f040bcc8a6a6f0334d`. Inspected files: 17.
