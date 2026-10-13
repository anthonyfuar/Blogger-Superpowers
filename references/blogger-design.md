# Blogger Design, SEO and Platform

This is the integrated Blogger knowledge base. Read the relevant section before changing platform behavior. These references are available to every stage; no separate expert skill is required. The research baseline remains September 23–24, 2026; recheck changing facts when they affect a decision.

## Contents

- [CSS, designer, and accessibility](#css-designer-and-accessibility)

- [SEO, metadata, and performance](#seo-metadata-and-performance)

- [Platform, API, feeds, and integrations](#platform-api-feeds-and-integrations)

## CSS, designer, and accessibility

### Owner-editable CSS

`b:skin` holds CSS and variable definitions. Help documents a unique name, description, type, and default value; `$variable` references are substituted by the engine. [Official variables](https://support.google.com/blogger/answer/46871?hl=en).

Conservative original example:

```xml
<b:skin><![CDATA[
/*
<Variable name="accent" description="Link color" type="color"
          default="#1859a9" value="#1859a9"/>
<Variable name="bodyfont" description="Body font" type="font"
          default="normal normal 18px Arial, sans-serif"
          value="normal normal 18px Arial, sans-serif"/>
*/
:root { --accent-color: $accent; }
body { font: $bodyfont; line-height: 1.65; }
a { color: var(--accent-color); }
]]></b:skin>
```

When to define designer variables at all, and how many, is in [designer variables](blogger-theme.md#designer-variables). `$accent` belongs to Blogger; `var(--accent-color)` belongs to the browser. One resolves before the other. Do not confuse variables with JavaScript properties or assume a custom property automatically becomes editable in the designer.

Real themes also use `Group`, dotted names, selectors, length/background types, and metadata. To preserve the designer, start from exported declarations and test each control; historical help does not specify every combination. Do not change an existing variable's type without checking its consumers. [Examples observed in Pixel](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml).

### Recommended visual system

Define typography, reading width, spacing scale, palette, borders, and states first. Avoid hundreds of divergent constants. Scope the theme's own component classes with a short prefix taken from the theme title. The short list of class names that must keep their fixed names is in the [theme skeleton](blogger-theme.md#how-to-build-on-it). Distinguish `.post-body` from general site chrome: older posts may contain inline styles, tables, code blocks, and aligned photos.

```css
:root { --content-width: 74rem; --gap: clamp(1rem, 3vw, 2rem); }
.bt-shell { max-inline-size: var(--content-width); margin-inline: auto; padding-inline: 1rem; }
.bt-layout { display: grid; gap: var(--gap); }
.bt-layout > * { min-inline-size: 0; }
.post-body { overflow-wrap: anywhere; }
.post-body img { max-inline-size: 100%; height: auto; }
.post-body pre { overflow: auto; }
@media (min-width: 64rem) {
  .bt-layout { grid-template-columns: minmax(0, 1fr) 19rem; }
}
```

This CSS is a proposal, not an engine requirement. Test it against the real structure: Blogger-generated wrappers may change which elements are direct children.

Unless `b:css` is disabled, Blogger also serves its own widget stylesheet ahead of the skin, and a local mockup does not contain it. On October 12, 2026 the stylesheet served to a public Blogger blog set side margins on `.section`, vertical margins and a line height on `.widget`, zero margins on `h1` to `h4`, list padding through `.widget ul` and `.widget li`, and `word-break: break-word` on `body`. Those rules shift full-width layouts, flatten heading spacing and out-rank single-class list styles. The [responsive reference](responsive.md#bloggers-own-stylesheet) lists them and gives the block that cancels them; every skin starts with that block. This is observed behavior, not a documented contract: inspect the stylesheet actually served to the saved theme before claiming a layout matches a preview. Disabling `b:css` instead is a larger change that leaves native comments and gadgets unstyled.

### Responsiveness and difficult content

Every theme is responsive. The contract, base skin, phone navigation, post-content rules and the check are in [responsive layout](responsive.md); this paragraph is only the summary. Choose breakpoints when content requires them. Validate at 320, 375, 768, 1024 and 1280 px, zoom, and orientation. Test huge titles, long words, tables, and embeds. For wide tables, use a scrollable container with an accessible name where appropriate; do not indiscriminately turn `table` into a block. Use a video wrapper with an appropriate aspect ratio rather than arbitrary height.

Do not fix card heights assuming two-line titles. Do not hide important content on mobile. Late-loading ads and widgets need reserved space.

### Accessibility

WCAG 2.2 provides criteria for contrast, reflow, keyboard use, focus, and touch targets. Useful references: 4.5:1 for normal text, 3:1 for large text; a 24×24 CSS px AA minimum target size with its exceptions. Do not claim full compliance based on these isolated values. [WCAG 2.2](https://www.w3.org/TR/WCAG22/).

Recommended application to themes:

- A skip-to-content link, landmarks, and coherent heading hierarchy.
- Buttons for actions; real links for navigation. Visible form labels.
- Keyboard-usable mobile navigation, synchronized `aria-expanded`, Escape dismissal, and preserved focus.
- Do not use `role='menu'` for ordinary navigation unless implementing its entire keyboard contract.
- Identifiable `:focus-visible`; no global `outline:none`. Do not disable zoom.
- Empty alt text for decorative images; relevant descriptions for informative images, without mechanically repeating the title on every image.
- `prefers-reduced-motion` to reduce animation; content readable without depending on motion or hover.
- Language and direction from context, with logical properties such as `margin-inline` for RTL.

### Light/dark themes

Use the system preference as the CSS default; optionally provide a manual switch. If persisting through `localStorage`, tolerate exceptions and unavailable storage. Define consistent pairs for backgrounds, text, links, and focus. Do not invert the whole page with filters: that breaks photos and third-party colors. Test comments and iframes that do not inherit theme tokens.

### Browser-ready styling

Write final CSS directly. Do not require Sass/Tailwind installation, browser-time JSX compilation or a CSS development CDN. Selected original components that need a build system must be replaced by their official browser-ready distribution or adapted to plain browser code with honestly documented behavior. Do not introduce a framework merely for a menu or theme switch.

When inspecting an existing theme or an already compiled component, check whether its CSS generator scanned the XML and includables. Dynamically assembled utility class names may be absent from the generated CSS. Restore needed final selectors directly without introducing a required build tool or runtime compiler.

## SEO, metadata, and performance

### Head and views

Inventory the rendered head before adding tags. Maintain a consistent policy for titles, descriptions, canonicals, robots, Open Graph, and shared images; prevent the native head, plugins, and theme from emitting contradictory variants. Do not paste a generic mega-block of SEO markup.

| View | Proposal |
|---|---|
| Homepage | Brand and purpose; site image if available |
| Post | Its own title/description and individual canonical URL |
| Static page | Its own metadata; do not pretend it is an editorial article |
| Label/archive | Name and context; deliberate indexing policy |
| Internal search | Evaluate indexing; never canonicalize all posts to the homepage |
| Error | HTTP status and useful message; do not simulate an error with a 200 page |

A canonical is a signal, not an order; Google may select another. Avoid handling `?m=1`, HTTP/HTTPS, or filters through improvised JavaScript redirects. [Canonicalization](https://developers.google.com/search/docs/crawling-indexing/canonicalization).

### Platform settings

Blogger allows visibility, search description, redirects, and robots controls. These are separate from XML; a theme alone does not fix a blog marked non-indexable. Do not impose global `noindex` or robots settings without understanding which content should appear. [Blogger SEO help](https://support.google.com/blogger/answer/41373?hl=en).

Robots rules preventing crawling are not equivalent to `noindex`: consult Search Central's contract if changes are needed. First inspect the served robots file and sitemaps actually offered by the blog (`/sitemap.xml` and variants that exist); do not assume a handmade feed is necessary. Never publish secrets relying on robots protection.

### Structured data

`BlogPosting` or `Article` may suit editorial posts; include truthful data consistent with visible content: title, author, dates, and images. Do not invent an author, ratings, or recent date. Validate JSON and use Rich Results Test. Eligibility does not guarantee rich results. [Article](https://developers.google.com/search/docs/appearance/structured-data/article).

A theme may emit data from Blog, not necessarily the head. Inspect all JSON-LD scripts and microdata to avoid contradictory entities. `BreadcrumbList` should represent a real path; multiple labels do not automatically establish a category hierarchy. A contact page should not inherit `BlogPosting` merely because it reuses Blog's body.

Google removed sitelinks search in November 2024. A `SearchAction` can exist semantically, but do not promise that presentation. [Removal](https://developers.google.com/search/blog/2024/10/sitelinks-search-box). Search AI features do not require a secret “AI SEO” file or schema: apply published requirements and practices without inclusion promises. [AI guidance](https://developers.google.com/search/docs/appearance/ai-features).

### Navigation and pagination

Preserve `<a href>` links to subsequent pages. Load-more and infinite scrolling can enhance the experience, but need URLs and recoverable navigation. Do not use fragments as the only crawlable pagination mechanism or canonicalize every paginated page to the first. [Pagination guidance](https://developers.google.com/search/docs/specialty/ecommerce/pagination-and-incremental-page-loading).

In Blogger use context-provided `olderPageUrl`/`newerPageUrl`. Constructing page 2 by multiplying a post count can fail against service pagination and filters. Do not hide batch entries while keeping counters that promise something else.

Do not promise guaranteed SEO, AdSense approval, a performance score of 100 or a complete PWA merely by adding theme markup. These outcomes depend on platform settings, content, external services and measured behavior.

### Measurable performance

Core Web Vitals targets at the 75th percentile, separated by mobile/desktop: **LCP ≤2.5 s, INP ≤200 ms, CLS ≤0.1**. Lighthouse is a lab tool; TBT is not real INP, and an isolated score does not certify user experience. [Web Vitals](https://web.dev/articles/vitals).

The LCP image should be discovered early, have suitable dimensions, and normally avoid lazy loading. Use high priority only where appropriate; do not preload every photo. Evaluate download time, discovery delay, and rendering time separately. [LCP optimization](https://web.dev/articles/optimize-lcp).

Recommended application:

1. Measure homepage and article with real content, not only an empty demo.
2. Use responsive images and intrinsic dimensions; reserve space before loading.
3. Apply lazy loading to offscreen images/iframes, not indiscriminately.
4. Load optional functions when needed; control long tasks, observers, and scroll handlers.
5. Minimize font families/weights and dependencies; do not load an icon library for two symbols.
6. Reserve ad/embed space; test after enabling them, without hiding them during measurement.
7. Avoid one feed request per card and request chains for the first visible content.
8. Use explicit caching and expiration for auxiliary blocks without indefinitely hiding updates.

KB/request budgets are agreed and measured per project; they are not official Blogger limits. An optimization that breaks comments, Layout, or accessibility is a regression.

## Platform, API, feeds, and integrations

### Capabilities versus a custom backend

XML provides presentation and engine logic, not PHP plugin installation, SQL tables, Node execution, `.htaccess` control, or arbitrary HTTP header configuration. Private forms, payments, bookings, or application accounts require an external service or a platform with those capabilities. This is an architectural conclusion, not a numeric quota.

HTML/JS can integrate APIs, embeds, and tools, but browser restrictions still apply: origin, CORS, CSP, cookies, authentication, and availability. Do not put passwords or administrative tokens in the theme; content delivered to the browser is visible.

### Public API v3

The API exposes blogs, posts, pages, comments, users, and related resources; **it does not document a theme resource for uploading XML**. Do not invent `themes.update` or use private dashboard endpoints as a stable API. [v3 reference](https://developers.google.com/blogger/docs/3.0/reference).

Public data requires identification through an API key or OAuth; private data and mutations require appropriate authorization. An API key does not replace an OAuth token and should not be treated as a user password. Apply key restrictions and quotas according to the actual configuration. [Authorization](https://developers.google.com/blogger/docs/3.0/using).

`posts.list` exposes filters and token pagination; process `nextPageToken` rather than assuming one request returns everything. `fetchBodies`/partial fields can reduce payloads depending on the operation. Retry temporary failures with delays and a limit; do not loop forever on 403. [posts.list](https://developers.google.com/blogger/docs/3.0/reference/posts/list).

The API returns content JSON; this is not the same dictionary as `data:post`. Map properties instead of transplanting them. Preserve IDs as strings when passing them to JS.

### Feeds

Some implementations query `/feeds/posts/default?alt=json` and label variants. Feed behavior depends on settings, permissions, and observation; do not treat it as a replacement with the same contract as API v3. `alt=json-in-script` involves JSONP and code execution, so prefer `fetch` and JSON where supported. [Contemporary implementation](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml).

Recommendations for related posts, magazine blocks, and auxiliary search:

- Check the destination's actual feed: status, response type, full/summary content, unavailable feed, and navigation.
- Encode labels and query parameters; do not concatenate user input without `URL`/`URLSearchParams` or an engine helper.
- Show loading, empty, and error states; preserve a fallback link to ordinary label/search navigation.
- For listings, create nodes and use `textContent`; validate URLs and schemes before assigning them. Feed HTML is not automatically safe in every context.
- Limit queries and caching. Do not download the entire history to show three cards.
- Do not infer that `max-results=500` guarantees 500 entries. In this research a public feed requested with 500 returned 150 and required following `next`; this is evidence about that endpoint, not a global quota.

### Limits: confidence register

| Topic | Status at the cutoff | Decision |
|---|---|---|
| Blog members | Current help: maximum total of 100 authors, administrators, or readers | Separate from gadget counts |
| Content import | Help: unspecified daily file limit; no file-size restriction for that flow | Do not extrapolate to theme XML |
| Maximum theme size | No current official number confirmed | Test saving; do not claim 1 MB as a contract |
| Posts per page | Settings specify a desired maximum; observe actual output | Navigate using served URLs |
| API quotas | Project/service configuration | Inspect the console and actual errors |
| Complex expressions | Examples mention a token limit without a current contractual number | Split expressions/includes if they fail |
| Historical post, page, label, image, and rate limits | Conflicting older lists | Revalidate each requested number |

Sources: [permissions](https://support.google.com/blogger/answer/42673?hl=en), [import](https://support.google.com/blogger/answer/41387?hl=en), [settings](https://support.google.com/blogger/answer/9691230?hl=en), [expression-limit example](https://template-data.blogspot.com/p/sauce.html).

### Images and CDN

Use authorized media and verify each image response. Blogger may optimize uploads; do not promise original files or rewrite URL strings through a global regex. Hosts and formats may vary. Prefer `resizeImage`/`sourceSet` where type and origin allow them, with fallbacks for non-resizable sources. Test missing, small, portrait, external, and video-thumbnail images.

### Domain and HTTPS

Custom domains and HTTPS redirects are configured in Blogger/DNS, not XML. Use current dashboard instructions and values; do not hard-code someone else's verification CNAME. Blogger documents CAA for `letsencrypt.org` when those records are used. [Domain](https://support.google.com/blogger/answer/1233387?hl=en-uk), [HTTPS](https://support.google.com/blogger/answer/6284029?hl=en).

Inspect mixed content in themes, posts, and gadgets; change a scheme only if the resource exists over HTTPS. Do not destroy namespaces through global replacement. [Mixed content](https://support.google.com/blogger/answer/6286127?hl=en).

### PWA, AMP, and frameworks

A service worker needs a secure context, compatible-origin script, correct MIME type, and allowed scope; hosting it on an external CDN does not automatically control a blog. Blogger's theme editor does not provide an arbitrary-file server. A manifest alone does not solve this. A proxy or separate hosting could change the architecture, but needs its own design and tests. [Service worker registration](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register).

AMP is a separate implementation and validation target, not an attribute to add to a normal theme. Do not promise that ordinary ads, widgets, and JS work under its restrictions. React/Vue/Svelte can compile browser interfaces; evaluate accessibility, content without JS, real routes, and cost before replacing native rendering.

### Monetization, forms, and privacy

An ad slot does not guarantee network acceptance. Preserve owner IDs and load integrations only when in scope. For consent, consult current provider/territory requirements when the project needs them; a local flag is not a universal solution.

Test actual ContactForm or provider submission without real personal test data. Do not present `mailto:` as request storage or a local cart as commerce with payment/inventory/backend support. Preserve understandable error events and states without exposing tokens.
