# Effect Recipes: Entrances and Text

Read [the index and kit](effects.md) first. Every recipe here assumes the kit's head gate, styles and script are in the theme. Recipes are hardcoded CSS, SVG and JavaScript unless marked CDN. Replace colors, sizes and class names of the surrounding layout with the design plan's values.

## Contents

- Entrances: [E1 fade](#e1-gentle-fade-in), [E2 reveal on scroll](#e2-reveal-on-scroll), [E3 stagger](#e3-staggered-entrances), [E4 spring](#e4-gentle-spring), [E5 image reveal](#e5-image-reveal), [E6 hero sequence with an engine](#e6-hero-sequence-with-an-engine)
- Text: [T1 letters](#t1-letter-by-letter-text), [T2 changing words](#t2-changing-words), [T3 waving](#t3-waving-letters), [T4 circular](#t4-circular-text), [T5 ribbon](#t5-ribbon-text), [T6 cylinder](#t6-cylindrical-text), [T7 stacking](#t7-stacking-text), [T8 count-up](#t8-count-up-number), [T9 underline and lines](#t9-drawing-underline-and-lines), [T10 textured and shining](#t10-textured-and-shining-text)

## E1 Gentle fade-in

Add `data-fx='fade'` to any block. The kit fades it in once, when it enters the viewport. Use it on section intros, cards and images; never on the post body.

```xml
<p class='intro' data-fx='fade'>Field notes from small kitchens and slow mornings.</p>
```

Tune one element with inline custom properties: `style='--fx-dur:1s'`.

## E2 Reveal on scroll

The same attribute with a direction. `up` is the safe default. `left` and `right` turn into `up` on phones automatically.

```xml
<h2 data-fx='up'>Latest stories</h2>
<figure data-fx='zoom'><img alt='' src='https://example.com/photo.jpg' width='1200' height='800' loading='lazy'/></figure>
<aside data-fx='left'>…</aside>
<blockquote data-fx='blur'>…</blockquote>
```

Values: `fade`, `up`, `down`, `left`, `right`, `zoom`, `blur`, `spring`, `reveal`. A section that uses `left` or `right` needs `overflow-x: clip` on its wrapper so the shifted start position cannot widen the page:

```css
.fx-clip { overflow-x: clip; }
```

## E3 Staggered entrances

Put `data-fx-group` on the parent. Its direct children enter one after another. The value is the entrance each child gets; children that already have `data-fx` keep their own.

```xml
<div class='cards' data-fx-group='up'>
  <article class='card'>…</article>
  <article class='card'>…</article>
  <article class='card'>…</article>
</div>
```

The delay between children is `--fx-gap` (80ms). The kit caps the delay at the ninth child, so a long post list never waits. In a Blogger post loop, put the attribute on the element that directly wraps the looped `article` elements. Once a child has entered, the kit lets go of it, so a card's own hover lift or photo zoom works as soon as its entrance is over. A child that an effect moves by script (a tilting card, a magnetic button) is the exception: wrap it in a plain `div` and let the wrapper be the group's child.

## E4 Gentle spring

`data-fx='spring'` settles with a small overshoot. Use it for cards and buttons, not for text blocks.

```xml
<div class='cards' data-fx-group='spring'>
  <article class='card'>…</article>
  <article class='card'>…</article>
</div>
```

The kit uses a `linear()` spring curve where the browser supports it and an overshoot bezier elsewhere.

## E5 Image reveal

The frame opens like a blind while the image settles from a slight zoom. Put the attribute on the wrapper, not on the `img`: the kit clips the wrapper's direct child, because an element that is itself clipped to nothing never reports that it has scrolled into view. Always give the image its real `width` and `height` so the space is reserved.

```xml
<figure class='fx-frame' data-fx='reveal'>
  <img alt='A ceramic bowl on a linen cloth' src='https://example.com/bowl.jpg' width='1200' height='800' loading='lazy'/>
</figure>
```

```css
.fx-frame { margin: 0; overflow: hidden; }
.fx-frame img { display: block; width: 100%; height: auto; }
.fx-on [data-fx="reveal"] > img { transform: scale(1.12); }
.fx-on [data-fx="reveal"].is-in > img { transform: none; }
.fx-on [data-fx="reveal"].fx-from-left:not(.is-in) > * { clip-path: inset(0 100% 0 0); }
.fx-on [data-fx="reveal"].fx-from-center:not(.is-in) > * { clip-path: inset(0 50% 0 50%); }
```

Add `class='fx-frame fx-from-left'` or `fx-from-center` to change the direction. On the first screen it is fine for smaller pictures the user asked to reveal, such as two or three tiles in a welcome area. Do not put it on a full-width hero photograph: that is the page's most important paint and should simply be there.

## E6 Hero sequence with an engine

**CDN.** Use this only when the brief asks for a coordinated opening: several elements entering in a choreographed order. For anything simpler, E3 does the job with no download. Load [one engine](effects.md#engines-from-a-cdn), mark the hero's parts with `data-hero` in the order they should appear, and do not also give them `data-fx`.

```xml
<header class='hero'>
  <p class='hero__kicker' data-hero='1'>Issue 14</p>
  <h1 class='hero__title' data-hero='2'>Quiet places, loud colors</h1>
  <p class='hero__lead' data-hero='3'>A journal about cities after dark.</p>
  <a class='fx-btn' data-hero='4' href='/search'>Read the issue</a>
</header>
```

This rule hides the parts only until the script has decided what to do, so there is no flash. The script always adds `fx-go`, with or without the engine, which is what keeps the hero visible when the CDN is blocked. A CDN that stalls instead of failing would hold the page's ready event back, so the first lines start a timer that shows the hero after 2.5 seconds whatever happens; if the engine arrives later than that, the opening is simply skipped.

```css
.fx-on:not(.fx-go) [data-hero] { opacity: 0; }
```

With GSAP:

```js
(function (fx) {
  var root = document.documentElement, late = false;
  var net = window.setTimeout(function () { late = true; root.classList.add('fx-go'); }, 2500);
  fx.ready(function () {
    var parts = document.querySelectorAll('[data-hero]');
    window.clearTimeout(net);
    root.classList.add('fx-go');
    if (late || !parts.length || fx.still || !window.gsap) return;
    window.gsap.from(parts, { y: 28, autoAlpha: 0, duration: 0.8, ease: 'power3.out', stagger: 0.12, clearProps: 'all' });
  });
})(window.fx);
```

With Anime.js instead (never both):

```js
/* alt: anime */
(function (fx) {
  var root = document.documentElement, late = false;
  var net = window.setTimeout(function () { late = true; root.classList.add('fx-go'); }, 2500);
  fx.ready(function () {
    var parts = document.querySelectorAll('[data-hero]');
    window.clearTimeout(net);
    root.classList.add('fx-go');
    if (late || !parts.length || fx.still || !window.anime || !window.anime.animate) return;
    window.anime.animate(parts, { opacity: [0, 1], translateY: [28, 0], duration: 800, delay: window.anime.stagger(120), ease: 'outCubic' });
  });
})(window.fx);
```

GSAP can also split the title into letters with its SplitText plugin. Load `SplitText.min.js` after the core, leave `data-hero` off the title so it is never hidden, and add this block after the one above:

```js
/* alt: splittext */
fx.ready(function () {
  var title = document.querySelector('.hero__title');
  if (!title || !fx.on || !window.gsap || !window.SplitText) return;
  window.gsap.registerPlugin(window.SplitText);
  var split = window.SplitText.create(title, { type: 'words,chars' });
  window.gsap.from(split.chars, { yPercent: 60, autoAlpha: 0, duration: 0.6, ease: 'power3.out', stagger: 0.02 });
});
```

## T1 Letter-by-letter text

Letters rise in one after another. For a heading of up to about forty characters that contains plain text only (no links or inner tags). The script keeps words unbroken and sets an `aria-label`, so the heading still reads as one phrase. On a hero headline this delays the page's main text by a fraction of a second; that is the effect the user asked for, and the head gate's timer guarantees the text appears even if the script never runs.

```xml
<h1 class='hero__title' data-fx='letters'>Stories worth keeping</h1>
```

```css
.fx-w { display: inline-block; white-space: nowrap; }
.fx-ch { display: inline-block; }
.fx-on [data-fx="letters"] { transition: none; }
.fx-on [data-fx="letters"].fx-split { opacity: 1; }
.fx-on [data-fx="letters"] .fx-ch { opacity: 0; transform: translateY(.5em); transition: opacity .5s var(--fx-ease, ease), transform .5s var(--fx-ease, ease); transition-delay: calc(var(--i) * 28ms); }
.fx-on [data-fx="letters"].is-in .fx-ch { opacity: 1; transform: none; }
```

```js
(function (fx) {
  if (!fx.on) return;
  fx.each('[data-fx="letters"]', function (el) { fx.split(el); });
})(window.fx);
```

## T2 Changing words

One word in a sentence swaps through a short list, then rests. Write the first word in the markup so the sentence is complete without JavaScript. Keep every word short: the script reserves the width of the longest one.

```xml
<h1>Notes on <span class='fx-words' data-words='slow travel|good food|quiet places'>slow travel</span></h1>
```

```css
.fx-words { display: inline-block; max-width: 100%; white-space: nowrap; }
.fx-on .fx-words { transition: opacity .35s var(--fx-ease, ease), transform .35s var(--fx-ease, ease); }
.fx-words.is-out { opacity: 0; transform: translateY(.35em); }
```

```js
(function (fx) {
  if (!fx.on) return;
  fx.each('.fx-words[data-words]', function (el) {
    var words = el.getAttribute('data-words').split('|').map(function (s) { return s.trim(); }).filter(Boolean);
    if (words.length < 2) return;
    var loops = parseInt(el.getAttribute('data-loops'), 10) || 2, i = 0, done = 0, timer = 0;
    var probe = el.cloneNode(false), widest = 0;
    probe.style.cssText = 'position:absolute;visibility:hidden';
    el.parentNode.appendChild(probe);
    words.forEach(function (word) { probe.textContent = word; widest = Math.max(widest, probe.getBoundingClientRect().width); });
    el.parentNode.removeChild(probe);
    el.style.minWidth = Math.ceil(widest) + 'px';
    function next() {
      el.classList.add('is-out');
      window.setTimeout(function () {
        i = (i + 1) % words.length;
        el.textContent = words[i];
        el.classList.remove('is-out');
        if (i === 0 && ++done >= loops) window.clearInterval(timer);
      }, 360);
    }
    fx.live(el, function () { if (done < loops) timer = window.setInterval(next, 2600); }, function () { window.clearInterval(timer); });
  });
})(window.fx);
```

It runs through the list twice and stops on the first word. Set `data-loops='1'` for a single pass. Do not make it endless: text that keeps changing beside the content needs a pause control.

## T3 Waving letters

Each letter lifts in turn, three times, then rests. For one short, playful heading.

```xml
<h2 class='fx-wave'>Hello there</h2>
```

```css
.fx-on .fx-wave .fx-ch { animation: fx-wave 2.6s ease-in-out 3; animation-delay: calc(var(--i) * 70ms); }
@keyframes fx-wave { 0%, 60%, 100% { transform: none; } 30% { transform: translateY(-.18em); } }
```

```js
(function (fx) {
  if (!fx.on) return;
  fx.each('.fx-wave', function (el) { fx.split(el); });
})(window.fx);
```

T1 and T3 share the `.fx-w` and `.fx-ch` rules from T1; include those two lines when using T3 alone. It stops by itself, so it does not count as a loop.

## T4 Circular text

Words set on a circle that turns slowly: a badge, a seal, a stamp beside the hero. Pure SVG, so it stays sharp at any size. The ring is decoration; say the same words in real text nearby, or give the `svg` a `role='img'` and an `aria-label` and drop `aria-hidden`.

```xml
<div class='fx-ring' aria-hidden='true'>
  <svg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'>
    <defs><path id='fxRingPath' d='M100 100m-80 0a80 80 0 1 1 160 0a80 80 0 1 1 -160 0'/></defs>
    <text><textPath href='#fxRingPath' textLength='496' lengthAdjust='spacing'>Slow stories · Field notes · Since 2019 ·</textPath></text>
  </svg>
</div>
```

```css
.fx-ring { width: clamp(7rem, 22vw, 11rem); aspect-ratio: 1; color: inherit; }
.fx-ring svg { display: block; width: 100%; height: 100%; overflow: visible; }
.fx-ring text { fill: currentColor; font-size: 15px; font-weight: 600; letter-spacing: .12em; text-transform: uppercase; }
.fx-on .fx-ring svg { animation: fx-spin 26s linear infinite; }
```

`textLength='496'` spreads the words around the full circle (the path is 503 units long). Keep the phrase between 30 and 50 characters and end it with a separator. The text takes the page's font; set `font-family` on `.fx-ring text` to change it.

Loop: counts against the [limit of three](effects.md#limits-and-combinations). Because the ring is decoration that repeats words already on the page, it needs no pause button; the kit pauses it off screen. For a still badge, leave out the last CSS line. As it turns, the corners of its square box swing about a fifth of its width past the circle: keep that much room to the page edge, or give its section `overflow-x: clip`.

## T5 Ribbon text

A band of short phrases that slides sideways without end. Write the phrases once. The script copies them as many times as the screen needs, so the band never shows a gap however short the list is, and it sets the speed from the band's length. The band has a pause button, because these are words a reader may want to finish.

```xml
<div class='fx-ribbon'>
  <div class='fx-ribbon__track'>
    <span>New essays every Sunday</span><span>Field notes</span><span>Seasonal recipes</span><span>Reader letters</span>
  </div>
  <button class='fx-pause' type='button' aria-pressed='false' hidden='hidden'><span class='fx-vh'>Pause the moving text</span></button>
</div>
```

```css
.fx-ribbon { position: relative; overflow: hidden; padding-block: .75rem; }
.fx-ribbon__track { display: flex; flex-wrap: wrap; gap: .25rem 0; }
.fx-ribbon__track > * { padding-inline-end: 2.5rem; white-space: nowrap; }
.fx-ribbon.is-live { padding-inline-end: 2.75rem; }
.fx-ribbon.is-live .fx-ribbon__track { flex-wrap: nowrap; width: max-content; animation: fx-ribbon var(--fx-ribbon-time, 30s) linear infinite; }
.fx-ribbon.is-live:hover .fx-ribbon__track, .fx-ribbon.is-live:focus-within .fx-ribbon__track { animation-play-state: paused; }
.fx-ribbon .fx-pause { position: absolute; inset-block: 0; inset-inline-end: 0; background: inherit; }
@keyframes fx-ribbon { to { transform: translateX(-50%); } }
```

```js
(function (fx) {
  if (!fx.on) return;
  fx.each('.fx-ribbon', function (box) {
    var track = box.querySelector('.fx-ribbon__track');
    if (!track || !track.children.length) return;
    function copy(nodes) {
      nodes.forEach(function (n) {
        var c = n.cloneNode(true);
        c.setAttribute('aria-hidden', 'true');
        Array.prototype.forEach.call(c.querySelectorAll('a, button'), function (x) { x.tabIndex = -1; });
        if (c.matches('a, button')) c.tabIndex = -1;
        track.appendChild(c);
      });
    }
    var set = Array.prototype.slice.call(track.children), need = Math.max(box.clientWidth, window.screen ? window.screen.width : 0) * 1.1, guard = 0;
    box.classList.add('is-live');
    while (track.scrollWidth < need && guard++ < 40) copy(set);
    copy(Array.prototype.slice.call(track.children));
    track.style.setProperty('--fx-ribbon-time', Math.max(16, Math.round(track.scrollWidth / 2 / 48)) + 's');
  });
})(window.fx);
```

Without motion or without JavaScript the phrases simply wrap as a normal row and the button stays hidden. The band moves at about 48 pixels a second whatever its length; change the `48` to change the speed.

Loop: counts against the [limit of three](effects.md#limits-and-combinations). Give the band a background color so the pause button, which inherits it, covers the text sliding under it.

## T6 Cylindrical text

One word repeated around a turning drum. Decorative only: use it beside a real heading that says the same thing. Set `--n` to the number of copies.

```xml
<div class='fx-cyl' aria-hidden='true' style='--n:8'>
  <div class='fx-cyl__drum'>
    <span style='--i:0'>WANDER</span><span style='--i:1'>WANDER</span><span style='--i:2'>WANDER</span><span style='--i:3'>WANDER</span>
    <span style='--i:4'>WANDER</span><span style='--i:5'>WANDER</span><span style='--i:6'>WANDER</span><span style='--i:7'>WANDER</span>
  </div>
</div>
```

```css
.fx-cyl { height: 2.4em; font-size: clamp(2rem, 9vw, 5rem); font-weight: 800; line-height: 1; overflow: hidden; }
.fx-cyl__drum { position: relative; height: 100%; }
.fx-cyl__drum > span { display: none; }
.fx-cyl__drum > span:first-child { display: grid; place-items: center; height: 100%; }
.fx-on .fx-cyl { height: 3.6em; overflow: visible; overflow-x: clip; perspective: 9em; }
.fx-on .fx-cyl__drum { transform-style: preserve-3d; animation: fx-cyl 14s linear infinite; }
.fx-on .fx-cyl__drum > span, .fx-on .fx-cyl__drum > span:first-child { position: absolute; inset: 0; display: grid; place-items: center; height: auto; backface-visibility: hidden; transform: rotateX(calc(var(--i) * 360deg / var(--n))) translateZ(1.25em); }
@keyframes fx-cyl { to { transform: rotateX(-360deg); } }
```

Without motion it shows the word once. Keep the word to about eight letters so it fits a phone at this size.

Loop: counts against the limit of three.

## T7 Stacking text

Outlined copies fan out behind a heading as it arrives. The copies come from the `data-text` attribute, which must repeat the heading's text exactly.

```xml
<h2 class='fx-stack' data-fx='fade' data-text='OUT NOW'>OUT NOW</h2>
```

```css
.fx-stack { position: relative; display: inline-block; isolation: isolate; --fx-stack-line: currentColor; }
.fx-stack::before, .fx-stack::after {
  content: attr(data-text); content: attr(data-text) / "";
  position: absolute; inset: 0; z-index: -1; pointer-events: none;
  -webkit-text-fill-color: transparent; -webkit-text-stroke: 1px var(--fx-stack-line);
  opacity: .55; transition: transform var(--fx-dur, .7s) var(--fx-ease, ease) .15s;
}
.fx-stack::after { opacity: .3; }
.fx-stack.is-in::before, html:not(.fx-on) .fx-stack::before { transform: translate(.07em, .07em); }
.fx-stack.is-in::after, html:not(.fx-on) .fx-stack::after { transform: translate(.14em, .14em); }
```

The second `content` line gives the copies an empty text alternative, so a screen reader reads the heading once.

## T8 Count-up number

A figure counts from zero to its value when it scrolls into view. Write the final, formatted value in the markup; that is what readers without motion see.

```xml
<p class='stat'><span class='fx-count' data-count='12840'>12,840</span> readers each month</p>
```

```css
.fx-count { font-variant-numeric: tabular-nums; }
```

```js
(function (fx) {
  if (!fx.on) return;
  fx.each('.fx-count[data-count]', function (el) {
    var end = parseFloat(el.getAttribute('data-count')), places = parseInt(el.getAttribute('data-decimals'), 10) || 0;
    if (!isFinite(end)) return;
    var finalText = el.textContent, lang = (document.documentElement.lang || '').replace('_', '-') || undefined, opts = { minimumFractionDigits: places, maximumFractionDigits: places };
    var show = function (v) { try { return v.toLocaleString(lang, opts); } catch (e) { return v.toLocaleString(undefined, opts); } };
    fx.seen(el, function () {
      var t0 = 0, dur = 1400;
      function tick(now) {
        if (!t0) t0 = now;
        var p = Math.min((now - t0) / dur, 1), eased = 1 - Math.pow(1 - p, 3);
        el.textContent = p < 1 ? show(end * eased) : finalText;
        if (p < 1) window.requestAnimationFrame(tick);
      }
      window.requestAnimationFrame(tick);
    });
  });
})(window.fx);
```

Keep units and symbols outside the span (`<span …>98</span>%`). While it counts, digits are grouped the way the blog's language does it, taken from the page's `lang`; the final text is exactly what the markup says. Use real numbers the owner supplied; never invent statistics for a blog.

## T9 Drawing underline and lines

An underline that draws itself under a link on hover and focus. It works on links that wrap onto two lines. On touch screens, where there is no hover, the underline is simply shown.

```xml
<a class='fx-underline' href='/p/about.html'>About this journal</a>
```

```css
.fx-underline { text-decoration: none; padding-bottom: .12em; background: linear-gradient(currentColor, currentColor) 0 100% / 0 2px no-repeat; transition: background-size .45s var(--fx-ease, ease); -webkit-box-decoration-break: clone; box-decoration-break: clone; }
.fx-underline:hover { background-size: 100% 2px; }
.fx-underline:focus-visible { background-size: 100% 2px; }
@media (hover: none) { .fx-underline { background-size: 100% 2px; } }
```

Use this on links that are text: card titles, “read more” links, links in a footer. Do not add it to links inside post bodies, which stay underlined all the time so they can be told from the text around them.

A menu item is different: it is a box 44 px high for touch, and a line at the bottom of that box would sit far below the words. For menu items and other tall single-line links, draw the line with a pseudo-element placed under the text instead:

```css
@media (min-width: 48em) {
  .site-nav a { position: relative; text-decoration: none; }
  .site-nav a::after { content: ''; position: absolute; left: 0; right: 0; bottom: .55rem; height: 2px; background: currentColor; transform: scaleX(0); transform-origin: 0 50%; transition: transform .35s var(--fx-ease, ease); }
  .site-nav a:focus-visible::after { transform: scaleX(1); }
  .site-nav .is-current > a::after { transform: scaleX(1); }
}
@media (min-width: 48em) and (hover: hover) {
  .site-nav a:hover::after { transform: scaleX(1); }
}
```

It applies from the menu breakpoint up, where each item is as wide as its words; use the theme's own breakpoint in both queries. In the phone menu, where items are full-width rows, the plain underline of the [phone menu](responsive.md#navigation-that-works-on-a-phone) rules stays.

A hand-drawn line that draws under one word when it scrolls into view:

```xml
<h2>Everything here is <span class='fx-squiggle' data-fx='draw'>handmade<svg viewBox='0 0 120 12' preserveAspectRatio='none' aria-hidden='true' xmlns='http://www.w3.org/2000/svg'><path d='M2 8Q12 2 22 8T42 8T62 8T82 8T102 8T118 8' pathLength='1'/></svg></span></h2>
```

```css
.fx-squiggle { position: relative; display: inline-block; white-space: nowrap; }
.fx-squiggle svg { position: absolute; left: 0; bottom: -.3em; width: 100%; height: .45em; overflow: visible; }
.fx-squiggle path { fill: none; stroke: currentColor; stroke-width: 2.5; stroke-linecap: round; vector-effect: non-scaling-stroke; stroke-dasharray: 1; stroke-dashoffset: 0; }
.fx-on [data-fx="draw"] { opacity: 1; }
.fx-on [data-fx="draw"] path { stroke-dashoffset: 1; transition: stroke-dashoffset .9s var(--fx-ease, ease) .2s; }
.fx-on [data-fx="draw"].is-in path { stroke-dashoffset: 0; }
```

`pathLength='1'` is what lets one rule draw any path: the dash is measured against a length of one. The same pattern draws any decorative line, divider or illustration outline.

With Anime.js loaded, the engine can draw several paths in sequence instead:

```js
/* alt: anime */
fx.ready(function () {
  var paths = document.querySelectorAll('.fx-sketch path');
  if (!paths.length || fx.still || !window.anime || !window.anime.svg) return;
  window.anime.animate(window.anime.svg.createDrawable(paths), { draw: ['0 0', '0 1'], duration: 1400, delay: window.anime.stagger(140), ease: 'inOutQuad' });
});
```

## T10 Textured and shining text

A gradient or an image shows through the letters. The plain `color` is the fallback and is what high-contrast modes and print use.

```xml
<h1 class='fx-texture fx-texture--move'>Golden hour</h1>
```

```css
.fx-texture { color: var(--fx-texture-ink, #9a3412); }
@supports ((-webkit-background-clip: text) or (background-clip: text)) {
  .fx-texture { background: var(--fx-texture, linear-gradient(110deg, #9a3412, #f59e0b 35%, #7c2d12 70%, #f59e0b)); background-size: 220% 100%; -webkit-background-clip: text; background-clip: text; -webkit-text-fill-color: transparent; }
  .fx-on .fx-texture--move { animation: fx-texture 9s ease-in-out infinite alternate; }
}
@keyframes fx-texture { to { background-position: 100% 0; } }
@media (forced-colors: active), print { .fx-texture { background: none; -webkit-text-fill-color: currentColor; } }
```

Loop, when `fx-texture--move` is used: counts against the limit of three. Without that class the fill is still.

For a photographic texture set `--fx-texture: url(https://example.com/marble.jpg) center / cover` on the element. Check the contrast of the darkest and lightest part of the fill against the page background; a texture does not excuse unreadable text.

A band of light that crosses the letters now and then, for one short line such as a tagline:

```xml
<p class='fx-shine'>New issue out today</p>
```

```css
.fx-shine { color: var(--fx-shine-ink, #57534e); }
@supports ((-webkit-background-clip: text) or (background-clip: text)) {
  .fx-on .fx-shine { background: linear-gradient(100deg, var(--fx-shine-ink, #57534e) 40%, var(--fx-shine-light, #fff) 50%, var(--fx-shine-ink, #57534e) 60%) 120% 0 / 250% 100% no-repeat; -webkit-background-clip: text; background-clip: text; -webkit-text-fill-color: transparent; animation: fx-shine 4.5s var(--fx-ease, ease) infinite; }
}
@keyframes fx-shine { 0%, 45% { background-position: 120% 0; } 100% { background-position: -20% 0; } }
@media (forced-colors: active), print { .fx-on .fx-shine { background: none; -webkit-text-fill-color: currentColor; animation: none; } }
```
