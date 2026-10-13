# Responsive Layout

Every theme this skill delivers is responsive. This is not an interview question and not an option: a theme that scrolls sideways on a phone, hides its navigation, or needs zooming to read is a defect, whatever the design brief says. “Keeping almost the entire design” on phones means the same content and character in a layout that fits, never a shrunken desktop page.

Read this file in the design, implementation and verification stages of every theme, with or without effects.

## Contents

- [The contract](#the-contract)
- [Blogger's own stylesheet](#bloggers-own-stylesheet)
- [Base skin](#base-skin)
- [Layout patterns](#layout-patterns)
- [Navigation that works on a phone](#navigation-that-works-on-a-phone)
- [Post content you do not control](#post-content-you-do-not-control)
- [Fonts](#fonts)
- [Never](#never)
- [Checking](#checking)

## The contract

A theme passes only if all of this holds on the homepage, a post, a static page, a label or search listing, and the error page:

1. **No sideways scroll** at 320, 375, 768, 1024 and 1280 px wide. The document is never wider than the viewport.
2. **Readable without zooming.** Body text is at least 16 px; lines in a post are 45 to 75 characters on wide screens.
3. **Everything is reachable.** No content, link or control is hidden on a phone that exists on a desktop. Navigation is fully usable by touch and by keyboard.
4. **Touch targets** are at least 44 by 44 CSS px, with space between neighbors.
5. **Media fits.** Images, video, embeds, tables and code never push the page wider.
6. **It survives real content.** A sixty-character title, a single long word, a post with no image, a label with thirty posts and an empty listing all hold the layout.
7. **It reflows at 200% zoom** and when the reader raises the default text size.
8. **The head is right.** The viewport meta tag is present and the `html` element carries `b:responsive='true'`.

```xml
<meta content='width=device-width, initial-scale=1' name='viewport'/>
```

Never add `maximum-scale` or `user-scalable=no`: readers must be able to zoom.

## Blogger's own stylesheet

Unless `b:css='false'` is set on the `html` element, Blogger loads its own widget stylesheet before the theme's skin. A local preview does not contain it, which is why a layout can look right locally and shift on the live blog. These rules were read on October 12, 2026 from the stylesheet served to a public Blogger blog (`css_bundle_v2.css`); treat them as observed behavior, not a published contract, and inspect the stylesheet of the saved theme when something looks off.

| Blogger's rule | What it does to a theme |
|---|---|
| `.section { margin: 0 15px }` | Every section gets side margins, so full-width headers, heroes and footers stop short of the edges |
| `.widget { margin: 30px 0 }`, `.widget { line-height: 1.4 }` | Vertical gaps between gadgets, and a line height that overrides the body's |
| `h1, h2, h3, h4 { margin: 0; position: relative }` | Headings lose the browser's default spacing |
| `.widget ul { padding: 0 0 0 1.25em; margin: 0; line-height: 1.2 }`, `.widget li { padding: .25em 0; margin: 0 }` | Lists inside any gadget get this spacing, and it out-ranks a single class selector |
| `.widget .post-body ul { padding: 0 2.5em; margin: .5em 0; line-height: 1.4 }` | Lists in posts get wide side padding, which is too much on a phone |
| `body { word-break: break-word }` | Any word can break anywhere, so a flex or grid item can collapse to one letter per line |
| `a img { position: relative }`, `.post-body { width: 100% }` | Usually harmless; reset so nothing depends on them |

The theme keeps Blogger's stylesheet, because native comments and gadgets rely on it, and cancels these rules at the top of the skin. The [base skin](#base-skin) does this. Two rules follow for all theme CSS:

- **Always set heading margins yourself.** Never rely on the browser default for `h1` to `h4`.
- **Style lists with the element in the selector**: `ul.card-list`, `.site-nav ul`, `.card-list > li`. A bare `.card-list` loses to Blogger's `.widget ul`.

Setting `b:css='false'` removes the stylesheet altogether. It also leaves native comment threads and gadgets unstyled, so it is a larger decision that belongs to a redesign with those parts restyled and tested, not a shortcut.

## Base skin

Start every skin with this block, then replace the token values with the design plan's. If the theme defines Blogger designer variables (see [theme skeleton](blogger-theme.md#designer-variables)), their comment block comes first and this block follows it. It is mobile first: the single-column phone layout is the default and wider layouts are added with `min-width` queries.

```css
/* 1. Cancel Blogger's widget stylesheet where it bends the layout. */
.section, .widget { margin: 0; padding: 0; line-height: inherit; }
.widget ul, .widget ol { margin: 0; padding: 0; line-height: inherit; }
.widget li { margin: 0; padding: 0; text-indent: 0; }
h1, h2, h3, h4 { position: static; }
a img { position: static; }

/* 2. Foundations. */
*, *::before, *::after { box-sizing: border-box; }
:root {
  --gutter: clamp(1rem, 4vw, 2.5rem);
  --page: 74rem;
  --measure: 32em;
  --radius: .6rem;
  --step--1: clamp(.83rem, .8rem + .15vw, .9rem);
  --step-0: clamp(1rem, .96rem + .2vw, 1.125rem);
  --step-1: clamp(1.2rem, 1.1rem + .5vw, 1.5rem);
  --step-2: clamp(1.45rem, 1.25rem + 1vw, 2rem);
  --step-3: clamp(1.75rem, 1.35rem + 2vw, 2.9rem);
  --step-4: clamp(2.1rem, 1.4rem + 3.6vw, 4.25rem);
}
html { -webkit-text-size-adjust: 100%; text-size-adjust: 100%; scroll-padding-top: 5rem; }
body { margin: 0; padding: 0; font-size: var(--step-0); line-height: 1.65; word-break: normal; overflow-wrap: break-word; }
h1, h2, h3, h4 { margin: 0 0 .5em; line-height: 1.15; text-wrap: balance; overflow-wrap: break-word; }
h1 { font-size: var(--step-4); }
h2 { font-size: var(--step-3); }
h3 { font-size: var(--step-1); }
p { margin: 0 0 1em; }
img, svg, video, canvas { max-width: 100%; }
img, video { height: auto; }
a { text-underline-offset: .18em; }
:focus-visible { outline: 3px solid currentColor; outline-offset: 3px; }
button, input, select, textarea { font: inherit; max-width: 100%; }
[hidden] { display: none !important; }

/* 3. Page shell. */
.shell { width: min(100% - var(--gutter) * 2, var(--page)); margin-inline: auto; }
.shell--wide { width: min(100% - var(--gutter) * 2, 90rem); }
.bleed { padding-inline: var(--gutter); }
.grid { display: grid; gap: clamp(1rem, 3vw, 2rem); grid-template-columns: repeat(auto-fill, minmax(min(100%, 17rem), 1fr)); }
.grid > *, .split > * { min-width: 0; }
.split { display: grid; gap: clamp(1.5rem, 4vw, 3rem); }
@media (min-width: 64em) { .split { grid-template-columns: minmax(0, 1fr) minmax(0, 20rem); align-items: start; } }
.skip-link { position: absolute; left: var(--gutter); top: -10rem; z-index: 100; padding: .75rem 1rem; background: #fff; color: #000; }
.skip-link:focus { top: .75rem; }
```

How it is meant to be used:

- **`.shell`** centers content with a gutter that never disappears. `.bleed` is for full-width bands that still need side padding.
- **`.grid`** is the default for card lists. It needs no breakpoints: columns appear when there is room for 17rem and collapse to one when there is not. Change the `17rem` to change how many columns a width gives: with the 74rem page, 17rem gives four columns at 1280 px and 21rem gives three. It uses `auto-fill`, so a single search result keeps the width of one card instead of stretching across the page.
- **`--measure`** is the width of a post's text column. It is set in `em`, not `ch`: a `ch` is the width of the digit zero, which is far wider than an average letter, and 68ch measured 85 to 104 characters per line in common fonts. 32em gives about 65 to 74. Count the characters in a full line of the real font at 1280 px and adjust until it is between 45 and 75.
- **`[hidden]`** is forced to hide. Without that line, any element whose class sets `display` ignores the `hidden` attribute, and buttons meant to stay hidden until a script runs would show.
- **`.split`** is article plus sidebar: one column until 64em, then two.
- **The type scale** grows with the viewport between fixed limits, so no heading needs its own media query. Use `--step-4` for a hero title, `--step-3` for section titles, `--step-0` for body.
- Add breakpoints only where the content asks for one. When you do, use `min-width` in `em` (30em, 48em, 64em, 80em), not device names.

## Layout patterns

**Hero with text beside art.** One column on phones with the text first, two from 48em.

```css
.hero { display: grid; gap: clamp(1.5rem, 5vw, 4rem); align-items: center; padding-block: clamp(2.5rem, 8vw, 6rem); }
.hero > * { min-width: 0; }
@media (min-width: 48em) { .hero { grid-template-columns: minmax(0, 1.1fr) minmax(0, .9fr); } }
```

**Uneven or magazine grids.** Build the special arrangement only from 48em up and let the same items fall into `.grid` below it. Never position cards with fixed pixel offsets. Make a featured card wider, not taller: spanning rows stretches a photo-above-text card into a tall, mostly empty box.

```css
@media (min-width: 48em) {
  .mosaic { display: grid; grid-template-columns: repeat(6, minmax(0, 1fr)); gap: 1.5rem; }
  .mosaic > * { grid-column: span 2; }
  .mosaic > :first-child { grid-column: span 4; }
}
```

**Media with a fixed shape.** Reserve the space with `aspect-ratio` and let the image fill it.

```css
.media { display: block; aspect-ratio: 3 / 2; overflow: hidden; background: #e7e5e4; }
.media img { display: block; width: 100%; height: 100%; object-fit: cover; }
```

**Rows that wrap.** Meta lines, tag lists and footers use `display: flex; flex-wrap: wrap; gap: …`. A flex row without `flex-wrap` is the most common cause of sideways scroll.

**Sticky header.** Keep it under 4.5rem tall on phones, and keep `scroll-padding-top` on `html` at least that tall so in-page links are not hidden behind it. Below the menu breakpoint, make the bar sticky only when the menu is collapsed (`.js .site-top { position: sticky; top: 0; }`): without JavaScript the links wrap onto several rows, and a sticky bar that tall would cover the page.

**Decorative layers.** Anything positioned outside its section (blobs, rings, oversized type) needs `overflow: hidden` or `overflow-x: clip` on that section. When the section, or a card or frame inside it, holds an effect tied to scrolling, clip with `overflow: clip`: a box with `overflow: hidden` counts as a scrolling area and the effect would follow it instead of the page.

## Navigation that works on a phone

Links wrap in a row by default, so the menu works with JavaScript off. With JavaScript on, phones get a menu button that opens and closes the list; from the menu breakpoint up the button disappears and the links sit in a row. The button is a real `button` with `aria-expanded`, closes on Escape and on a click outside, and returns focus when closed.

The menu breakpoint is 48em when the bar holds a brand and up to about five short links. A search field, a light and dark button or more links need more room. Start at 64em, measure the row at the breakpoint with the theme's fonts loaded, and move it up until everything sits on one line: bars with five links, a search field and a mode button needed 68em to 75em in tests. The breakpoint is written in several places that change together: the two media queries below, the `(min-width: 48em)` in the menu script, the rule that places a search form in the bar, and every recipe rule written for the open bar, such as the menu variant of [T9](effects-text.md#t9-drawing-underline-and-lines).

```xml
<header class='site-head'>
  <a class='site-brand' href='/'>Paper and Sun</a>
  <button class='fx-menu' type='button' aria-expanded='false' aria-controls='siteNav' hidden='hidden'>
    <span class='fx-menu__bars' aria-hidden='true'><i></i><i></i><i></i></span>
    <span class='fx-vh'>Menu</span>
  </button>
  <nav class='site-nav' id='siteNav' aria-label='Main'>
    <ul>
      <li><a href='/'>Home</a></li>
      <li><a href='/search/label/Recipes'>Recipes</a></li>
      <li><a href='/p/about.html'>About</a></li>
      <li><a href='/p/contact.html'>Contact</a></li>
    </ul>
  </nav>
</header>
```

In a real theme the list comes from a `PageList` or `LinkList` gadget in a section inside the `nav`, so the owner edits the links in Layout; the [theme skeleton](blogger-theme.md#navigation-gadget) shows that gadget with its own markup. The rules below are written to style that list as well. A search field goes inside the same `nav`, after the list, so the one button opens both.

```css
.site-head { display: flex; flex-wrap: wrap; align-items: center; justify-content: space-between; gap: .5rem 1.5rem; padding: .75rem var(--gutter); }
.site-brand { display: inline-flex; align-items: center; min-height: 2.75rem; font-weight: 700; text-decoration: none; }
.site-nav ul { display: flex; flex-wrap: wrap; gap: 0 1.25rem; margin: 0; padding: 0; list-style: none; }
.site-nav li { margin: 0; padding: 0; }
.site-nav a { display: inline-flex; align-items: center; min-height: 2.75rem; text-decoration: none; }
.site-nav a:hover { text-decoration: underline; }
.site-nav a:focus-visible { text-decoration: underline; }
.fx-menu { display: inline-grid; place-items: center; width: 2.75rem; height: 2.75rem; padding: 0; border: 0; border-radius: var(--radius, .6rem); background: none; color: inherit; cursor: pointer; }
.fx-menu[hidden] { display: none; }
.fx-menu__bars { display: grid; gap: 5px; width: 1.4rem; }
.fx-menu__bars i { display: block; height: 2px; border-radius: 2px; background: currentColor; }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(2) { opacity: 0; }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }
.fx-vh { position: absolute; width: 1px; height: 1px; margin: -1px; overflow: hidden; clip-path: inset(50%); white-space: nowrap; }
@media (max-width: 47.99em) {
  .js .site-nav { display: none; flex-basis: 100%; }
  .js .site-nav.is-open { display: block; max-height: calc(100vh - 5rem); overflow-y: auto; overscroll-behavior: contain; }
  .js .site-nav ul { flex-direction: column; }
  .js .site-nav a { display: flex; min-height: 3rem; }
}
@media (min-width: 48em) { .site-head .fx-menu { display: none; } }
```

When the bar holds a search field, the form sits inside the `nav` after the list. From the menu breakpoint up, the list and the form share one row; below it, the form is a full-width row at the end of the opened menu:

```css
.site-nav form { display: flex; align-items: center; gap: .5rem; margin: 0; }
.site-nav input[type='search'] { flex: 1 1 8rem; width: 8rem; min-width: 0; min-height: 2.75rem; }
@media (min-width: 48em) {
  .site-nav { display: flex; align-items: center; gap: .25rem 1.25rem; min-width: 0; }
  .site-nav form { flex: none; }
}
```

The field needs its explicit `width`: without one a text field keeps its built-in width of about twenty characters and pushes the links onto a second row. From the breakpoint up the form keeps that width and, when the row is too short, the links wrap inside the bar and nothing sticks out of the page. Links that wrap at the breakpoint mean the breakpoint is too low.

The `.js` class is what collapses the list on a phone, so it must never be set unless the button is really going to work. It is set in the head, and taken away again if the menu script has not reported in after three seconds: a phone never ends up with the links hidden and no button. A theme that uses the [effects kit](effects.md#the-kit) uses the kit's head gate, which does exactly this as well. A theme with no effects adds this instead, directly after the closing `b:skin` tag:

```xml
<script>
//<![CDATA[
(function (d, w) {
  var c = d.documentElement.classList;
  c.add('js');
  w.setTimeout(function () { if (!c.contains('menu-ready')) c.remove('js'); }, 3000);
})(document, window);
//]]>
</script>
```

The menu script goes in the body script, with or without the kit:

```js
(function () {
  var root = document.documentElement, btn = document.querySelector('.fx-menu[aria-controls]');
  var nav = btn && document.getElementById(btn.getAttribute('aria-controls'));
  if (!btn || !nav) { root.classList.remove('js'); return; }
  function isOpen() { return btn.getAttribute('aria-expanded') === 'true'; }
  function set(open) { btn.setAttribute('aria-expanded', String(open)); nav.classList.toggle('is-open', open); }
  btn.hidden = false;
  root.classList.add('js', 'menu-ready');
  btn.addEventListener('click', function () { set(!isOpen()); });
  document.addEventListener('keydown', function (e) { if (e.key === 'Escape' && isOpen()) { set(false); btn.focus(); } });
  document.addEventListener('click', function (e) { if (isOpen() && !nav.contains(e.target) && !btn.contains(e.target)) set(false); });
  nav.addEventListener('click', function (e) { if (e.target.closest && e.target.closest('a')) set(false); });
  var wide = window.matchMedia ? window.matchMedia('(min-width: 48em)') : null;
  if (wide && wide.addEventListener) wide.addEventListener('change', function () { set(false); });
})();
```

Put this block first in the body script, before the kit: one script block means an error in a later recipe would otherwise take the menu down with it. The bars already switch to a cross when the menu is open, instantly, which is right for a still theme. For the bars to turn smoothly, add the one transition rule from [I3](effects-interaction.md#i3-menu-button-morph). A side menu or a large category menu uses the same button and script; only the open state's CSS changes (a fixed panel instead of an inline block). Whatever the style, the links must also be reachable when the script fails.

## Post content you do not control

Posts hold whatever the owner pasted over the years: wide images, tables, code, embeds, inline styles. The theme must contain all of it.

```css
.post-body { width: auto; max-width: var(--measure); margin-inline: auto; overflow-wrap: anywhere; overflow-x: auto; }
.post-body img, .post-body video, .post-body iframe, .post-body embed, .post-body object { max-width: 100%; }
.post-body iframe { border: 0; }
.post-body img { height: auto; }
.post-body iframe[src*='youtube'], .post-body iframe[src*='vimeo'], .post-body iframe[src*='blogger.com/video'] { width: 100%; height: auto; aspect-ratio: 16 / 9; }
.post-body pre { max-width: 100%; overflow-x: auto; white-space: pre; }
.post-body table { border-collapse: collapse; overflow-wrap: normal; word-break: normal; }
.post-body .fx-scroll { max-width: 100%; overflow-x: auto; }
.widget .post-body ul, .widget .post-body ol { margin: 1em 0; padding: 0 0 0 1.4em; line-height: inherit; }
.widget .post-body li { margin: 0 0 .35em; padding: 0; }
.post-body .separator, .post-body .tr-caption-container { max-width: 100%; }
.post-body a[style*='margin-left'], .post-body a[style*='margin-right'] { margin-inline: 0 !important; }
```

A wide table cannot shrink without breaking its words apart, and must not be turned into a block, which would break it for screen readers. So table cells keep whole words, and this short script wraps each table that is wider than the post in a scrolling region the reader can focus. Without JavaScript the post body itself scrolls sideways instead; the page never does.

```js
(function () {
  Array.prototype.forEach.call(document.querySelectorAll('.post-body table'), function (table) {
    if (table.scrollWidth <= table.parentNode.clientWidth || table.parentNode.classList.contains('fx-scroll')) return;
    var wrap = document.createElement('div');
    wrap.className = 'fx-scroll'; wrap.tabIndex = 0;
    wrap.setAttribute('role', 'region'); wrap.setAttribute('aria-label', 'Table, scroll sideways to see more');
    table.parentNode.insertBefore(wrap, table); wrap.appendChild(table);
  });
})();
```

The `margin-inline` rule cancels the fixed side margins Blogger's editor writes onto image links, which otherwise push images off a narrow screen. The post title, the comment section and the pager live outside `.post-body` and get the theme's own styles.

## Fonts

A pairing from the interview, or from Fontpair, is loaded from Google Fonts with one stylesheet in the head. Request only the weights the design uses, add `display=swap`, and write each `&` as `&amp;`.

An ordinary stylesheet link stops the page from drawing until it has arrived, so a slow font server would leave readers looking at a blank screen. Load it so the page never waits: the link starts as a print stylesheet, which the browser fetches without blocking, and switches itself on when it has loaded. Readers see the fallback fonts at once and the chosen fonts a moment later.

```xml
<link href='https://fonts.googleapis.com' rel='preconnect'/>
<link crossorigin='anonymous' href='https://fonts.gstatic.com' rel='preconnect'/>
<link href='https://fonts.googleapis.com/css2?family=Fraunces:wght@400;700&amp;family=Manrope:wght@400;600&amp;display=swap' media='print' onload='this.media=&quot;all&quot;' rel='stylesheet'/>
<noscript><link href='https://fonts.googleapis.com/css2?family=Fraunces:wght@400;700&amp;family=Manrope:wght@400;600&amp;display=swap' rel='stylesheet'/></noscript>
```

```css
:root {
  --font-head: 'Fraunces', Georgia, 'Times New Roman', serif;
  --font-body: 'Manrope', system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
}
body { font-family: var(--font-body); }
h1, h2, h3, h4 { font-family: var(--font-head); }
```

Choose fallback fonts close in width to the chosen ones, so the text barely moves when they swap. Request the stylesheet URL once before delivery: Google Fonts answers with an error when a family name is misspelled or a weight does not exist, and the page then falls back silently. Every `font-family` ends in a system fallback of the same kind, serif or sans. For “no font downloads”, leave the links out and use only the fallback stacks. Two families and four weights in total is the ceiling for a fast page.

## Browser floor

On an edit, match the level of fallbacks the existing theme already uses. For a new theme: the base skin and the recipes are written for browsers from about 2022 on: `clamp()`, `min()`, flex `gap`, `aspect-ratio`, `:focus-visible` and logical properties such as `margin-inline`. Everything else (`text-wrap`, scroll-driven animations, view transitions, `@property`, `color-mix()`) is used only where an older browser simply shows the plain version. When the brief names old devices or an older audience, add fallbacks in this order: a plain value before each `clamp()` or `min()`, `margin-left`/`margin-right` beside `margin-inline`, margins instead of flex `gap`, and a fixed `padding-top` box instead of `aspect-ratio`. Never put `:focus-visible` in the same selector list as `:hover`: a browser that does not know one selector drops the whole rule.

## Never

- `width: 100vw` on a section: it is wider than the page whenever a scrollbar shows. Use `width: 100%`.
- A fixed pixel width on a container, card or image. Use `max-width` and let it shrink.
- `overflow-x: hidden` on `html` or `body` to hide a sideways scroll. Find the element that causes it.
- A flex row of several items without `flex-wrap`, or a grid child without `min-width: 0`. A single control that uses `inline-flex` to center its icon and label is not a row.
- A menu that opens only on hover, or content that appears only on hover.
- `display: none` on real content below a breakpoint.
- Text set in `vw` alone. Use `clamp()` so it has a floor and a ceiling.
- Text baked into an image, or a heading that needs one specific line break to fit.
- A fixed height on something that holds text.
- `position: fixed` panels taller than the smallest screen without their own scrolling.

## Checking

Run this on every view at every width in the contract. It reports whether the page scrolls sideways and which elements stick out. An empty `offenders` list with `overflows: false` is a pass. It is one expression: paste it into the browser console, or pass it to whatever runs script in the page. To check many widths at once, load the preview in `iframe` elements of exact widths and run it inside each. A 640 px frame stands in for 200% zoom at 1280 px. How to get a page to run it on is in [building a local preview](blogger-validation.md#building-a-local-preview).

```js
/* alt: check */
(function () {
  var de = document.documentElement, vw = de.clientWidth, out = [];
  Array.prototype.forEach.call(document.querySelectorAll('body *'), function (el) {
    var r = el.getBoundingClientRect();
    if (!r.width || (r.right <= vw + 1 && r.left >= -1)) return;
    for (var p = el.parentElement; p && p !== de; p = p.parentElement) {
      var s = window.getComputedStyle(p);
      if (s.overflowX !== 'visible' || s.position === 'fixed') return;
    }
    out.push((el.id ? '#' + el.id : el.tagName.toLowerCase()) + (typeof el.className === 'string' && el.className ? '.' + el.className.split(' ')[0] : '') + ' ' + Math.round(r.left) + '→' + Math.round(r.right));
  });
  return { viewport: vw, document: de.scrollWidth, overflows: de.scrollWidth > vw, offenders: out.slice(0, 12) };
})();
```

Then, by hand or with browser tools:

1. At 320 and 375 px: open and close the menu by touch and by keyboard; reach every link; confirm no text is cut off and every tap target is large enough.
2. At the menu breakpoint, at 1024 px and at 1280 px: the header bar is one row. If the links or the search field drop under the brand, raise the menu breakpoint as described in [navigation that works on a phone](#navigation-that-works-on-a-phone).
3. On a post: a wide image, a table, a code block and a video embed all stay inside the column.
4. Zoom to 200% at 1280 px: the layout reflows to its narrow form and nothing overlaps.
5. Compare the phone and desktop homepages: the same sections and the same content are present on both.
6. With JavaScript off: navigation links are visible and usable at every width.
7. On the saved theme in Blogger, repeat the first check on the public URL and on `?m=1`. Local previews do not include Blogger's stylesheet or its generated wrappers.

Record the widths and views actually checked. Without a browser, state that responsive behavior was reviewed in the source only.
