# Blogger XML, Language, Data and Widgets

This is the integrated Blogger knowledge base. Read the relevant section before changing platform behavior. These references are available to every stage; no separate expert skill is required. The research baseline remains September 23–24, 2026; recheck changing facts when they affect a decision.

## Contents

- [Formats, languages, and architecture](#formats-languages-and-architecture)

- [XML and escaping contexts](#xml-and-escaping-contexts)

- [Template language](#template-language)

- [Data, types, and views](#data-types-and-views)

- [Sections, widgets, and includes](#sections-widgets-and-includes)

## Formats, languages, and architecture

| Artifact | Purpose | Destination |
|---|---|---|
| Theme `.xml` | Blogger presentation | Theme → restore/upload or edit HTML |
| Content backup XML / `feed.atom` | Posts, pages, and comments | Settings → import content |
| Post template | Initial editor text/HTML | Settings → posts |
| HTML/JavaScript gadget | One gadget instance's content | Layout → gadget |
| Rendered HTML | Public output | Inspection; not a replacement for source |
| Internal working notes | Temporary planning and checks | Private scratch or conversation; never a final attachment |

A post template does not replace the site-wide theme. [Editor](https://support.google.com/blogger/answer/154172?hl=en). Content and theme backups are managed separately. [Backup](https://support.google.com/blogger/answer/41387?hl=en).

```text
internal plan + selected browser resources → named-theme.xml
named-theme.xml + data and settings → Blogger engine → HTML/CSS/JS
HTML/CSS/JS → browser → interface
```

| Technology | Role |
|---|---|
| XML + namespaces | Container parsed by Blogger |
| `b:`, `data:`, `expr:` | Engine control, data, and expressions |
| HTML / CSS / JavaScript | Semantics / appearance / interaction |
| JSON, JSON-LD | Integrations and metadata; not the theme format |
| SVG | Inline vector with its namespace, or an external resource |
| TypeScript, Sass, Tailwind, JSX | Not directly usable in XML; choose a browser-ready original or adapt selected behavior to plain browser code |
| Assistant tools already available | Optional inspection and validation; no installed toolchain is required by this skill |
| PHP, SQL, server-side Python | Not XML runtimes |

Writing PHP or uploading `package.json` does not provision a backend. An external service can provide processing with its own hosting and authentication; this distinction follows from the architecture.

### Families

**Classic:** recognize `$BlogTitle$` and other historical constructs. Treat changes as migration work; do not accidentally mix them with modern layouts.

**Older layouts:** inspect v1 widgets and data such as a string author or `numComments`. **Modern layouts:** often use `b:layoutsVersion='3'`, `b:defaultwidgetversion='2'`, and widgets with `version='2'`. **Dynamic Views:** have their own behavior/dependencies; do not assume interchangeability with ordinary layouts.

`b:version` is not API v3. `b:templateVersion` generally describes the theme, not the language. `b:responsive` does not create media queries. Changing `b:css`/`b:js` can affect native functions. Observed comparison: [Pixel](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml), [JetTheme](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml).

### Migration and native-dependency safeguards

Treat `b:version`, `b:layoutsVersion`, `b:defaultwidgetversion` and each `b:widget/@version` as separate controls. Changing a number is not a migration; inspect the corresponding markup, data and native behavior.

Preserve `all-head-content` and native dependencies unless deliberately replacing their functions and testing every affected view. Never use fake closing tags or comment tricks to suppress Blogger-injected CSS or scripts. A stripped-down `Blog1` is not equivalent to the native widget; use the functional equivalence matrix below before replacing it.

### Recommended decisions

Divide the project into head, CSS tokens, components, editable sections, Blog, and JS enhancements. Keep any intermediate work in disposable scratch; only the named installable XML is delivered. A generic HTML minifier can damage quotes, namespaces, or expressions.

Use gadgets to configure links, the logo, and blocks the owner changes frequently. Use designer variables for compatible visual options. Do not require editing JS to rename a category.

For patches: minimal diffs, without regenerating unrelated widgets. For new themes: define the view contract and initially preserve native Blog. To convert HTML: map static content to real data, widgets, and empty states; changing the extension does not convert the site.

## XML and escaping contexts

XML requires balanced elements, quoted attributes, one root, and declared prefixes. CDATA is text: it cannot nest or contain `]]>`. The predefined entities are `amp`, `lt`, `gt`, `quot`, and `apos`; use Unicode or numeric references for others. XML comments cannot contain `--`. [XML 1.0](https://www.w3.org/TR/xml/).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:b="http://www.google.com/2005/gml/b"
      xmlns:data="http://www.google.com/2005/gml/data"
      xmlns:expr="http://www.google.com/2005/gml/expr">
  <!-- theme content -->
</html>
```

Namespace URIs are identifiers: do not convert them to HTTPS while fixing mixed content. Check actual resource URLs instead. Do not introduce a remote DTD to support `&nbsp;`.

| Context | Pattern | Failure |
|---|---|---|
| Literal XML URL | `href='/search?q=xml&amp;max-results=8'` | Unescaped `&` |
| Dynamic attribute | `expr:href='data:post.url'` | `href='data:post.url'` emits a literal |
| Comparison | `cond='data:i &lt; 3'` | `<` inside an attribute |
| HTML boolean | `defer='defer'` | Attribute without a value |
| JSON | JSON serialization / verified `.jsonEscaped` | Confusing HTML and JSON |
| Static JS | Comment-guarded CDATA (`//<![CDATA[` … `//]]>`) or an external file | XML interprets `&&`; a bare `<![CDATA[` is a JavaScript syntax error in the served HTML |
| Editorial HTML | Intentional post body | Escaping it and displaying tags |

There is no universal escape: HTML, JS, CSS, and URLs are different contexts. [XSS](https://developer.mozilla.org/en-US/docs/Web/Security/Attacks/XSS).

```xml
<b:skin><![CDATA[
  :root { --reading-width: 70ch; }
  .post-body { max-inline-size: var(--reading-width); }
]]></b:skin>
<script type='text/javascript'>
//<![CDATA[
document.documentElement.classList.add('js');
//]]>
</script>
```

Always put each CDATA marker of an inline script on its own line behind a JavaScript comment, exactly as above. The public page is parsed as HTML, where `<script>` content is raw text and a CDATA marker has no special meaning: if the markers reach the browser, `<script><![CDATA[code]]></script>` fails with a syntax error and the whole block is discarded, while the commented form is valid whether or not the markers are emitted. On October 12, 2026 a public Blogger blog was observed serving its inline scripts with the `//<![CDATA[` lines intact in the HTML, which confirms the markers do reach the browser. A local XML parser strips CDATA and hides this failure, so a preview built by parsing the XML proves nothing about it. For a literal `<style>` outside `b:skin`, guard with `/*<![CDATA[*/` and `/*]]>*/`. Never leave an empty `<script>` in a finished theme.

`<data:blog.title/>` inside CDATA is not evaluated as a tag. Pass values to JS through `expr:data-*` and read them with `dataset`, avoiding concatenation into executable code.

Prefer `<script src='…'></script>` to a self-closing script: XML and HTML have different rules. `defer` serves classic external scripts; `async` changes execution order; modules behave differently. [Script element](https://developer.mozilla.org/en-US/docs/Web/HTML/Reference/Elements/script).

### JSON-LD

Check native output first. Use `JSON.parse(script.textContent)` to verify the JSON actually served. `.jsonEscaped` handles strings, but also check HTML embedding, especially `</script>`. Valid JSON does not guarantee a safe HTML block. An alternative is creating the script with `textContent = JSON.stringify(object)` from DOM values, weighing JS dependence against native markup.

Test quotes, `&`, `<`, `>`, backslashes, line breaks, accents, emoji, and `</script>`. Do not double-escape or use regex as a general HTML sanitizer.

### Two trees

Valid theme XML can generate invalid HTML. Sections and widgets generate wrappers, includes insert nodes, and conditions remove branches. A section inside `<ul>` may become a `<div>` where `<li>` was expected. Validate each view's final HTML. Do not open an element inside a condition and close it outside: use complete elements or `b:tag`.

## Template language

### Basic control

| Construct | Brief contract |
|---|---|
| `<data:blog.title/>` | Emits a datum |
| `expr:href='…'` | Evaluates an attribute value |
| `b:includable id='main'` | Custom widget's main entry point |
| `b:include name='…' data='…' cond='…'` | Invokes an includable with optional context |
| `b:if cond`, `b:elseif cond`, `b:else` | Branches; elseif/else are self-closing separators |
| `b:switch var`, `b:case value`, `b:default` | Selection by value |
| `b:loop values var index` | Iteration; the index starts at zero |
| `b:eval expr` | Emits an expression result |
| `b:with var value` | Alias scoped to descendants |

Declaring an additional includable does not execute it. Received context corresponds to its `var`; resolve local and inherited includes before renaming them. Ranges such as `1 to 3` are inclusive. [Official tag contract](https://support.google.com/blogger/answer/46995?hl=en).

### Operators

`+` adds or concatenates; `-` subtracts; `== != < > <= >=` compare; `!`/`not` negate; `and`/`or` combine; `?:` selects; `in`/`contains` express membership with the container on opposite sides; `()` groups; `[0]` indexes a list. Blogger Help's own examples are the safest forms to copy: `cond='data:blog.pageType == "item" and data:post.allowComments'`, `cond='data:blog.pageType in {"static_page", "item"}'`, `expr:href='data:blog.homepageUrl + "feeds/posts/default"'`, `expr:class='data:post.allowComments ? "comment" : "no-comment"'`, `expr='data:post.labels[0].url'`. In XML attributes write `<` as `&lt;`; prefer `and` over `&&` to avoid extra escaping. [Official announcement](https://blogger.googleblog.com/2015/05/).

Do not automatically use `===`, `?.`, `??`, `.map()`, `.filter()`, regex, `Date`, `Math`, or JS functions. Surface similarity does not imply JavaScript grammar. `b:eval` is not JavaScript `eval()` either.

### Lambdas and transformations

Original syntax example:

```xml
<b:if cond='data:post.labels any (label => label.name == "Tutorial")'>
  <span class='post-badge'>Tutorial</span>
</b:if>
```

The operator applies to an already available collection. It does not run new queries or request more posts. Blogger announced lambdas in 2016. [Announcement](https://blogger.googleblog.com/2016/05/more-custom-template-flexibility.html).

Advanced inventory observed in the author-provided [Operators](https://template-data.blogspot.com/p/operators_22.html) example: `* / %`; aliases `eq lt lte gt gte`; `take/limit`, `skip/offset`; `any/all/none/count/map/filter`; arrays `[]`, objects `{ key: value }`; `.size/.length`, `.first/.last`, `.empty/.notEmpty`; `.escaped/.jsEscaped/.jsonEscaped/.cssEscaped`; `snippet`, `resizeImage`, `sourceSet`, `format`; `path`, `params`, `appendParams`, `fragment`. Verify availability in context. `params` replaces parameters; `appendParams` adds them. Dates use ICU formats, written as an infix operator: `data:post.date format "MMMM d, yyyy"`; a date also offers `.iso8601`. `sourceSet` needs sizes appropriate to the original image. Do not infer support for unlisted operators.

Do not invent an undocumented exhaustive precedence table: group conditions and calculations explicitly. Guard empty arrays before indexing or using `first/last`. Do not convert blog IDs to JS numbers: they may exceed safe integer precision.

### Modern tags and semantics

**`b:tag`:** creates a conditional wrapper; when `cond` is false its children still exist. It therefore does not hide a block. `expr:name` selects the name; its control attributes are not copied to the result. [b:tag example](https://template-data.blogspot.com/p/btag.html).

**`b:class`:** adds a class to the parent, optionally with `cond`. **`b:attr`:** adds an attribute to the parent using `name`, `value`, and `cond`. Avoid collisions with directly written attributes. [b:class](https://template-data.blogspot.com/p/bclass.html), [b:attr](https://template-data.blogspot.com/p/battr.html).

**`b:message` / `b:param`:** localized messages and named parameters; preserve exact keys. **`b:comment`:** a comment not rendered by default; `render='true'` emits it. Do not put secrets in a theme even if a comment is not published. [b:message](https://template-data.blogspot.com/p/bmessage.html), [b:comment](https://template-data.blogspot.com/p/bcomment.html).

**`b:defaultmarkups` / `b:defaultmarkup`:** customize default markup by widget type; **`b:widget-settings` / `b:widget-setting`** store settings; **`b:template-skin`** appears as layout-editor CSS. Preserve the exported theme's structure and keys: there is no universal guarantee here for every combination. [Inspected implementation](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml).

### Detected errors: do not perpetuate them

- The Template Data `b:if` example uses `b:elseif value`; the condition attribute is `cond`.
- Its last-item `b:loop` example compares against `.size + 1`; a zero-based index requires `.size - 1`.
- Some examples use `href='data:…'` without `expr:`: do not copy that as a dynamic link.
- The lambda announcement has an incorrect illustrative closing tag `<b:if/>`; write `</b:if>`.
- Official help contains typographically incomplete examples. Validate each reconstructed example as XML before reuse.

Contextualized original examples are in [Recipes](blogger-validation.md#original-contextualized-recipes). They do not prove server acceptance of every combination in every theme.

## Data, types, and views

### Scope before names

`data:blog` provides global context; other data belongs to a widget type. In a loop, `var='p'` creates `data:p`. An include can rename its received object through `var`. Do not assume `data:post` exists in head, Header, or HTML: it is normally available within Blog or an explicit context.

The older official reference includes `blog.title`, `pageTitle`, `pageType`, `url`, `homepageUrl`, `encoding`, and `languageDirection`; for Blog, pagination, posts, labels, and comments; for HTML/Text, `title` and `content`. It is a useful foundation, not a complete modern schema. [Layouts Data Tags](https://support.google.com/blogger/answer/47270?hl=en).

### Modern views

| Data | Use | Caution |
|---|---|---|
| `data:view.isHomepage` | Homepage | Do not substitute `pageType == index` |
| `data:view.isPost` | Individual post | Different from a static page |
| `data:view.isPage` | Static page | Do not assume post labels/author |
| `data:view.isSingleItem` | Post or page | Does not mean only a post |
| `data:view.isMultipleItems` | Listing view | May have zero or one result |
| `data:view.isSearch` | Search or label | Check `search.label/query` |
| `data:view.isArchive` | Date archive | `archive` depends on this view |
| `data:view.isError` | Error | Also inspect the actual HTTP response |
| `data:view.isPreview` | Preview | Not a production test |
| `data:view.isLayoutMode` | Layout editor | Do not hide editing controls |

Additional observed fields: `title`, `description`, `url`, `featuredImage`, `postId`, `pageId`, `search.label`, `search.query`, and `archive.year/month/day`. Some are optional. URLs may expose `.canonical`; `view.type` uses another classification and is not equivalent to `blog.pageType`.

Per-field evidence: [isHomepage](https://template-data.blogspot.com/2017/03/ishomepage_41.html), [isPost](https://template-data.blogspot.com/2017/03/ispost_36.html), [isPage](https://template-data.blogspot.com/2017/03/ispage_35.html), [isSingleItem](https://template-data.blogspot.com/2017/03/issingleitem_88.html), [isMultipleItems](https://template-data.blogspot.com/2017/03/ismultipleitems_10.html), [isSearch](https://template-data.blogspot.com/2016/04/issearch_32.html), [isArchive](https://template-data.blogspot.com/2016/04/isarchive_98.html), [isError](https://template-data.blogspot.com/2017/03/iserror_96.html), [isPreview](https://template-data.blogspot.com/2017/03/ispreview_16.html), [isLayoutMode](https://template-data.blogspot.com/2016/04/islayoutmode_15.html). These are author-provided examples, not an official contract updated for 2026.

### Blog compatibility

| Concept | Common in v1 | Common in v2 |
|---|---|---|
| Author | String `post.author` | `post.author.name`, other object fields |
| Comments | `post.numComments` | `post.numberOfComments` |
| Image | `post.firstImageUrl`, context-dependent thumbnails | `post.featuredImage` |
| Date | `post.timestamp` and variants | `post.date`, `post.lastUpdated` |
| Excerpt | Theme fields or logic | `post.snippets` or the `snippet` helper |

This is a detection guide, not a guaranteed equivalence table. Verify each row against exported XML and output. Do not migrate through global replacement. [v1 code comparison](https://github.com/puikinsh/Pixel-Blogger-Template/blob/87b8ddfdd76df90d0af5531a22c3140328631e7e/Pixel-Blogger-Template.xml), [v2](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml).

### Targeted discovery

1. Find the widget and its version. Search it for `data:`, `expr:`, `var`, `b:with`, and includes.
2. Follow every include and passed object without assuming global scope.
3. Locate examples that already consume the field in that export.
4. Add a small test to the test blog and observe missing values, types, and escaped output.
5. Save evidence with view, version, and result; remove diagnostic data before delivery.

The `data:messages`, `data:skin.vars`, and `data:widgets` dictionaries help in modern themes, but are not an arbitrarily queryable database. `widgets` does not authorize inventing properties or restoring retired gadgets. `skin.vars` fields depend on defined variables and may have normalized names; obtain them from the actual theme.

### Missing data and consistency

Handle posts without images/titles/labels, profiles without photos, disabled comments, empty searches, and lists with no results. A missing field does not always mean a syntax error; it can indicate the wrong view or scope.

Do not filter `data:posts` to simulate global categories: that only filters the current page's batch. It can produce empty pages and inconsistent pagination. Use label links or an explicit external query for independent blocks. Preserve correct Blogger-supplied URLs; do not construct slugs from titles.

## Sections, widgets, and includes

### Structural contract

Blogger Help states this contract, reread on October 13, 2026. Follow it exactly.

| Tag | Attribute | Rule |
|---|---|---|
| `b:section` | `id` | Required. “A unique name, with letters and numbers only”: no hyphens, underscores or spaces |
| | `class` | Optional but recommended. Common names are `navbar`, `header`, `main`, `sidebar` and `footer`; Blogger uses them to carry gadgets over when the owner changes theme. Avoid `tabs` and `crosscol`, which Blogger's own stylesheet styles |
| | `maxwidgets` | Optional number. No limit when absent |
| | `showaddelement` | Optional, `yes` or `no`, default `yes` |
| | `growth` | Optional, `horizontal` or `vertical`, default `vertical` |
| `b:widget` | `id` | Required. Letters and numbers only, unique in the theme. It cannot be changed without deleting the gadget and its content. Convention: the type followed by a number, such as `Blog1`, `HTML2`, `PageList1` |
| | `type` | Required |
| | `locked` | Optional. Help documents `yes` and `no`; Blogger's own exports, and Help's own last example, write `true` and `false`. Use `true` or `false` |
| | `title` | Optional |
| | `pageType` | Optional, `all`, `archive`, `main` or `item`, default `all` |
| | `mobile` | Optional, `yes`, `no` or `only`. It matters only when the blog serves Blogger's separate mobile theme; see [mobile and Layout](#mobile-and-layout-compatibility) |
| `b:includable` | `id` | Required. Letters and numbers. Every gadget that defines includables has one with `id='main'` |
| | `var` | Optional. Letters and numbers |
| `b:include` | `name` | Required. Must match an includable of the same gadget, defined there or [native to that gadget type](blogger-theme.md#native-blog-includables) |
| | `data`, `cond` | Optional |

“A section can only contain widgets. To insert extra code within a section, split the section into two or more new sections.” Any HTML may go around sections. On the published page every section and gadget becomes a `div` with that id, so a section may never sit where a `div` is not allowed (inside a list, a table, a paragraph, a link or a heading), nor inside another Blogger tag. IDs should remain stable; changing them can be equivalent to deleting and creating an instance. [Official contract](https://support.google.com/blogger/answer/46888?hl=en).

The data each common gadget offers to a custom `main`, a complete Blog gadget with every view, and how to show a section on some views only are written out in the [theme skeleton](blogger-theme.md). Use familiar conventions such as `Blog1`, `Header1`, `HTML1`, `Label1`, and `PageList1`. Historical `HeaderView`/`ListView` examples do not justify inventing types for a current theme. Obtain actual types and settings from a recent export.

### Editable areas

Place semantic structure outside sections and custom content inside an HTML widget or its includable. `maxwidgets='1'` limits a particular area; it is not Blogger's global limit. `showaddelement='no'` hides the add control; `locked` controls Layout operations, not server authorization.

```xml
<aside aria-label='Additional information'>
  <b:section class='sidebar' id='sidebar' showaddelement='yes'>
    <b:widget id='HTML1' locked='false' title='Information' type='HTML'/>
  </b:section>
</aside>
```

An abbreviated declaration lets the system fill in default markup; verify expansion by saving and re-exporting. Do not use an empty `main` includable to “let it fill itself in”: it can suppress content.

### Recommended functional inventory

| Need | Starting point | Review |
|---|---|---|
| Posts and pages | Blog | Body, navigation, comments, metadata |
| Identity | Header / Profile | Logo, title, single author or team |
| Navigation | PageList / LinkList | Current URL, keyboard, editable links |
| Taxonomy | Label | Empty state, counts, long names |
| Discovery | BlogArchive / PopularPosts / FeaturedPost | Batch, dates, specific format |
| Free-form content | HTML / Text / Image | Escaping, images, configuration |
| Contact | ContactForm | Scripts, actual submission, error states |
| Monetization | AdSense or HTML area | Authorized integration, dimensions |
| Institutional context | Attribution / ReportAbuse | Behavior and current requirements |

These are candidates, not guarantees that every gadget is available in every account/family. Do not add retired services because they appear in an old dictionary.

### Composition and inheritance

A custom widget is organized around `main`; other includables represent components. Use explicit parameters. Resolve local includes, default markup, and inherited context before claiming a `b:include` does not exist. Names such as `super.*` in an existing theme may depend on inheritance; do not invent or delete them merely because no local definition is visible.

`b:defaultmarkups` centralizes customization by type, but affects multiple instances. For a targeted change, assess scope before replacing an entire default. Preserve `widget-settings` unless a change is requested; keys vary by gadget and version. [Code showing defaults and settings](https://github.com/jettheme/core/blob/06b6913c332bdad2c2f827fb9a91977cbdfb836f/jettheme-v2.xml).

### Blog and comments

Starting from native Blog reduces risk by preserving comments, pagination, bylines, and owner options. If rewriting it, build an equivalence matrix: full post, static page, listings, search, archives, dates, labels, author, existing/new comments, editing, and navigation.

Do not replace comments with a static form that does not submit. Widgets may need scripts, an iframe, IDs, and settings. Distinguish visible comments, acceptance of new comments, and form location. Test anonymously and under actual browser restrictions; do not depend on administrator cookies.

Do not insert ads by indiscriminately manipulating body `innerHTML`: it can destroy listeners, duplicate elements, or split tables/code. If insertion is required, work on eligible nodes after defining exclusions and preserve editorial integrity.

### Mobile and Layout compatibility

The historical `mobile` option does not replace responsive CSS. Test normal URLs and `?m=1`; output depends on the family and mobile settings. Do not force JS redirects to hide a problem.

A responsive theme is meant to be the one theme for every device. Blogger also has a separate mobile theme, switched in the Theme screen's mobile settings; when it is on, phones get Blogger's own mobile layout instead of the theme, and only Header, Blog, Profile, PageList, AdSense and Attribution gadgets appear unless a gadget says `mobile='yes'`. Tell the owner, in the delivery note, to set the mobile setting to show the desktop theme on phones. Do not add `mobile` attributes to make up for it.

The Layout editor is also a view: grids, fixed elements, hidden containers, or clipping can prevent moving gadgets. Scope fixes to Layout mode through mechanisms supported by the theme (`b:template-skin`, verified selectors, `data:view.isLayoutMode`).

After migration, review retained widgets, duplicates, titles, and settings. Avoid a “cleanup template” that deletes all gadgets to fix one specific issue.
