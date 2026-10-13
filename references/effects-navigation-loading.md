# Effect Recipes: Navigation, Reading and Loading

Read [the index and kit](effects.md) first. A Blogger blog moves between real pages. Nothing here replaces that with script-driven routing, delays a click, or shows a loader for something that is not actually loading.

## Contents

- Between pages: [N1 quick fade](#n1-quick-fade), [N2 color wipe](#n2-color-wipe), [N3 sliding panel](#n3-sliding-panel), [N4 curtain](#n4-curtain), [N5 top progress line](#n5-top-progress-line)
- Reading: [R1 reading progress](#r1-reading-progress), [R2 reading time](#r2-reading-time), [R3 article contents](#r3-article-contents), [R4 share links](#r4-share-links), [R5 print](#r5-print)
- Loading: [L1 spinner](#l1-spinner), [L2 pulsing dot](#l2-pulsing-dot), [L3 brand mark](#l3-brand-mark), [L4 status message](#l4-status-message), [L5 content progress line](#l5-content-progress-line), [L6 skeletons with a real request](#l6-skeletons-with-a-real-request)
- Galleries: [G1 sliding gallery](#g1-sliding-gallery), [G2 slow carousel](#g2-slow-carousel)

## How page transitions work here

N1 to N4 use the browser's own cross-document view transitions. They are CSS only. The browser keeps a picture of the old page, loads the new one normally, and animates between the two. Every page of a blog shares one theme, so both pages opt in automatically.

- Nothing is intercepted. Links, new tabs, Back, Forward, downloads and anchors behave exactly as without the effect.
- A browser that does not support it navigates instantly. That is the fallback, and it is a good one. Say so to the user rather than promising the effect everywhere.
- Use exactly one of N1 to N4. Each block is complete on its own.
- Keep the total under half a second. A transition that makes reading wait is a cost, not a feature.

## N1 Quick fade

```css
@media (prefers-reduced-motion: no-preference) {
  @view-transition { navigation: auto; }
  ::view-transition-old(root) { animation: fx-vt-out .16s ease both; }
  ::view-transition-new(root) { animation: fx-vt-in .24s ease both; }
}
@keyframes fx-vt-out { to { opacity: 0; } }
@keyframes fx-vt-in { from { opacity: 0; } }
```

## N2 Color wipe

The new page is uncovered from one side while the old one dims.

```css
/* alt: wipe */
@media (prefers-reduced-motion: no-preference) {
  @view-transition { navigation: auto; }
  ::view-transition-old(root) { animation: fx-vt-dim .4s ease both; }
  ::view-transition-new(root) { animation: fx-vt-wipe .4s cubic-bezier(.7, 0, .2, 1) both; }
}
@keyframes fx-vt-dim { to { filter: brightness(.7); } }
@keyframes fx-vt-wipe { from { clip-path: inset(0 0 0 100%); } to { clip-path: inset(0); } }
```

## N3 Sliding panel

The new page slides in over the old one, which eases back.

```css
/* alt: slide */
@media (prefers-reduced-motion: no-preference) {
  @view-transition { navigation: auto; }
  ::view-transition-old(root) { animation: fx-vt-back .42s cubic-bezier(.7, 0, .2, 1) both; }
  ::view-transition-new(root) { animation: fx-vt-slide .42s cubic-bezier(.7, 0, .2, 1) both; }
}
@keyframes fx-vt-back { to { transform: translateX(-8%); opacity: .4; } }
@keyframes fx-vt-slide { from { transform: translateX(100%); } }
```

## N4 Curtain

The old page lifts away to show a plain color, then the new page rises into place. Set `--fx-curtain` to a brand color.

```css
/* alt: curtain */
@media (prefers-reduced-motion: no-preference) {
  @view-transition { navigation: auto; }
  ::view-transition { background: var(--fx-curtain, #1c1917); }
  ::view-transition-old(root) { animation: fx-vt-lift .26s cubic-bezier(.7, 0, .3, 1) both; }
  ::view-transition-new(root) { animation: fx-vt-rise .3s cubic-bezier(.2, .7, .2, 1) .2s both; }
}
@keyframes fx-vt-lift { to { clip-path: inset(0 0 100% 0); } }
@keyframes fx-vt-rise { from { clip-path: inset(100% 0 0 0); } to { clip-path: inset(0); } }
```

## N5 Top progress line

A thin moving line at the top of the window while the next page is on its way. It is honest: it shows that something is happening, not how much is left. It never blocks a click, never covers content, and always clears.

```xml
<div class='fx-pageline' aria-hidden='true'></div>
```

```css
.fx-pageline { position: fixed; top: 0; left: 0; z-index: 90; width: 100%; height: 3px; overflow: hidden; pointer-events: none; opacity: 0; transition: opacity .2s; }
.fx-pageline::before { content: ''; position: absolute; inset: 0; width: 40%; background: var(--fx-pageline, currentColor); transform: translateX(-100%); }
.fx-pageline.is-on { opacity: 1; }
.fx-pageline.is-on::before { animation: fx-pageline 1.1s ease-in-out infinite; }
@keyframes fx-pageline { to { transform: translateX(250%); } }
@media (prefers-reduced-motion: reduce) { .fx-pageline.is-on::before { animation: none; width: 100%; transform: none; opacity: .6; } }
```

```js
(function () {
  var bar = document.querySelector('.fx-pageline'), timer = 0;
  if (!bar) return;
  function clear() { bar.classList.remove('is-on'); window.clearTimeout(timer); }
  document.addEventListener('click', function (e) {
    if (e.defaultPrevented || e.button !== 0 || e.metaKey || e.ctrlKey || e.shiftKey || e.altKey) return;
    var a = e.target.closest ? e.target.closest('a[href]') : null;
    if (!a || (a.target && a.target !== '_self') || a.hasAttribute('download')) return;
    var to;
    try { to = new URL(a.href, window.location.href); } catch (err) { return; }
    if (to.origin !== window.location.origin) return;
    if (to.pathname === window.location.pathname && to.search === window.location.search) return;
    bar.classList.add('is-on');
    timer = window.setTimeout(clear, 8000);
  });
  window.addEventListener('pageshow', clear);
})();
```

The script only watches clicks; it never calls `preventDefault`. It ignores new-tab and modified clicks, downloads, other sites, `mailto:` and same-page anchors. `pageshow` clears the line when the reader comes back with the Back button, and the timeout clears it if a navigation is cancelled.

## R1 Reading progress

A bar across the top that fills as the reader moves through a post. Show it on posts only. It answers the reader's own scrolling, so it stays on with reduced motion.

In the template, outside any section, near the top of the body:

```xml
<!-- blogger -->
<b:if cond='data:view.isPost'>
  <div class='fx-progress' aria-hidden='true'><span></span></div>
</b:if>
```

```css
.fx-progress { position: fixed; top: 0; left: 0; z-index: 80; width: 100%; height: 3px; pointer-events: none; }
.fx-progress span { display: block; height: 100%; background: var(--fx-progress, currentColor); transform: scaleX(0); transform-origin: 0 50%; }
@supports (animation-timeline: scroll()) {
  .fx-progress span { animation: fx-progress linear both; animation-timeline: scroll(root block); }
  @keyframes fx-progress { to { transform: scaleX(1); } }
}
```

Fallback for browsers without scroll-driven animations:

```js
(function () {
  var bar = document.querySelector('.fx-progress span');
  if (!bar || (window.CSS && CSS.supports && CSS.supports('animation-timeline: scroll()'))) return;
  var frame = 0;
  function paint() {
    frame = 0;
    var max = document.documentElement.scrollHeight - window.innerHeight;
    bar.style.transform = 'scaleX(' + (max > 0 ? Math.min(window.scrollY / max, 1) : 0).toFixed(4) + ')';
  }
  window.addEventListener('scroll', function () { if (!frame) frame = window.requestAnimationFrame(paint); }, { passive: true });
  paint();
})();
```

If the theme also uses N5, place the two lines at different heights or use only one of them.

## R2 Reading time

“About 6 minutes to read”, worked out from the words in the post. The element is in the markup, hidden, and the script fills and shows it; without JavaScript it simply is not there. Place it in the post's meta line, inside the post view.

```xml
<span class='fx-readtime' hidden='hidden'></span>
```

```js
(function () {
  var out = document.querySelector('.fx-readtime'), body = document.querySelector('.post-body');
  if (!out || !body) return;
  var words = (body.textContent.match(/\S+/g) || []).length;
  if (words < 60) return;
  var minutes = Math.max(1, Math.round(words / 220));
  (out.querySelector('.fx-readtime__text') || out).textContent = 'About ' + minutes + (minutes === 1 ? ' minute' : ' minutes') + ' to read';
  out.hidden = false;
})();
```

220 words a minute is an ordinary reading pace. Very short notes show nothing. For an icon beside the text, put both inside the hidden element and give the text its own inner `span`, so the icon appears and disappears with it:

```xml
<span class='fx-readtime' hidden='hidden'><svg aria-hidden='true' class='ico' xmlns='http://www.w3.org/2000/svg'><use href='#i-clock'/></svg> <span class='fx-readtime__text'></span></span>
```

The script writes into `fx-readtime__text` when it is there and into the element itself when it is not. Never leave the icon outside the hidden element: on a short post, or without JavaScript, it would stand alone.

## R3 Article contents

A list of the post's own headings, built when the post has at least three. It reads the `h2` and `h3` elements inside the post body, gives each an id, and links to them. Place the empty block above the post body or in the article's side column.

```xml
<nav class='fx-toc' aria-labelledby='fxTocTitle' hidden='hidden'>
  <h2 id='fxTocTitle'>In this article</h2>
  <ol></ol>
</nav>
```

```css
.fx-toc { margin-block: 1.5rem; padding: 1rem 1.25rem; border: 1px solid color-mix(in srgb, currentColor 16%, transparent); border-radius: var(--fx-radius, .6rem); }
.fx-toc[hidden] { display: none; }
.fx-toc h2 { margin: 0 0 .5rem; font-size: 1rem; }
.fx-toc ol { margin: 0; padding: 0; list-style: none; }
.fx-toc ol > li { margin: 0; padding: 0; }
.fx-toc ol > li.is-sub { padding-inline-start: 1rem; }
.fx-toc a { display: flex; align-items: center; min-height: 2.75rem; text-decoration: none; }
.fx-toc a:hover { text-decoration: underline; }
.fx-toc a:focus-visible { text-decoration: underline; }
```

```js
(function () {
  var toc = document.querySelector('.fx-toc'), body = document.querySelector('.post-body');
  if (!toc || !body) return;
  var list = toc.querySelector('ol'), heads = body.querySelectorAll('h2, h3'), used = {};
  if (!list || heads.length < 3) return;
  Array.prototype.forEach.call(heads, function (h, n) {
    var text = h.textContent.trim();
    if (!text) return;
    if (!h.id) {
      var slug = text.toLowerCase().replace(/[^\p{L}\p{N}]+/gu, '-').replace(/^-+|-+$/g, '') || 'part';
      used[slug] = (used[slug] || 0) + 1;
      h.id = 'toc-' + slug + (used[slug] > 1 ? '-' + used[slug] : '');
    }
    var li = document.createElement('li'), a = document.createElement('a');
    if (h.tagName === 'H3') li.className = 'is-sub';
    a.href = '#' + h.id; a.textContent = text;
    li.appendChild(a); list.appendChild(li);
  });
  if (list.children.length) toc.hidden = false;
})();
```

With a sticky header, keep `scroll-padding-top` on `html` (it is in the [base skin](responsive.md#base-skin)) so a heading reached from the list is not hidden under the bar.

## R4 Share links

Plain links to each service's own share page. No script, no tracking widget, no third-party button code. The addresses are built by Blogger from the post's real address and title. Place it inside the post markup, where `data:post` exists.

```xml
<!-- blogger -->
<ul class='fx-share' aria-label='Share this post'>
  <li><a expr:href='"https://www.facebook.com/sharer/sharer.php?u=" + data:post.url.canonical' rel='noopener noreferrer' target='_blank'>Facebook</a></li>
  <li><a expr:href='"https://x.com/intent/post?url=" + data:post.url.canonical' rel='noopener noreferrer' target='_blank'>X</a></li>
  <li><a expr:href='"https://api.whatsapp.com/send?text=" + data:post.url.canonical' rel='noopener noreferrer' target='_blank'>WhatsApp</a></li>
  <li><a expr:href='"mailto:?body=" + data:post.url.canonical'>Email</a></li>
</ul>
```

```css
ul.fx-share { display: flex; flex-wrap: wrap; gap: .5rem; margin: 2rem 0; padding: 0; list-style: none; }
.fx-share > li { margin: 0; padding: 0; }
.fx-share a { display: inline-flex; align-items: center; min-height: 2.75rem; padding: 0 1rem; border: 1px solid currentColor; border-radius: var(--fx-radius, .6rem); text-decoration: none; }
.fx-share a:hover { text-decoration: underline; }
.fx-share a:focus-visible { text-decoration: underline; }
```

Offer only the services the owner wants. `data:post.url.canonical` was seen in a current third-party theme; if Blogger rejects it on saving, use `data:post.url`. A “copy link” button would need script and clipboard permission; a plain link to the post is enough.

## R5 Print

A print layout for posts: the article and nothing else, black on white, with link addresses written out. Add it at the end of the skin.

```css
@media print {
  .site-head, .site-nav, .fx-menu, .skip-link, .fx-progress, .fx-pageline, .fx-ribbon, .fx-related, .fx-share, .fx-toc, .comments, #comments, .blog-pager, .pager, footer, aside, iframe, video, form { display: none !important; }
  :root { color-scheme: light !important; --bg: #fff !important; --surface: #fff !important; --ink: #000 !important; --muted: #333 !important; --line: #999 !important; --accent: #000 !important; }
  html, body { background: #fff !important; color: #000 !important; }
  body { font-size: 11.5pt; line-height: 1.5; }
  .shell, .post-body { width: auto !important; max-width: none !important; margin: 0 !important; padding: 0 !important; }
  .post-body { overflow: visible !important; }
  h1, h2, h3 { break-after: avoid; }
  img, figure, pre, blockquote, table { break-inside: avoid; max-width: 100% !important; }
  .post-body pre { white-space: pre-wrap; }
  .post-body a[href^='http']::after { content: ' (' attr(href) ')'; font-size: .85em; word-break: break-all; }
  a { color: inherit !important; text-decoration: underline; }
}
```

Add the theme's own header, footer and sidebar class names to the first rule, and use the theme's own token names in the second: it turns every color token to black on white. Keep the `!important` on each token. A dark set of tokens is selected in more than one way (the reader's choice, or the device setting through a selector such as `:root:not([data-theme='light'])`), and without it the stronger of those selectors would keep light text on white paper. A token that the theme sets on an element other than `:root` needs its own line here. The kit already prints content that had not scrolled into view. Check the result in the browser's print preview on a real post, once with the device in light mode and once in dark mode.

## Loading cues: the rule

A Blogger page arrives with its posts already in it. There is nothing to wait for, so there is no page loader, no splash screen and no fake delay. L1 to L6 are for a part of the page that really is fetched afterwards: related posts, a recent-comments list, a gallery read from a feed. The pattern is always the same:

1. The region is in the markup with `hidden='hidden'`. Without JavaScript it never appears, so nobody sees a loader that cannot finish.
2. The script unhides it, sets `aria-busy='true'`, shows the cue, and starts the request with a time limit.
3. On success, on an empty result, on an error and on the time limit, the cue is removed and `aria-busy` is cleared. There is always a useful end state: the content, or a plain link.

L1 to L5 are the cues. L6 is the complete pattern with a real request; use it as the frame and swap its skeleton for any other cue.

## L1 Spinner

```xml
<span class='fx-spinner' role='status'><span class='fx-vh'>Loading</span></span>
```

```css
.fx-spinner { display: inline-block; width: 1.5rem; height: 1.5rem; border: 2px solid currentColor; border-right-color: transparent; border-radius: 50%; animation: fx-spin .8s linear infinite; }
@media (prefers-reduced-motion: reduce) { .fx-spinner { animation-duration: 2.4s !important; animation-iteration-count: infinite !important; } }
```

A spinner is a status, not decoration, so it keeps turning with reduced motion, only slower.

## L2 Pulsing dot

```xml
<span class='fx-dot' role='status'><span class='fx-vh'>Loading</span></span>
```

```css
.fx-dot { display: inline-block; width: .7rem; height: .7rem; border-radius: 50%; background: currentColor; animation: fx-dot 1.1s ease-in-out infinite; }
@keyframes fx-dot { 0%, 100% { transform: scale(.6); opacity: .4; } 50% { transform: scale(1); opacity: 1; } }
@media (prefers-reduced-motion: reduce) { .fx-dot { animation: none; } }
```

## L3 Brand mark

The blog's own mark breathes while a region loads. Put the owner's inline SVG logo, or an initial, inside.

```xml
<span class='fx-mark' role='status'><span aria-hidden='true'>K</span><span class='fx-vh'>Loading</span></span>
```

```css
.fx-mark { display: inline-grid; place-items: center; width: 3rem; height: 3rem; border-radius: 50%; background: var(--fx-mark-bg, #1c1917); color: var(--fx-mark-ink, #fff); font-weight: 700; animation: fx-mark 1.4s ease-in-out infinite; }
@keyframes fx-mark { 0%, 100% { transform: scale(.94); opacity: .75; } 50% { transform: scale(1); opacity: 1; } }
@media (prefers-reduced-motion: reduce) { .fx-mark { animation: none; } }
```

## L4 Status message

A short sentence, announced politely to screen readers.

```xml
<p class='fx-status' role='status'>Finding more stories…</p>
```

```css
.fx-status { margin: 0; font-size: .9rem; color: var(--fx-muted, inherit); }
```

## L5 Content progress line

A thin line along the top edge of the region that is loading, not of the window.

```xml
<div class='fx-loadline' role='status'><span class='fx-vh'>Loading</span></div>
```

```css
.fx-loadline { position: relative; height: 3px; overflow: hidden; background: color-mix(in srgb, currentColor 14%, transparent); }
.fx-loadline::before { content: ''; position: absolute; inset: 0; width: 40%; background: currentColor; animation: fx-pageline 1.1s ease-in-out infinite; }
@media (prefers-reduced-motion: reduce) { .fx-loadline::before { animation: none; width: 100%; opacity: .5; } }
```

It reuses the `fx-pageline` keyframes from [N5](#n5-top-progress-line); copy that one `@keyframes` rule if N5 is not in the theme.

## L6 Skeletons with a real request

Grey shapes stand where content is about to arrive. Here they hold the place of “more posts with the same label”, fetched from the blog's own feed after a post has loaded. This is a real request, so the skeleton is honest.

Place the region inside the post view, after the post body. The script takes the label from the first label link on the page, the kind the [theme skeleton](blogger-theme.md#the-skeleton) prints with `rel='tag'`, so normally nothing else is needed. Only a theme that does not show a post's labels has to pass one in a `data-label` attribute, using the indexing form Blogger Help documents, inside the post markup and only when the post has labels: `<b:if cond='data:post.labels'>…expr:data-label='data:post.labels[0].name'…</b:if>`.

```xml
<!-- blogger -->
<b:if cond='data:view.isPost'>
  <section class='fx-related' hidden='hidden' aria-labelledby='fxRelatedTitle'>
    <h2 id='fxRelatedTitle'>Keep reading</h2>
    <ul class='fx-related__list'></ul>
  </section>
</b:if>
```

```css
.fx-related { margin-block: 3rem; }
ul.fx-related__list { display: grid; gap: 1rem; grid-template-columns: repeat(auto-fit, minmax(min(100%, 14rem), 1fr)); margin: 1rem 0 0; padding: 0; list-style: none; }
.fx-related__list > li { margin: 0; padding: 1rem; border: 1px solid color-mix(in srgb, currentColor 16%, transparent); border-radius: var(--fx-radius, .6rem); }
.fx-related__list > li { min-height: 5.5rem; }
.fx-related__list a { display: inline-flex; align-items: center; min-height: 2.75rem; font-weight: 600; text-decoration: none; }
.fx-related__list a:hover { text-decoration: underline; }
.fx-related__list a:focus-visible { text-decoration: underline; }
.fx-related__list time { display: block; margin-top: .4rem; font-size: .85rem; color: var(--fx-muted, inherit); }
.fx-skel { display: block; height: .9rem; border-radius: .3rem; background: linear-gradient(90deg, color-mix(in srgb, currentColor 10%, transparent) 25%, color-mix(in srgb, currentColor 20%, transparent) 37%, color-mix(in srgb, currentColor 10%, transparent) 63%) 0 0 / 400% 100%; animation: fx-skel 1.4s ease infinite; }
.fx-skel + .fx-skel { width: 60%; margin-top: .6rem; }
@keyframes fx-skel { from { background-position: 100% 0; } to { background-position: 0 0; } }
@media (prefers-reduced-motion: reduce) { .fx-skel { animation: none; } }
```

```js
(function () {
  var box = document.querySelector('.fx-related');
  if (!box || !window.fetch) return;
  var list = box.querySelector('.fx-related__list');
  var tagLink = document.querySelector('a[rel~="tag"][href*="/search/label/"], .post-labels a[href*="/search/label/"]');
  var label = box.getAttribute('data-label') || (tagLink ? decodeURIComponent(tagLink.getAttribute('href').split('/search/label/')[1].split(/[?#]/)[0]) : '');
  if (!list || !label) return;
  var here = window.location.pathname, max = 3;
  function item(children) { var li = document.createElement('li'); children.forEach(function (c) { li.appendChild(c); }); list.appendChild(li); return li; }
  function done() { list.removeAttribute('aria-busy'); }
  function fallback() {
    list.textContent = '';
    var a = document.createElement('a');
    a.href = '/search/label/' + encodeURIComponent(label);
    a.textContent = 'More in ' + label;
    item([a]); done();
  }
  box.hidden = false;
  list.setAttribute('aria-busy', 'true');
  for (var n = 0; n < max; n++) {
    var one = document.createElement('span'), two = document.createElement('span');
    one.className = two.className = 'fx-skel';
    item([one, two]).setAttribute('aria-hidden', 'true');
  }
  var ctrl = 'AbortController' in window ? new AbortController() : null;
  var timer = window.setTimeout(function () { if (ctrl) ctrl.abort(); }, 7000);
  window.fetch('/feeds/posts/summary/-/' + encodeURIComponent(label) + '?alt=json&max-results=' + (max + 1), ctrl ? { signal: ctrl.signal } : {})
    .then(function (res) { if (!res.ok) throw new Error('feed ' + res.status); return res.json(); })
    .then(function (data) {
      window.clearTimeout(timer);
      var entries = (data.feed && data.feed.entry) || [], shown = 0;
      list.textContent = '';
      entries.forEach(function (entry) {
        if (shown >= max) return;
        var href = '';
        (entry.link || []).forEach(function (l) { if (l.rel === 'alternate') href = l.href; });
        var url;
        try { url = new URL(href, window.location.href); } catch (err) { return; }
        if (url.origin !== window.location.origin || url.pathname === here) return;
        var a = document.createElement('a'), parts = [a];
        a.href = url.pathname;
        a.textContent = (entry.title && entry.title.$t) || 'Untitled post';
        var when = entry.published && entry.published.$t ? new Date(entry.published.$t) : null;
        if (when && !isNaN(when)) {
          var time = document.createElement('time');
          time.dateTime = entry.published.$t;
          var fmt = { year: 'numeric', month: 'long', day: 'numeric' };
          try { time.textContent = when.toLocaleDateString((document.documentElement.lang || '').replace('_', '-') || undefined, fmt); }
          catch (err) { time.textContent = when.toLocaleDateString(undefined, fmt); }
          parts.push(time);
        }
        item(parts); shown++;
      });
      if (shown) done(); else fallback();
    })
    .catch(function () { window.clearTimeout(timer); fallback(); });
})();
```

Why it is written this way:

- Titles go in with `textContent` and links are rebuilt from a parsed URL on the blog's own origin, so nothing from the feed is ever inserted as HTML.
- An empty result, a failed request and the seven-second limit all end in the same plain link to the label page.
- The request asks for one more post than it shows, because the current post is usually among the results.
- Dates are written in the blog's language, read from the page's `lang`, not in the visitor's.
- A feed can be switched off or shortened in the blog's settings. That is not an error to report to the reader; the fallback link covers it.

## G1 Sliding gallery

A row of pictures the reader slides by touch, trackpad, keyboard or the two buttons. It never moves on its own. Built on native scroll snapping, so it works with JavaScript off; the script only adds the buttons.

```xml
<section class='fx-gallery' aria-label='Photo gallery'>
  <ul class='fx-gallery__track' tabindex='0'>
    <li><figure><img alt='Harbour at low tide' src='https://example.com/1.jpg' width='1200' height='800' loading='lazy'/><figcaption>Low tide</figcaption></figure></li>
    <li><figure><img alt='Fishing nets drying' src='https://example.com/2.jpg' width='1200' height='800' loading='lazy'/><figcaption>Nets</figcaption></figure></li>
    <li><figure><img alt='A blue door' src='https://example.com/3.jpg' width='1200' height='800' loading='lazy'/><figcaption>Blue door</figcaption></figure></li>
  </ul>
  <div class='fx-gallery__nav' hidden='hidden'>
    <button class='icon-btn' type='button' data-dir='-1' aria-label='Previous pictures'><svg xmlns='http://www.w3.org/2000/svg' class='ico' aria-hidden='true' style='transform:scaleX(-1)'><use href='#i-arrow'/></svg></button>
    <button class='icon-btn' type='button' data-dir='1' aria-label='Next pictures'><svg xmlns='http://www.w3.org/2000/svg' class='ico' aria-hidden='true'><use href='#i-arrow'/></svg></button>
  </div>
</section>
```

```css
ul.fx-gallery__track { display: flex; gap: 1rem; margin: 0; padding: 0 0 .75rem; list-style: none; overflow-x: auto; scroll-snap-type: x mandatory; scroll-behavior: smooth; overscroll-behavior-x: contain; }
.fx-gallery__track > li { flex: 0 0 min(82%, 26rem); margin: 0; padding: 0; scroll-snap-align: start; }
.fx-gallery__track figure { margin: 0; }
.fx-gallery__track img { display: block; width: 100%; height: auto; border-radius: var(--fx-radius, .6rem); }
.fx-gallery__track:focus-visible { outline: 3px solid var(--fx-focus, currentColor); outline-offset: 3px; }
.fx-gallery__nav { display: flex; gap: .5rem; justify-content: flex-end; }
.fx-gallery__nav[hidden] { display: none; }
@media (prefers-reduced-motion: reduce) { ul.fx-gallery__track { scroll-behavior: auto; } }
```

```js
(function () {
  Array.prototype.forEach.call(document.querySelectorAll('.fx-gallery'), function (box) {
    var track = box.querySelector('.fx-gallery__track'), nav = box.querySelector('.fx-gallery__nav');
    if (!track || !nav) return;
    nav.hidden = false;
    nav.addEventListener('click', function (e) {
      var btn = e.target.closest ? e.target.closest('button[data-dir]') : null;
      if (btn) track.scrollBy({ left: Number(btn.getAttribute('data-dir')) * track.clientWidth * 0.85 });
    });
  });
})();
```

This recipe needs the [icon set](effects-interaction.md#i1-icon-set-and-styles): its sprite for the arrow and its `.ico` and `.icon-btn` rules. Without those rules an `svg` falls back to 300 px wide and breaks a phone layout. The track scrolls inside itself, so the page never scrolls sideways.

## G2 Slow carousel

The same gallery, advancing by itself every few seconds. Because it moves on its own it has a pause button, and it also pauses under the pointer, while focused, while touched, off screen and in a hidden tab. With reduced motion it does not start at all and is simply G1.

Add `data-auto='6000'` (milliseconds per step) to the G1 `section` and this button inside its `fx-gallery__nav`, before the arrows:

```xml
<button class='icon-btn fx-gallery__pause' type='button' aria-pressed='false'><span class='fx-gallery__pause-mark' aria-hidden='true'></span><span class='fx-vh'>Pause gallery</span></button>
```

```css
.fx-gallery__pause-mark { width: .7rem; height: .8rem; border-inline: .22rem solid currentColor; }
.fx-gallery__pause[aria-pressed='true'] .fx-gallery__pause-mark { width: 0; height: 0; border-block: .45rem solid transparent; border-inline: .75rem solid currentColor; border-inline-end-width: 0; }
```

```js
(function (fx) {
  fx.each('.fx-gallery[data-auto]', function (box) {
    var track = box.querySelector('.fx-gallery__track'), pause = box.querySelector('.fx-gallery__pause');
    if (!track) return;
    if (fx.still) { if (pause) pause.hidden = true; return; }
    var every = Math.max(parseInt(box.getAttribute('data-auto'), 10) || 6000, 4000), timer = 0, user = false, held = false, live = false;
    function step() {
      var end = track.scrollLeft + track.clientWidth >= track.scrollWidth - 4;
      if (end) track.scrollTo({ left: 0 }); else track.scrollBy({ left: track.clientWidth * 0.85 });
    }
    function sync() {
      window.clearInterval(timer);
      if (live && !user && !held) timer = window.setInterval(step, every);
    }
    ['pointerenter', 'focusin', 'touchstart'].forEach(function (type) { box.addEventListener(type, function () { held = true; sync(); }, { passive: true }); });
    ['pointerleave', 'focusout', 'touchend'].forEach(function (type) { box.addEventListener(type, function () { held = false; sync(); }, { passive: true }); });
    if (pause) pause.addEventListener('click', function () {
      user = !user;
      pause.setAttribute('aria-pressed', String(user));
      sync();
    });
    fx.live(box, function () { live = true; sync(); }, function () { live = false; sync(); });
  });
})(window.fx);
```

Counts as a continuous loop. Do not use it for the first screen's main image, and never for text the reader must finish.
