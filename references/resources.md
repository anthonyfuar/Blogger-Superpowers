# Visual Resources and Browser Recipes

This Markdown reference replaces bundled engines, compiled demos, source catalogs and build tools. It contains original-source routes and small integration patterns only. No claim is made that every component of every site has been ported. Select from the user's answers and load only what the chosen theme actually uses.

## Creator credits

Credit the creator of every source a theme actually uses, in the final XML where the license asks for it and in the delivery note. A source listed here or in the source map is a candidate, not something already integrated: nothing is used until the user's answers select it and its browser route is verified.

| Project                                                           | Creator / team credit                                                                | What it offers                                                            |
| ----------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------- |
| [React Bits](https://reactbits.dev/)                              | [DavidHDev and contributors](https://github.com/DavidHDev/react-bits)                | Animated text, backgrounds and interactions.                              |
| [Its Hover](https://www.itshover.com/icons)                       | [Abhijit and contributors](https://github.com/itshover/itshover)                     | Animated icons.                                                           |
| [Space Type Generator](https://spacetypegenerator.com/)           | [Kiel M.](https://www.kielm.com/)                                                    | Kinetic lettering.                                                        |
| [simpleParallax](https://simpleparallax.com/)                     | [Geoffrey Signorato and contributors](https://github.com/geosigno/simpleParallax.js) | Image parallax on scroll.                                                 |
| [Anime.js](https://animejs.com/)                                  | [Julian Garnier and contributors](https://github.com/juliangarnier/anime)            | Sequenced text, SVG and interface animation.                              |
| [GSAP](https://gsap.com/)                                         | [GreenSock / Webflow and contributors](https://gsap.com/)                            | Timelines, entrances and scroll-driven animation.                         |
| [Spline](https://spline.design/)                                  | [Spline, Inc. and its team](https://spline.design/)                                  | Hosted interactive 3D scenes.                                             |
| [Originkit](https://www.originkit.dev/?sort=trending)             | [Originkit creators and contributors](https://www.originkit.dev/docs/why-originkit)  | Customizable interface components.                                        |
| [Boneyard](https://boneyard.vercel.app/overview)                  | [0xGF and contributors](https://github.com/0xGF/boneyard)                            | Skeletons for real loading states.                                        |
| [Fontpair](https://fontpair.co/)                                  | [Fontpair creators and curators](https://fontpair.co/)                               | Font-pairing ideas.                                                       |
| [Kokonut UI](https://kokonutui.com/)                              | [Dorian Baffier and contributors](https://github.com/kokonut-labs/kokonutui)         | Interface and motion components.                                          |
| [Magic UI](https://magicui.design/)                               | [Dillion and contributors](https://github.com/magicuidesign/magicui)                 | Animated interface components.                                            |
| [ShaderGradient](https://shadergradient.co/)                      | [ruucm and contributors](https://github.com/ruucm/shadergradient)                    | Moving gradient backgrounds.                                              |
| [Google Fonts](https://fonts.google.com/)                         | [Google Fonts and the typeface designers](https://github.com/google/fonts)           | Hosted font families.                                                     |
| [Google design.md](https://github.com/google-labs-code/design.md) | [Google Labs and contributors](https://github.com/google-labs-code/design.md)        | The format of the internal design plan; nothing from it ships in a theme. |

Original browser distributions take priority over adaptation; permitted selected-source adaptation takes priority over an original recreation when integration is needed. Never describe a source as used, integrated or ported unless its code or files are in the delivered XML.

### Attribution evidence and boundaries

Creator credits were checked against project sites/repositories on . The names are credited as displayed, including handles; contributors share credit.

- React Bits: [DavidHDev's repository](https://github.com/DavidHDev/react-bits).
- Its Hover: [the icons page](https://www.itshover.com/icons) identifies Abhijit and links its repository.
- Space Type Generator: [the project](https://spacetypegenerator.com/) credits [Kiel M.](https://www.kielm.com/).
- simpleParallax: [the homepage](https://simpleparallax.com/) names Geoffrey Signorato.
- Anime.js: [Julian Garnier's repository](https://github.com/juliangarnier/anime).
- GSAP: [the official site](https://gsap.com/) identifies GSAP as a Webflow product; credit also preserves the GreenSock project identity.
- Spline: [the official site](https://spline.design/) identifies Spline, Inc.
- Originkit: credit is to the [project's creators](https://www.originkit.dev/docs/why-originkit). A named individual creator could not be verified from the accessible official pages; do not invent one.
- Boneyard: [the documentation](https://boneyard.vercel.app/overview) credits 0xGF and links the repository.
- Fontpair: credit is to the [project's creators and curators](https://fontpair.co/). The current site's individual creator attribution could not be verified in this audit; do not treat a search snippet as proof of current ownership.
- Kokonut UI: [the homepage](https://kokonutui.com/) credits Dorian Baffier.
- Magic UI: [the homepage](https://magicui.design/) credits Dillion and links its repository.
- ShaderGradient: [ruucm's repository and contributors](https://github.com/ruucm/shadergradient).
- Google design.md: [the Google Labs repository and contributors](https://github.com/google-labs-code/design.md).
- Google Fonts: credit the [project and typeface designers](https://github.com/google/fonts); retain font-specific notices whenever font files are redistributed.
- 

Credits do not replace required license notices or establish permission to redistribute. Preserve notices for each selected source inside the final XML where applicable. Hiding Blogger platform branding never authorizes removing third-party credits.

## Resource priority

1. Original, version-pinned browser CDN with a verified public API.
2. Selected original browser-ready JS/CSS/SVG embedded in the XML, keeping required notices.
3. A focused adaptation of permitted original source to plain browser code, prepared without requiring a package/build installation.
4. An original recreation only when the other routes are unavailable, with fidelity differences explained.

Do not ship React source as if it were browser-ready. A genuine published browser module with explicit resolvable dependencies may run in a browser; JSX, Next server actions and bare imports do not. Prefer a usable original distribution or carefully adapt the selected component. Never invent an ESM/CDN endpoint, bundle a whole repository, run a development compiler in the visitor's browser or request an npm toolchain as a required setup step.

## Source map

What to ask about for each source, and where its working code lives. The route column is settled in the [tool routes](effects.md#tool-routes): three sources and the Spline viewer load from a CDN, and every other source is recreated with hardcoded recipes because it has no file a browser can load.

| Source                                                  | Ask about / consider                                           | Route in a static theme                                                                                                                                                                |
| ------------------------------------------------------- | -------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [React Bits](https://reactbits.dev/)                    | Animated text, entrances, cards, backgrounds, cursor effects   | Hardcoded: [E1–E5, T1–T10](effects-text.md), [S4–S10](effects-interaction.md), [B1–B7](effects-backgrounds-3d.md)                                                                      |
| [Its Hover](https://www.itshover.com/icons)             | Outline/solid icon language and hover/focus/tap motion         | Hardcoded: [I1–I3](effects-interaction.md#i1-icon-set-and-styles). Keyboard and touch are not implied by hover                                                                         |
| [Space Type Generator](https://spacetypegenerator.com/) | Kinetic typography, ribbons, circular or layered lettering     | Hardcoded: [T4–T7](effects-text.md#t4-circular-text). A user's own exported loop can be embedded as media                                                                              |
| [simpleParallax](https://simpleparallax.com/)           | Image depth, direction, intensity, mobile disablement          | CDN, or hardcoded CSS: [D1](effects-backgrounds-3d.md#d1-photo-depth-on-scroll)                                                                                                        |
| [Anime.js](https://animejs.com/)                        | Sequenced text, SVG motion, button/card responses              | CDN: [E6](effects-text.md#e6-hero-sequence-with-an-engine), [T9](effects-text.md#t9-drawing-underline-and-lines)                                                                       |
| [GSAP](https://gsap.com/)                               | Coordinated entrances, rich scroll/timeline behavior           | CDN: [E6](effects-text.md#e6-hero-sequence-with-an-engine), [D2](effects-backgrounds-3d.md#d2-moving-layers). Never together with Anime.js                                             |
| [Spline](https://spline.design/)                        | An existing interactive scene versus a simple decorative shape | CDN viewer plus the user's real scene: [D4](effects-backgrounds-3d.md#d4-spline-scene). Without a scene: [D3](effects-backgrounds-3d.md#d3-3d-object-in-css). Never invent a scene URL |
| [Originkit](https://www.originkit.dev/?sort=trending)   | Selected hero/background/text/interaction effects              | Hardcoded: the matching recipe from the [choice index](effects.md#choice-index). No bypass of paid access and no fictitious CDN                                                        |
| [Boneyard](https://boneyard.vercel.app/overview)        | Skeleton style during real asynchronous loading                | Hardcoded: [L6](effects-navigation-loading.md#l6-skeletons-with-a-real-request). Never a fake delay for server-rendered posts                                                          |
| [Fontpair](https://fontpair.co/)                        | Heading/body mood and pairing                                  | Pairing idea only; load the chosen families as in [fonts](responsive.md#fonts)                                                                                                         |
| [Kokonut UI](https://kokonutui.com/)                    | Shimmer text, interactive cards, playful controls              | Hardcoded: [T10](effects-text.md#t10-textured-and-shining-text), [S2, S6, S7, S9](effects-interaction.md)                                                                              |
| [Magic UI](https://magicui.design/)                     | Animated buttons, borders, text and decorative sections        | Hardcoded: [S4, S5](effects-interaction.md#s4-glowing-borders), [T5](effects-text.md#t5-ribbon-text), [B4, B7, B11](effects-backgrounds-3d.md)                                         |
| [ShaderGradient](https://shadergradient.co/)            | Flowing gradients, restrained versus immersive backgrounds     | Hardcoded: [B1](effects-backgrounds-3d.md#b1-flowing-color), CSS or its WebGL variant. Labeled as an original gradient, not the ShaderGradient renderer                                |

Aim for broad, meaningful use across the design: a signature visual, coherent icons, interaction feedback and tasteful reading/scroll details when desired. A no-motion design can still use the icon set, font pairings and strong composition. User preferences, supported browser behavior and actual rights determine what ships.

## Versioned CDN recipes

The pinned script tags, their integrity hashes, the verified calls and the loading rules are kept in one place: [Engines from a CDN](effects.md#engines-from-a-cdn). Use those tags exactly. Reverify the selected endpoint before a new final integration; a recorded fetch is evidence with a date, not a promise that an endpoint still answers. Include only the selected script, never the whole list, and do not use `async` for order-dependent initialization.

Every effect that needs script follows the same shape, shown in full in [the kit](effects.md#the-kit): look the target up and stop when it is missing, check the engine's global, honor reduced motion before creating anything, and leave native content visible if loading fails. No engine is needed for a simple CSS hover.

## Icons and interaction

Working code: [icon set, styles and motion](effects-interaction.md#i1-icon-set-and-styles). Choose a consistent family matching the user's answer. Inline only the SVG paths used, with proper namespaces, stable viewBox, currentColor where suitable and required notices. Decorative SVGs use aria-hidden; meaningful icon-only controls need an accessible label. Use buttons for actions and anchors for navigation. Provide equivalent focus/tap behavior where hover is selected. Avoid dependence on a moving cursor for understanding or operating a control.

## Parallax and continuous motion

Working code: [photo depth](effects-backgrounds-3d.md#d1-photo-depth-on-scroll) and [moving layers](effects-backgrounds-3d.md#d2-moving-layers). Use real image dimensions, reserved layout space and a modest travel distance. Keep text/controls readable; limit transforms to decorative layers. Avoid scroll-jacking and changing native page-scroll behavior. Respect reduced motion at startup and when preferences change; disable/reduce on phones if requested. Pause or destroy offscreen/hidden-document effects and clean observers/listeners when appropriate. Never lazily hide essential content waiting for a motion engine.

## Between-page transitions

A Blogger blog uses real document navigation. Keep anchors and native history. A visual transition is not permission to replace routing, comments, paging or metadata with an AJAX application.

Add a transition only when the user chose one; “instant navigation” means adding nothing. The transition is the browser's own cross-document view transition, with ordinary navigation as the fallback. Both pages must opt in and support varies, so describe it as an enhancement, not a promise. The complete CSS for each interview choice, including its reduced-motion behavior, is in the [navigation recipes](effects-navigation-loading.md#how-page-transitions-work-here); use one of those blocks as written. [MDN contract](https://developer.mozilla.org/en-US/docs/Web/CSS/Reference/At-rules/@view-transition).

If an explicit page-loading cue is also chosen, it must be nonblocking and clear on pageshow, including Back/Forward cache restoration, and on relevant cancellation/failure timeouts. Never delay departure to finish decoration. Never trap focus or cover content indefinitely. Do not intercept modified clicks, non-primary buttons, downloads, external targets, same-page anchors or non-HTTP schemes. Keep normal right-click/new-tab behavior. A slim indeterminate cue is honest; a simulated 0–100% bar is not measured progress. [pageshow reference](https://developer.mozilla.org/en-US/docs/Web/API/Window/pageshow_event).

## Actual loading states

Working code: [loading cues and a real request](effects-navigation-loading.md#loading-cues-the-rule). Keep initially available Blogger posts visible. Use a spinner, dots, skeleton or status text only for a real pending operation such as an asynchronously fetched gallery. Set aria-busy on that region, reserve its space and clear state on success, error and timeout. Do not invent a fixed delay so the loader can be seen. Do not tie the whole page to every third-party image/ad finishing. Use quiet status announcements; do not repeatedly announce decorative animation. Respect the chosen loading appearance, timing and motion level.

## Branding defaults

Blogger platform attribution and badges must be hidden by default on every generated public view. Never make the user reconfirm this during the interview. Only an explicit later user request overrides it.

- Inspect Attribution widgets and actual platform badges in the base. Omit/remove platform credit where cleanly supported. Preserve owner copyright and other useful text from a customized attribution area.
- Always include a narrowly scoped CSS hiding fallback for the identified native Attribution IDs/classes, so native expansion or retained gadgets cannot visibly reintroduce the credit. For a base using the conventional Attribution1 and widget Attribution class, the following is a starting rule; adapt to the actual inspected IDs/markup:

```css
#Attribution1,
.widget.Attribution { display: none !important; }
```

- Check the actual live markup when available; a rule alone is not proof across all themes. Do not hide every footer, delete every Google link, remove ReportAbuse indiscriminately or strip mandatory third-party notices. Hidden is not the same as deleted.
- Replace a default Blogger favicon with the user's existing non-Blogger icon or an original simple mark derived from the theme. The tag, where it goes and how to write the address are in [favicon](blogger-theme.md#favicon). Embed a tested SVG/PNG data image directly in the XML when appropriate so no favicon sidecar is delivered. Use a real hosted URL only if it actually exists. Inspect competing native declarations and preserve all-head-content; do not use empty href values or destructive head hacks.
- Site-level favicon settings are separate from XML. Use authorized access when available; otherwise explain any remaining upload/setup in chat without falsely claiming it is live. Preserve the one-file delivery by keeping the usable icon embedded and exposing any extraction/setup steps in chat only if needed. Do not create a second delivered icon file without a new user request.

Official mechanisms: [theme editing](https://support.google.com/blogger/answer/176245?hl=en), [widget tags](https://support.google.com/blogger/answer/46888?hl=en), [favicon setting](https://support.google.com/blogger/answer/9691230?hl=en). These do not certify any particular untested XML.

## Keep the finished theme clean

Only selected browser code, CSS, SVG/data images, native Blogger markup and required notices go into the XML. Each chosen resource has an internal note of version/source, rights, purpose, API, bytes/requests if measured and failure fallback. There is no delivered manifest or assets folder. No raw repo, JSON registry, Node package, Python helper, compiled dependency collection or temporary build remains in the skill or final output. A resource whose behavior was approximated is never described as the complete original implementation.
