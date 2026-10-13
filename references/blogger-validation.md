# Blogger Verification, Recovery and Recipes

This is the integrated Blogger knowledge base. Read the relevant section before changing platform behavior. These references are available to every stage; no separate expert skill is required. The research baseline remains September 23–24, 2026; recheck changing facts when they affect a decision.

## Contents

- [Validation, diagnosis, and installation](#validation-diagnosis-and-installation)

- [Syntax conformance pass](#syntax-conformance-pass)

- [Building a local preview](#building-a-local-preview)

- [Original contextualized recipes](#original-contextualized-recipes)

## Validation, diagnosis, and installation

### Evidence levels

| Level | Demonstrates | Does not demonstrate |
|---|---|---|
| Local XML | Well-formed document and checked structural rules | Valid expressions or available data |
| Saving in Blogger | Server acceptance of that theme version | Every view works |
| Real rendering | HTML, resources, and data for inspected views | No failures in untested cases |
| Interaction | Exercised functions | Performance for every user |
| Field data | User/operational metrics | A permanent guarantee |

Do not claim “production-ready” after only XML checks. When access is missing, deliver complete local work with an explicit limitation rather than pretending it was installed.

### Structural inspection without an installed analyzer

The [syntax conformance pass](#syntax-conformance-pass) is the list to work through; the paragraphs below explain the reasoning behind its structural items. Use an XML parser already available through the assistant's environment when possible. No helper script, Python, Node or package installation is required by this skill. A source review alone is not an executed parser check; record those separately. Never claim Blogger compilation from either one.

Check: one html root and one head/body; exact GML namespaces; one b:skin inside head for a layout theme; declared prefixes; balanced elements, attributes and CDATA; legal entities and comments; no unintended external DTD; unique alphanumeric section/widget IDs; sections contain only widgets without free text; widgets are direct section children and have nonempty types supported by evidence from the target theme; maxwidgets matches the actual count; custom widget includables include main; no duplicate includable IDs in one scope; real Blog rendering exists. Abbreviated native widgets require save/re-export expansion checks.

Required control attributes (or verified expr: variants): if/elseif cond; loop values and var; with value and var; eval expr; include name; includable id; switch var; case value; tag name; attr name and value; class name; message name; param name and value. Check else/elseif under if and case/default under switch as empty separators. Inspect literal `data:blog…`, `data:post…` and similar Blogger paths written in an attribute without expr: (a `data:image/…` address, as in the embedded favicon, is an ordinary URL and is correct as it is), mixed-content asset URLs, post HTML inserted into scripts, retired gadgets, and disabled native CSS/JS dependencies. For every inline script, read the raw text between its tags as an HTML parser would, not the XML-parsed text: CDATA markers must sit behind `//` comments on their own lines ([contexts](blogger-language.md#xml-and-escaping-contexts)), the code must parse as JavaScript with those lines present, selectors it animates must be checked for existence on views where they are absent, and no empty script remains. These checks preserve the former analyzer's decision logic as instructions; they do not replace a Blogger compiler.

### Structural boundaries and regression cases

The former expert skill's analyzer and its 15 regression tests are represented below as knowledge, without shipping or requiring the scripts. These are expected outcomes for an available structural checker; do not claim the cases ran unless they actually did. A source review cannot establish all widget types, resolve inherited includes, compile Blogger expressions, or validate generated HTML/CSS/JS. Review warnings by observable impact rather than turning each warning into a platform prohibition.

| Case from the original tests | Expected structural interpretation |
|---|---|
| Original laboratory with three abbreviated widgets | Structurally acceptable; record three widgets and require native expansion verification |
| Literal `A & B` in element text | XML parse error |
| `A &amp; B — café 😀 &#160;` | XML-valid text, entities and Unicode |
| Undeclared `&nbsp;` | XML parse error; use Unicode or a numeric reference |
| Duplicate section/widget ID | Structural error across their shared ID space |
| A paragraph directly inside a section | Structural error: only widgets belong there |
| Custom includables without `main` | Structural error; do not confuse this with an abbreviated native widget |
| `main` defined in two different widgets | Allowed: includable IDs are unique within their widget/defaultmarkup scope, not globally |
| An include inherited from Blogger with no local definition | Not a structural error by itself; resolve the actual native/inherited contract |
| JavaScript containing `<` and `&&` inside CDATA | Opaque XML text; still review JavaScript separately |
| GML namespace changed from HTTP to HTTPS | Wrong namespace identifier |
| `b:elseif value` without `cond` | Missing required condition attribute |
| Actual external DTD or declared entity | Unsupported by the former checker; avoid it in this workflow, without inventing a universal Blogger prohibition |
| Text resembling an entity declaration inside an XML comment | Harmless opaque comment text, not a real declaration; likewise recognize CDATA before flagging DTD-looking text |
| A syntactically invalid Blogger expression in otherwise valid XML | May pass structural checks; only Blogger compilation checks expression acceptance |

Also check that head and body are direct children of the html root, the XHTML namespace is intentional, `b:skin` belongs directly in head, and `maxwidgets` is a nonnegative integer before comparing its count. Preserve the distinction between an unreadable/invalid-encoding file, malformed XML, structural errors and review warnings. Accept an existing UTF-8 BOM when reading; deliver valid UTF-8. Do not auto-serialize the entire XML merely to fix a local finding: that can change CDATA, prefixes and settings. Record section/widget counts, widget types and layout/default-widget versions when useful for a migration, alongside the explicit fact that structural validation does not verify Blogger acceptance.

### Installation and recovery workflow

1. Save the current theme export and capture relevant gadget settings. Back up content separately when warranted by the intervention.
2. Keep any change diff and working backup in private scratch; preserve user-owned original XML in place. Record family/versions; do not rely solely on the file date for identification.
3. Test first in an authorized test blog. Do not import content into production merely for testing convenience.
4. In Theme, use the available restore/upload menu or Edit HTML for appropriate changes. Upload the descriptively named theme XML, not a content backup or an archive.
5. Save, inspect errors, and re-export to observe engine normalization/expansion.
6. Verify Layout, Customize, public views, and functions. An administrator session can conceal reader problems.
7. Apply to the destination when within the user's authorization. Verify again after the change.
8. For a material regression, restore the previous copy and verify gadgets/settings. Do not assume older XML restores every external or content change.

Exact controls may vary; inspect the current dashboard. [Design](https://support.google.com/blogger/answer/176245?hl=en), [backup](https://support.google.com/blogger/answer/41387?hl=en).

### View matrix

| Case | Check |
|---|---|
| Empty homepage and homepage with posts | Empty state, order, initial content, navigation |
| Long post | Complete body, headings, tables, images, embeds, comments |
| Post without photo/title/labels | Fallbacks and structure |
| Static page | Its own design and metadata |
| Label containing accents | URL, encoding, title |
| Search with results and empty search | Visible query, escaping, navigation |
| Date archive | Range and pagination |
| Last listing page | No nonexistent navigation links |
| Missing URL | Observed HTTP response and useful content |
| `?m=1`, normal URL, custom domain | Same essential content, URLs, resources |
| Preview and Layout | Editing controls, dependencies, sections |

Cross-cutting tests: keyboard, focus, zoom, 320/375/768/1024/1280 px with the [overflow check](responsive.md#checking), broken images, disabled JS, cold cache, failed external scripts, anonymous session, open/closed comments, and light/dark themes if present. On a saved theme, also compare spacing and line height against the local mockup: Blogger's own widget stylesheet is absent from a local preview ([its rules](responsive.md#bloggers-own-stylesheet)).

### Diagnosis by symptom

| Symptom | Check first | Avoid |
|---|---|---|
| XML does not parse | Line/column, `&`, quotes, closing tags, CDATA | Blind global replacement |
| Unbound prefix | Root namespaces and exact URIs | Changing URIs to HTTPS |
| Invalid widget/section | ID/type/location, duplicates | Deleting all gadgets |
| Unknown include | Scope, version, name, defaultmarkup | Inventing an empty definition |
| Empty datum | View, received var, type, version | Blaming the XML parser |
| Literal `data:…` displayed | Missing `expr:` or gadget content context | Adding JS to hide it |
| No posts appear | Blog/main, condition, feed/settings | Importing duplicate content |
| Fewer posts than expected | Settings, jump breaks, actual batch | Fabricating numeric pagination |
| Unstyled homepage | `b:skin`, variable substitution, final CSS | Changing the whole family |
| Customize does not work | Variables, groups, syntax, values | Confusing it with browser CSS |
| Comments/contact fail | Scripts, IDs, settings, browser | Only styling the form |
| HTTP resources blocked | Generating theme, post, or gadget | Changing namespaces |
| Invalid JSON-LD | Final text, quotes, missing values, script closing | Testing only XML source |
| Displaced layout | Generated widgets, widths, tables, ads | Global `overflow:hidden` |
| Duplicates after migration | Retained IDs and Layout settings | Indiscriminate cleanup template |

### Behavioral tests

Create test data with quotes, ampersands, accents, emoji, long strings, and valid editorial HTML. Keep essential content and links usable when JS or external services fail. Do not test forms using other people's personal email addresses or generate ad clicks.

Inspect HTTP source as well as the post-JS DOM: differences reveal late metadata, duplication, missing content, or automatic browser repairs. Inspect console and network per view. Prioritize warnings by observable effect rather than count.

### Theme delivery

Deliver exactly one descriptively named UTF-8 XML file. Inline required styles, browser code, SVG/icon data and license notices where appropriate; external verified CDN references are allowed. Keep design.md, notes, previews, evidence and intermediate assets in temporary scratch or conversation, never in a delivered output folder. State installation/recovery steps and tests in the final chat response. Do not call the XML a complete content backup or claim it is installed without evidence. Clean only temporary files created by this run; preserve user inputs and unrelated work.

## Syntax conformance pass

The gate between writing a theme and checking how it looks. It asks one question: is every line written the way Blogger's own contract says it must be? It is run twice on every theme, new or edited: by whoever writes the XML, as soon as it is written, and again in verification by reading the file fresh. A theme does not move on while any item fails.

Each item names the reference that holds the rule. Read the whole of [Blogger language](blogger-language.md), [Blogger design](blogger-design.md), [the theme skeleton](blogger-theme.md), [responsive layout](responsive.md) and this file before writing; this list is the test of that reading, not a substitute for it. Answer every item pass, fail or not applicable, from the file itself, never from memory of having written it.

**The document** ([XML and escaping](blogger-language.md#xml-and-escaping-contexts))

1. The file parses as XML: one root, every element closed, every attribute quoted, every boolean attribute given a value.
2. The root is `html` with `b:defaultwidgetversion='2'`, `b:layoutsVersion='3'`, `b:responsive='true'`, the XHTML namespace and the three Blogger namespaces written exactly, with `http`, not `https`.
3. `html` has exactly two children, `head` then `body`.
4. Only the five XML entities are used (`&amp;`, `&lt;`, `&gt;`, `&quot;`, `&apos;`); anything else is a numeric reference or the character itself. No comment contains `--`.
5. Every `&` in an address or in text is `&amp;`; every `<` inside an attribute is `&lt;`.

**The head** ([theme skeleton](blogger-theme.md#the-skeleton))

6. `<b:include data='blog' name='all-head-content'/>` is present and nothing removes or replaces what it prints.
7. There is one `title` element and one viewport `meta` that does not block zoom.
8. There is exactly one `b:skin`, directly in `head`, with its CSS inside `CDATA`.
9. Every script element has a separate closing tag; none is self-closing.

**Sections and gadgets** ([structural contract](blogger-language.md#structural-contract))

10. Every section and gadget id is letters and numbers only and is used once in the whole file.
11. Every section has a `class`; `showaddelement` is `yes` or `no`; `maxwidgets` is a number not smaller than the gadgets it holds.
12. A section contains gadgets and nothing else: no text, no HTML, no Blogger tag.
13. No section sits inside a Blogger tag (`b:if`, `b:loop`, `b:tag`…) or inside a list, table, paragraph, link, heading or button. A section shown on some views only uses its own `cond`, with `or data:view.isLayoutMode`.
14. Every gadget is a direct child of a section, has a `type`, an id of that type plus a number, `locked` as `true` or `false`, and `version='2'`.
15. There is one Blog gadget and its `main` prints full post bodies, Blogger's own post addresses, the pager addresses and the comments include.
16. A gadget that defines includables defines `main`; no includable id repeats within a gadget; includable ids and `var` names are letters and numbers only; none takes the name of a [native includable](blogger-theme.md#native-blog-includables) except `main`.
17. `b:widget-settings`, when present, is the first thing inside its gadget.
18. Every `b:include` names an includable defined in the same gadget or a native one of that gadget type. `all-head-content` is the only include outside a gadget.

**Tags and expressions** ([template language](blogger-language.md#template-language))

19. Required attributes are present: `b:if` and `b:elseif` have `cond`; `b:loop` has `values` and `var`; `b:with` has `var` and `value`; `b:eval` has `expr`; `b:switch` has `var`; `b:case` has `value`; `b:include` has `name`.
20. `b:else` and `b:elseif` are empty, self-closing and directly inside a `b:if`; `b:case` and `b:default` likewise inside a `b:switch`. Every `b:if` has its closing tag.
21. Anything computed in an attribute uses `expr:`. No attribute holds a bare `data:blog…`, `data:post…` or `data:view…` path without it. A literal `data:image/…` address is not a data path.
22. Expressions use only Blogger's operators: `==`, `!=`, `<`, `>`, `<=`, `>=`, `and`, `or`, `not`, `in`, `contains`, `? :`, `+`, `-`, `[n]`, and the helpers the skeleton reference lists: `format`, `snippet`, `resizeImage`, `sourceSet`. No `===`, `&&`, `||`, `?.`, `??`, no JavaScript method or keyword.
23. Strings inside an expression are in the other kind of quote from the attribute's own.
24. Data is read where it exists: `data:post` and the pager addresses only inside the Blog gadget; a gadget's own fields only inside that gadget; `data:blog` and `data:view` anywhere.
25. Only data paths listed in the [skeleton reference](blogger-theme.md#blog-gadget) or in [Blogger language](blogger-language.md#data-types-and-views) are used, and version-2 names are not mixed with version-1 names (`numComments`, `timestamp`, `firstImageUrl`).
26. No post address is built from a title, no list of posts is filtered to imitate a category, and no page number is calculated: Blogger's own addresses are used. A link the owner will edit inside a gadget's content, such as a welcome button to a label, is the exception: no data path works there, so it is typed as a root-relative address (`/search/label/Name`) with a label the user actually named.

**Skin and script** ([owner-editable CSS](blogger-design.md#owner-editable-css), [the kit](effects.md#the-kit))

27. Designer variables, when defined, sit inside a CSS comment, each with `name`, `description`, `type` and `default`; every `$name` in the CSS is defined; there is no other `$` anywhere in the skin.
28. The first CSS rules of the skin, after its title comment and any variable definitions, are the block that cancels Blogger's own stylesheet and hides the Attribution gadget.
29. No Blogger tag or data tag appears inside the skin or inside a script: they are not evaluated there. Values reach script through `expr:data-…` attributes.
30. Every inline script is wrapped in comment-guarded CDATA, parses as JavaScript with the guard lines present, and is not empty.
31. Every CDN script is version-pinned, carries `integrity` and `crossorigin`, and its address answers.

**Blogger behavior** ([sections, widgets and includes](blogger-language.md#sections-widgets-and-includes), [SEO](blogger-design.md#seo-metadata-and-performance))

32. Comments are Blogger's own, through the comments include; no hand-made comment form.
33. Listing pages link to older and newer posts with real links from `olderPageUrl` and `newerPageUrl`.
34. Text the owner will change lives in gadgets, not in the template; nothing asks the owner to edit XML or script to change a menu item or a label.
35. No password, key or private address is in the file; no form pretends to send anything without a real service behind it.
36. A theme title comment is present; in the template the blog's name always comes from `data:blog.title`, never typed in. A gadget's starting text is the owner's own content and may say what they said in the interview.

Record the result as “conformance pass: 36 checked, N not applicable, 0 failed”, and list any item that could only be judged by reading, not by a tool. This pass shows the theme follows the documented contract. It does not show that Blogger saved it.

## Building a local preview

A theme XML is not a page: a browser cannot open it. To check layout, responsiveness and effects before the owner saves the theme, build one plain HTML file per view from the XML and open those. This is a simulation of Blogger's output, useful for catching layout and script defects; it never shows that Blogger accepts the theme. Say “local simulation” wherever its results are reported.

Build the files in private scratch, by hand for a small theme or with a short script written for the run. Nothing from this step is delivered.

**What each file contains, in this order**

1. The theme's `head` content: meta tags, the favicon link, font links, CDN script tags. Leave `all-head-content` out.
2. Blogger's own stylesheet, as a `style` element, before the skin:

```css
html { height: 100%; }
body { min-height: 100%; position: relative; margin: 0; padding: 0 0 1px; overflow-wrap: break-word; word-break: break-word; }
.section { margin: 0 15px; }
.widget { margin: 30px 0; line-height: 1.4; }
.section:first-child .widget:first-child { margin-top: 0; }
.section:last-child .widget:last-child { margin-bottom: 0; }
h1, h2, h3, h4 { margin: 0; position: relative; }
a img { border: none; position: relative; }
.widget ul { padding: 0 0 0 1.25em; margin: 0; line-height: 1.2; }
.widget li { padding: .25em 0; margin: 0; text-indent: 0; }
.widget .post-body ul { padding: 0 2.5em; margin: .5em 0; line-height: 1.4; }
.widget .post-body li { margin-bottom: .25em; padding-top: 0; padding-bottom: 0; }
.post-body { width: 100%; }
#blog-pager { margin: 1em 0; text-align: center; overflow: hidden; }
.comments { clear: both; margin-top: 10px; margin-bottom: 0; }
.comments .comments-content .comment-thread ol { list-style-type: none; padding: 0; text-align: left; }
.comments .avatar-image-container { float: left; max-height: 36px; overflow: hidden; width: 36px; }
.comments .comment-block { margin-left: 48px; position: relative; }
.comments .comments-replybox { border: none; height: 250px; width: 100%; }
```

3. The skin's CSS in a second `style` element, with every `$name` or `$(name)` replaced by that variable's value.
4. Any head script exactly as written, including its `//<![CDATA[` and `//]]>` lines.
5. The body, with Blogger's tags worked out for this view:
   - each `b:section` becomes `<div class='ITS-CLASS section' id='THE-ID'>`, and is left out when its `cond` is false for the view;
   - each `b:widget` becomes `<div class='widget TYPE' data-version='2' id='THE-ID'>` around what its `main` prints;
   - `b:if`, `b:loop` and `b:include` are followed by hand with the sample data below;
   - `<data:…/>` and `expr:` attributes are replaced with the sample values;
   - the comments include is replaced by the stand-in markup below.
6. The body script as raw text, with its CDATA marker lines.

**Views to build.** First the two that carry most of the risk: the homepage and a post. Check those at 375 and 1280 px before anything else, so that a run that is cut short still has evidence for the views readers see most. Then the rest: a static page, a label listing, a search with no results and the error view, and the remaining widths. Whatever was not built is reported as not run; never describe a view that was not opened.

**Sample data.** Use content that tries to break the layout, not content that flatters it:

- Seven posts on the homepage, including a title of about ninety characters, a post with no image, a post with no title, a title with accents, an ampersand and quotation marks, and a post with four labels.
- A label whose name has an accent and a space.
- For the post view, this body:

```xml
<p>Opening paragraph with a <a href='#'>link</a> and enough words to wrap over several lines on a phone.</p>
<h2>A heading inside the post</h2>
<h3>A smaller heading under it</h3>
<p>Supercalifragilisticexpialidocious_and_an_unbroken_url_https://example.org/a/very/long/path/that/never/ends/and/never/wraps/on/small/screens.html</p>
<div class='separator' style='clear: both; text-align: center;'><a href='#' style='margin-left: 1em; margin-right: 1em;'><img border='0' width='1600' height='1000' src='REPLACE-WITH-ANY-LARGE-TEST-IMAGE'/></a></div>
<table border='1'><tr><th>Column one heading</th><th>Column two heading</th><th>Column three heading</th><th>Column four heading</th><th>Column five heading</th><th>Column six heading</th></tr><tr><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td></tr></table>
<pre>const aVeryLongLineOfCode = someFunction(withMany, arguments, thatDoNotWrap, atAll, andKeepGoing, forever);</pre>
<ul><li>List item one</li><li>List item two with more words</li></ul>
<blockquote>A quotation that should look deliberate.</blockquote>
<iframe width='560' height='315' src='about:blank' title='Embedded video'></iframe>
<h2>A second section</h2>
<p>Closing paragraph.</p>
```

- For comments, this stand-in, which follows the shape of Blogger's own thread and form:

```xml
<section class='comments threaded' id='comments'>
  <h3 class='title'>1 comment</h3>
  <div class='comments-content'><div class='comment-thread'><ol>
    <li class='comment'><div class='avatar-image-container'></div><div class='comment-block'>
      <div class='comment-header'><cite class='user'>A reader</cite> <span class='datetime secondary-text'>May 3, 2026</span></div>
      <p class='comment-content'>A sample comment long enough to wrap onto a second line on a phone.</p>
      <span class='comment-actions secondary-text'><a class='comment-reply'>Reply</a></span>
    </div></li>
  </ol></div></div>
  <div class='comment-footer'><div class='comment-form'><h4 id='comment-post-message'>Post a Comment</h4>
    <iframe class='blogger-iframe-colorize blogger-comment-from-post' frameborder='0' height='410px' id='comment-editor' src='about:blank' width='100%'></iframe>
  </div></div>
</section>
```

**States to produce**

| State | How |
|---|---|
| Each width | Resize the browser, or load the preview in `iframe` elements 320, 375, 768, 1024 and 1280 px wide |
| JavaScript off | The browser's own setting, or a copy of the file with its `script` elements removed |
| Reduced motion | The browser's rendering emulation for `prefers-reduced-motion`; only if that is unavailable, a copy whose first script overrides `matchMedia` |
| CDN blocked | Block the host in the browser, or change the script address to one that fails at once |
| CDN stalled | Point the script at a local address that accepts the connection and never answers; the page must still show everything on the screen within three seconds. Entrances further down still wait for the reader to scroll to them, as they do on a normal load |
| Fonts blocked or stalled | The same two tests on the font stylesheet address; the page must draw at once in its fallback fonts |
| 200% zoom | A 640 px wide view of the desktop layout |

A recipe that requests data, such as related posts, cannot finish from a file opened directly: the browser treats a local file as a different site. Serve the preview folder from a local web server and put a stand-in answer at the address the script asks for, here `/feeds/posts/summary/-/LABEL`:

```js
/* alt: feed-sample */
({ "feed": { "entry": [
  { "title": { "$t": "A <b>bold</b> & risky title" }, "published": { "$t": "2026-05-02T09:00:00Z" }, "link": [{ "rel": "alternate", "href": "http://localhost:8000/another-post.html" }] },
  { "title": { "$t": "A second story" }, "published": { "$t": "2026-04-11T09:00:00Z" }, "link": [{ "rel": "alternate", "href": "http://localhost:8000/second-post.html" }] },
  { "title": { "$t": "A link that must be dropped" }, "link": [{ "rel": "alternate", "href": "javascript:alert(1)" }] }
] } })
```

Save what is between the outer parentheses as the file; use the port the server really has. Test the answer arriving, an empty `entry` list, a missing file and a server that never answers.

Keep the preview tab in front while checking effects: a background tab stops drawing frames and entrances look stuck when nothing is wrong. If the browser tool reports the page as hidden, or screenshots do not change after an interaction, bring the tab to the front and repeat before judging the effect; a result taken from a hidden tab is not a result. When the tab cannot be brought to the front, use a browser window of your own, such as a headless one, or record the effect checks as not run.

What a preview cannot show: whether Blogger accepts the expressions, Blogger's real comment thread and form, generated gadget markup the theme does not define itself, and the Layout and Customize screens. Record those as not run.

## Original contextualized recipes

These recipes are patterns to integrate and test, not certified complete themes. Modern data needs a compatible base. Blog snippets belong inside its includables and loops; do not paste them indiscriminately into head or HTML gadgets. Local structural checks do not compile Blogger expressions or certify these recipes.

### 1. Card includable

Within the same Blog widget, integrate into its existing `main` without removing pagination or comments. In a new theme that `main` is the one in the [theme skeleton](blogger-theme.md#the-skeleton):

```xml
<b:loop values='data:posts' var='entry' index='index'>
  <b:include name='editorialCard' data='entry'/>
</b:loop>
```

As a sibling of `main`:

```xml
<b:includable id='editorialCard' var='p'>
  <article class='bt-card'>
    <h2>
      <a expr:href='data:p.url'>
        <b:if cond='data:p.title'><data:p.title/><b:else/>Untitled post</b:if>
      </a>
    </h2>
    <b:if cond='data:p.labels'>
      <ul class='bt-labels'>
        <b:loop values='data:p.labels' var='label'>
          <li><a expr:href='data:label.url'><data:label.name/></a></li>
        </b:loop>
      </ul>
    </b:if>
  </article>
</b:includable>
```

Do not invent a URL from `title`. Check whether titles support markup and what escaping that design requires. A card does not replace a full post rendering.

### 2. Modern view branches

```xml
<b:if cond='data:view.isHomepage'>
  <p class='bt-intro'>Latest posts</p>
<b:elseif cond='data:view.isPost'/>
  <p class='bt-context'>Article</p>
<b:elseif cond='data:view.isPage'/>
  <p class='bt-context'>Information</p>
<b:elseif cond='data:view.isSearch and data:view.search.label'/>
  <h1>Label: <data:view.search.label/></h1>
</b:if>
```

This is a contextual heading, not a replacement for Blog. Avoid duplicating `h1` with an already rendered title. `isSearch` includes labels; `isMultipleItems` does not count results.

### 3. Alias and excerpt

In a modern `data:post` context supporting helpers:

```xml
<b:with var='excerpt'
        value='snippet(data:post.body, {length: 170, links: false, linebreaks: false, ellipsis: true})'>
  <p class='bt-excerpt'><data:excerpt/></p>
</b:with>
```

Check resulting HTML: an excerpt is neither a reliable article copy nor a perfect editorial description. Do not cut HTML with `substring` and expect balanced tags.

### 4. Pagination with native fallback

Inside Blog, using its navigation data:

```xml
<nav aria-label='Page navigation' class='bt-pagination'>
  <b:if cond='data:newerPageUrl'>
    <a expr:href='data:newerPageUrl' rel='prev'>Newer posts</a>
  </b:if>
  <a expr:href='data:blog.homepageUrl'>Home</a>
  <b:if cond='data:olderPageUrl'>
    <a expr:href='data:olderPageUrl' rel='next'>Older posts</a>
  </b:if>
</nav>
```

`rel` does not replace correct URLs or guarantee ranking benefits. Check first/last pages, archives, filters, and empty batches.

### 5. Search without a JS dependency

In a base exposing `data:blog.searchUrl`:

```xml
<form expr:action='data:blog.searchUrl' method='get' role='search'>
  <label for='btSearch'>Search this blog</label>
  <input id='btSearch' name='q' type='search'/>
  <button type='submit'>Search</button>
</form>
```

Generate unique IDs if reused in multiple places. Do not place Blogger tags inside gadget content assuming compilation: use the theme's XML editor or an authorized literal URL in the gadget.

### 6. Passing text to JavaScript

```xml
<div expr:data-blog-title='data:blog.title' id='btConfig'></div>
<script type='text/javascript'>
//<![CDATA[
(() => {
  const config = document.getElementById('btConfig');
  if (!config) return;
  const title = config.dataset.blogTitle || '';
  // Use title as data; do not interpolate it into code or innerHTML.
})();
//]]>
</script>
```

For external scripts, use `defer` and a clear initialization contract. Test titles containing quotes, special characters, and apparent tags.

### 7. Conditional wrapper

```xml
<b:tag name='a' cond='not data:view.isHomepage'
       expr:href='data:blog.homepageUrl' class='bt-brand'>
  <data:blog.title/>
</b:tag>
```

On the homepage the title remains without a link. Use `b:if` to hide everything. To add classes or attributes to the parent:

```xml
<div class='bt-view'>
  <b:class name='bt-view-home' cond='data:view.isHomepage'/>
  <b:attr name='data-view-title' expr:value='data:view.title'/>
</div>
```

Verify the `value`/`expr:value` variant supported by the base. Using `expr:data-view-title` directly on the element is often simpler.

### 8. Modern responsive image

In a context confirming `featuredImage` and `isResizable`:

```xml
<b:if cond='data:post.featuredImage'>
  <b:if cond='data:post.featuredImage.isResizable'>
    <img alt='' class='bt-card-image'
         expr:src='resizeImage(data:post.featuredImage, 640, "16:9")'
         expr:srcset='sourceSet(data:post.featuredImage, [320, 640], "16:9")'
         sizes='(min-width: 64rem) 32rem, 100vw'
         width='640' height='360' loading='lazy'/>
  <b:else/>
    <img alt='' class='bt-card-image' expr:src='data:post.featuredImage' loading='lazy'/>
  </b:if>
</b:if>
```

This pattern is for a decorative offscreen card image with a sufficiently large original; do not copy `lazy` to the LCP image. Limit the source set if the original is smaller than a descriptor. HTML dimensions must match the delivered ratio. `sizes` must describe the actual layout. The external fallback needs known dimensions or CSS-reserved space.

If the base provides `responsiveImage`, inspect its contract and reuse it. [Author-provided snippet](https://template-data.blogspot.com/p/snippets.html).

### 9. Special label without requesting the whole blog

```xml
<b:if cond='data:post.labels any (label => label.name == "Guide")'>
  <span class='bt-badge'>Practical guide</span>
</b:if>
```

This evaluates a post's labels. To retrieve all guides, link to the label archive or build an independently queried block. Filtering only `data:posts` does not retrieve the entire archive.

### 10. Switch selection

```xml
<b:switch var='data:blog.pageType'>
  <b:case value='item'/><span>Post</span>
  <b:case value='static_page'/><span>Page</span>
  <b:default/><span>Posts</span>
</b:switch>
```

This example uses the historical classification; it does not identify the homepage exclusively. Do not mix it with `view.type` without checking its values.

### 11. Separate design from production data

For local design work, create a temporary HTML mockup with representative cases. Once the design decisions are settled, move components into XML and connect real data. Label the mockup as a simulation: a static server does not evaluate `b:if`, inheritance, or widget settings. Never present a local screenshot as evidence of Blogger installation.

## Laboratory starting point

This is the original minimal structural example, preserved in Markdown rather than as a shipped XML asset. It is not a completed theme, and it is no longer the starting point for a new one: start from the complete [theme skeleton](blogger-theme.md#the-skeleton), which has every view, a written-out Blog gadget and the responsive base. This example stays as the smallest structure that the checklist accepts. Use a real native/user export when available; do not silently substitute it for a requested preservation job. The native abbreviated widgets must be expanded and tested in Blogger. Implement the interview's design, branding defaults and functional coverage before delivery. No Blogger attribution gadget is included.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html b:defaultwidgetversion='2' b:layoutsVersion='3' b:responsive='true'
      expr:dir='data:blog.languageDirection' expr:lang='data:blog.locale'
      xmlns='http://www.w3.org/1999/xhtml'
      xmlns:b='http://www.google.com/2005/gml/b'
      xmlns:data='http://www.google.com/2005/gml/data'
      xmlns:expr='http://www.google.com/2005/gml/expr'>
  <head>
    <meta charset='UTF-8'/>
    <meta content='width=device-width, initial-scale=1' name='viewport'/>
    <b:include data='blog' name='all-head-content'/>
    <title><data:blog.pageTitle/></title>
    <b:skin><![CDATA[
/* Original laboratory. Verify saving and widgets in Blogger before use.
<Variable name="accent" description="Link color" type="color"
          default="#1859a9" value="#1859a9"/>
*/
:root { --bt-accent: $accent; }
* { box-sizing: border-box; }
body { margin: 0; color: #17202a; background: #fff; font: 1.125rem/1.65 system-ui, sans-serif; }
a { color: var(--bt-accent); }
img { max-width: 100%; height: auto; }
.bt-shell { max-width: 76rem; margin-inline: auto; padding: 1.25rem; }
.bt-layout { display: grid; gap: 2rem; }
.bt-layout > * { min-width: 0; }
.post-body { overflow-wrap: anywhere; }
.post-body pre { overflow: auto; }
.widget { margin-block-end: 1.5rem; }
.bt-skip { position: absolute; inset-inline-start: 1rem; top: -10rem; background: #fff; padding: .75rem; z-index: 100; }
.bt-skip:focus { top: 1rem; }
:focus-visible { outline: 3px solid var(--bt-accent); outline-offset: 3px; }
@media (min-width: 64rem) { .bt-layout { grid-template-columns: minmax(0, 1fr) 19rem; } }
    ]]></b:skin>
  </head>
  <body>
    <!-- Laboratory base; abbreviated widgets require expansion in Blogger. -->
    <a class='bt-skip' href='#btMain'>Skip to content</a>
    <header class='bt-shell'>
      <b:section class='header' id='header' maxwidgets='1' showaddelement='no'>
        <b:widget id='Header1' locked='true' title='Header' type='Header' version='2'/>
      </b:section>
    </header>
    <div class='bt-shell bt-layout'>
      <main id='btMain' tabindex='-1'>
        <b:section class='main' id='main' maxwidgets='1' showaddelement='no'>
          <b:widget id='Blog1' locked='true' title='Posts' type='Blog' version='2'/>
        </b:section>
      </main>
      <aside aria-label='Additional information'>
        <b:section class='sidebar' id='sidebar' showaddelement='yes'>
          <b:widget id='Label1' locked='false' title='Labels' type='Label' version='2'/>
        </b:section>
      </aside>
    </div>
    <footer class='bt-shell'>
      <b:section class='footer' id='footer' showaddelement='yes'/>
    </footer>
  </body>
</html>
```
