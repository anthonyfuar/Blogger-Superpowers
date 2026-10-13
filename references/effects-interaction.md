# Effect Recipes: Interaction and Icons

Read [the index and kit](effects.md) first. These recipes cover what happens when a reader points at, focuses, taps or presses something, plus the icon system. Hover effects are wrapped in `@media (hover: hover)` so a phone never shows a stuck hover state; every hover effect has a matching keyboard focus state.

## Contents

- Surfaces: [S1 buttons](#s1-button-interactions), [S2 lifting cards](#s2-lifting-cards), [S3 photo zoom](#s3-gentle-photo-zoom), [S4 glowing borders](#s4-glowing-borders), [S5 shimmering button](#s5-shimmering-button), [S6 reflective card](#s6-reflective-card), [S12 glass](#s12-glass-surface)
- Pointer: [S7 tilt](#s7-tilting-card), [S8 light](#s8-cursor-following-light), [S9 magnetic](#s9-magnetic-button), [S10 trail](#s10-cursor-trail), [S11 ripple](#s11-ripple)
- Icons: [I1 set and styles](#i1-icon-set-and-styles), [I2 motion](#i2-icon-motion), [I3 menu button](#i3-menu-button-morph)
- Utility: [U1 light and dark toggle](#u1-light-and-dark-toggle), [U2 follow the device setting](#u2-follow-the-device-setting)

## S1 Button interactions

The baseline for every theme, including “button interactions only”. A clear rest, hover, press and focus state; a touch target of at least 44 px.

```xml
<a class='fx-btn' href='/search'>Read the journal</a>
<button class='fx-btn fx-btn--quiet' type='button'>Subscribe</button>
```

```css
.fx-btn { display: inline-flex; align-items: center; justify-content: center; gap: .5em; min-height: 2.75rem; padding: .7em 1.25em; border: 2px solid transparent; border-radius: var(--fx-radius, .6rem); background: var(--fx-btn-bg, #1c1917); color: var(--fx-btn-ink, #fff); font: inherit; font-weight: 600; line-height: 1.2; text-decoration: none; cursor: pointer; transition: transform .18s var(--fx-ease, ease), box-shadow .18s var(--fx-ease, ease), background-color .18s, color .18s, border-color .18s; }
.fx-btn--quiet { background: transparent; color: inherit; border-color: currentColor; }
@media (hover: hover) {
  .fx-btn:hover { transform: translateY(-2px); box-shadow: 0 .6rem 1.2rem -.6rem rgba(0, 0, 0, .45); }
  .fx-btn--quiet:hover { background: var(--fx-btn-bg, #1c1917); color: var(--fx-btn-ink, #fff); border-color: transparent; }
}
.fx-btn:active { transform: translateY(0) scale(.98); box-shadow: none; }
.fx-btn:focus-visible { outline: 3px solid var(--fx-focus, currentColor); outline-offset: 3px; }
```

**Still variant, for a “no motion” brief.** Keep the first, second and last rules above, delete the `transition` from the first, and replace the hover and press rules with a change of color only:

```css
/* alt: still */
@media (hover: hover) {
  .fx-btn:hover { background: var(--fx-btn-hover, #44403c); }
  .fx-btn--quiet:hover { background: var(--fx-btn-bg, #1c1917); color: var(--fx-btn-ink, #fff); border-color: transparent; }
}
.fx-btn:active { background: var(--fx-btn-hover, #44403c); }
```

A still theme needs nothing else from this file except the icon set.

With reduced motion the color change stays and the movement goes. Making a transition instant is not enough, because the element would still jump to its moved position, so the second rule removes the movement itself. The third rule is for the mirrored “previous” arrow of [I2](#i2-icon-motion): its mirroring is a transform too, and without that rule the arrow would turn to point forward under the pointer. Add this once per theme that has any motion, and add to its second rule any hover or focus movement the theme defines itself:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after { animation-duration: .01ms !important; animation-iteration-count: 1 !important; transition-duration: .01ms !important; scroll-behavior: auto !important; }
  .fx-btn:hover, .fx-btn:active, .fx-lift:hover, .fx-lift:focus-within, .fx-zoom:hover img, .fx-zoom:focus-visible img, .fx-lift:hover .fx-zoom img, a:hover > .ico, button:hover > .ico, a:focus-visible > .ico, button:focus-visible > .ico, a:active > .ico, button:active > .ico { transform: none !important; }
  a:hover > .ico--back, button:hover > .ico--back, a:focus-visible > .ico--back, button:focus-visible > .ico--back, a:active > .ico--back, button:active > .ico--back { transform: scaleX(-1) !important; }
}
```

## S2 Lifting cards

A card rises slightly under the pointer and when a link inside it has focus.

```xml
<article class='card fx-lift'>
  <h3><a href='/2026/05/a-post.html'>The long way home</a></h3>
  <p>Three days on the coast road.</p>
</article>
```

```css
.fx-lift { transition: transform .3s var(--fx-ease, ease), box-shadow .3s var(--fx-ease, ease); }
@media (hover: hover) {
  .fx-lift:hover { transform: translateY(-6px); box-shadow: 0 1.4rem 2rem -1.2rem rgba(0, 0, 0, .35); }
}
.fx-lift:focus-within { transform: translateY(-6px); box-shadow: 0 1.4rem 2rem -1.2rem rgba(0, 0, 0, .35); }
```

For a design with no shadows, keep the lift and replace both `box-shadow` values with a change of `border-color`. A card can be a staggered-entrance child (E3) and lift on hover: the kit lets go of the card once it has entered. To make the whole card one tap target without nesting links, stretch the title link: `.card { position: relative; } .card h3 a::after { content: ''; position: absolute; inset: 0; }`.

## S3 Gentle photo zoom

The image grows a little inside its fixed frame. The frame clips it, so the layout never moves.

```xml
<a class='card__media fx-zoom' href='/2026/05/a-post.html'>
  <img alt='' src='https://example.com/coast.jpg' width='1200' height='800' loading='lazy'/>
</a>
```

```css
.fx-zoom { display: block; overflow: hidden; border-radius: var(--fx-radius, .6rem); }
.fx-zoom img { display: block; width: 100%; transition: transform .8s var(--fx-ease, ease); }
@media (hover: hover) { .fx-zoom:hover img, .fx-lift:hover .fx-zoom img { transform: scale(1.06); } }
.fx-zoom:focus-visible img { transform: scale(1.06); }
```

The recipe sets no height on the image, so the frame's own rule decides it: `height: 100%` with `object-fit: cover` in a fixed-ratio frame such as the skeleton's `card__media`, and the base skin's `height: auto` anywhere else. Do not add `height: auto` here: on a fixed-ratio frame it would leave a strip of the frame's background under every photo that is not exactly that ratio. Inside a card that already rounds its corners, set `--fx-radius: 0` on the frame.

## S4 Glowing borders

A light travels around the edge of a card or button. One or two per view: a featured card, the main call to action.

```xml
<div class='fx-glow'>
  <h3>This week</h3>
  <p>The market before sunrise.</p>
</div>
```

```css
@property --fx-angle { syntax: '<angle>'; inherits: false; initial-value: 0deg; }
.fx-glow { position: relative; isolation: isolate; padding: 1.5rem; border-radius: var(--fx-radius, 1rem); background: var(--fx-glow-surface, #fff); }
.fx-glow::before {
  content: ''; position: absolute; inset: -2px; z-index: -1; padding: 2px; border-radius: inherit; pointer-events: none;
  background: conic-gradient(from var(--fx-angle), var(--fx-glow-rest, rgba(0, 0, 0, .12)) 0 65%, var(--fx-glow, #f59e0b) 85%, var(--fx-glow-rest, rgba(0, 0, 0, .12)) 100%);
  -webkit-mask: linear-gradient(#000 0 0) content-box, linear-gradient(#000 0 0); -webkit-mask-composite: xor;
  mask: linear-gradient(#000 0 0) content-box exclude, linear-gradient(#000 0 0);
}
.fx-on .fx-glow::before { animation: fx-angle 5s linear infinite; }
@keyframes fx-angle { to { --fx-angle: 360deg; } }
```

Without motion, and in browsers without `@property`, it is a plain two-tone border. The ring is drawn 2 px outside the element, so the element itself must not have `overflow: hidden`; to clip a picture inside a glowing card, put the clipping on an inner wrapper. A skeleton card clips its photo itself and has no padding of its own, so a glowing card built on it needs these two rules as well:

```css
.card.fx-glow { overflow: visible; padding: 0; border-radius: var(--radius, 1rem); }
.card.fx-glow .card__media { overflow: hidden; border-radius: var(--radius, 1rem) var(--radius, 1rem) 0 0; }
```

Use the theme's own card class names in place of `card` and `card__media`, and its own radius token in place of `--radius`. These rules name the card's radius themselves, so they hold when the photo frame also carries [S3](#s3-gentle-photo-zoom) with `--fx-radius: 0`. On a card that lays its photo beside the text on wide screens, round the photo's outer corners for that layout instead of its top corners.

Loop: counts against the limit of three.

## S5 Shimmering button

A highlight sweeps across the button every few seconds. For the single most important action on the page. Loop: counts against the limit of three.

```xml
<a class='fx-btn fx-shimmer' href='/p/subscribe.html'>Get the newsletter</a>
```

```css
.fx-shimmer { position: relative; overflow: hidden; }
.fx-shimmer::after { content: ''; position: absolute; inset: 0; pointer-events: none; background: linear-gradient(110deg, transparent 35%, rgba(255, 255, 255, .45) 50%, transparent 65%); transform: translateX(-120%); }
.fx-on .fx-shimmer::after { animation: fx-shimmer 3.6s var(--fx-ease, ease) infinite; }
@keyframes fx-shimmer { 0%, 60% { transform: translateX(-120%); } 100% { transform: translateX(120%); } }
```

## S6 Reflective card

A streak of light crosses the card once when the pointer arrives, like light on a glossy print.

```xml
<article class='card fx-glare'>
  <h3>Printed matter</h3>
</article>
```

```css
.fx-glare { position: relative; overflow: hidden; }
.fx-glare::after { content: ''; position: absolute; inset: 0; pointer-events: none; background: linear-gradient(115deg, transparent 40%, rgba(255, 255, 255, .35) 50%, transparent 60%); transform: translateX(-130%); transition: transform .9s var(--fx-ease, ease); }
@media (hover: hover) { .fx-glare:hover::after { transform: translateX(130%); } }
.fx-glare:focus-within::after { transform: translateX(130%); }
```

## S12 Glass surface

A translucent panel that blurs what is behind it: a sticky header, a card over a photograph. Always keep a solid enough fallback color so the text stays readable where blur is unsupported.

```xml
<div class='fx-glass'>
  <p>Open daily, 8 to 4.</p>
</div>
```

```css
.fx-glass { padding: 1rem 1.25rem; border: 1px solid rgba(255, 255, 255, .35); border-radius: var(--fx-radius, 1rem); background: rgba(255, 255, 255, .86); }
@supports ((-webkit-backdrop-filter: blur(1px)) or (backdrop-filter: blur(1px))) {
  .fx-glass { background: rgba(255, 255, 255, .55); -webkit-backdrop-filter: blur(14px) saturate(1.4); backdrop-filter: blur(14px) saturate(1.4); }
}
```

Measure text contrast against the lightest and darkest thing that can sit behind the panel.

## S7 Tilting card

The card leans toward the pointer. Mouse and trackpad only; it never runs on touch or with reduced motion.

```xml
<article class='card fx-tilt' data-tilt='7'>
  <h3>Postcards</h3>
</article>
```

```css
.fx-tilt { transition: transform .25s var(--fx-ease, ease); will-change: transform; }
```

```js
(function (fx) {
  if (!fx.on || !fx.fine) return;
  fx.each('.fx-tilt', function (el) {
    var max = parseFloat(el.getAttribute('data-tilt')) || 7, frame = 0, px = 0, py = 0;
    el.addEventListener('pointermove', function (e) {
      px = e.clientX; py = e.clientY;
      if (frame) return;
      frame = window.requestAnimationFrame(function () {
        frame = 0;
        var r = el.getBoundingClientRect(), x = (px - r.left) / r.width - 0.5, y = (py - r.top) / r.height - 0.5;
        el.style.transform = 'perspective(900px) rotateX(' + (-y * max).toFixed(2) + 'deg) rotateY(' + (x * max).toFixed(2) + 'deg)';
      });
    });
    el.addEventListener('pointerleave', function () { el.style.transform = ''; });
  });
})(window.fx);
```

`data-tilt` is the largest angle in degrees. Stay at 10 or below; text on a steep tilt is hard to read. The script owns this element's `transform`, so do not give the same element `data-fx`, `fx-lift` or `fx-magnet`; when a tilting card is also a staggered-entrance child, wrap it in a plain `div` and stagger the wrapper.

## S8 Cursor-following light

A soft glow follows the pointer across a card or a whole section.

```xml
<section class='fx-spot'>
  <h2>Workshops</h2>
  <p>Small groups, long tables.</p>
</section>
```

```css
.fx-spot { position: relative; isolation: isolate; overflow: hidden; }
.fx-spot::before { content: ''; position: absolute; inset: 0; z-index: -1; pointer-events: none; opacity: 0; transition: opacity .4s; background: radial-gradient(18rem circle at var(--fx-mx, 50%) var(--fx-my, 50%), var(--fx-spot, rgba(245, 158, 11, .28)), transparent 65%); }
.fx-spot.is-lit::before { opacity: 1; }
```

```js
(function (fx) {
  if (!fx.on || !fx.fine) return;
  fx.each('.fx-spot', function (el) {
    var frame = 0, px = 0, py = 0;
    el.addEventListener('pointermove', function (e) {
      px = e.clientX; py = e.clientY;
      if (frame) return;
      frame = window.requestAnimationFrame(function () {
        frame = 0;
        var r = el.getBoundingClientRect();
        el.style.setProperty('--fx-mx', (px - r.left) + 'px');
        el.style.setProperty('--fx-my', (py - r.top) + 'px');
        el.classList.add('is-lit');
      });
    });
    el.addEventListener('pointerleave', function () { el.classList.remove('is-lit'); });
  });
})(window.fx);
```

## S9 Magnetic button

A button drifts a few pixels toward the pointer and springs back.

```xml
<a class='fx-btn fx-magnet' href='/p/contact.html'>Say hello</a>
```

```css
.fx-magnet { transition: transform .35s var(--fx-spring, ease); }
.fx-magnet.is-pulled { transition-duration: .12s; transition-timing-function: linear; }
```

```js
(function (fx) {
  if (!fx.on || !fx.fine) return;
  fx.each('.fx-magnet', function (el) {
    var pull = parseFloat(el.getAttribute('data-pull')) || 0.3;
    el.addEventListener('pointermove', function (e) {
      var r = el.getBoundingClientRect();
      var x = (e.clientX - (r.left + r.width / 2)) * pull, y = (e.clientY - (r.top + r.height / 2)) * pull;
      el.classList.add('is-pulled');
      el.style.transform = 'translate(' + x.toFixed(1) + 'px,' + y.toFixed(1) + 'px)';
    });
    el.addEventListener('pointerleave', function () { el.classList.remove('is-pulled'); el.style.transform = ''; });
  });
})(window.fx);
```

Do not combine with S1's hover lift on the same element: the script owns its `transform`.

## S10 Cursor trail

A short tail of dots follows the pointer and fades. It does not replace the cursor. Homepage only, and never when the brief says “no custom cursor”.

```css
.fx-trail { position: fixed; top: 0; left: 0; z-index: 60; width: .5rem; height: .5rem; margin: -.25rem 0 0 -.25rem; border-radius: 50%; background: var(--fx-trail, #f59e0b); pointer-events: none; opacity: 0; will-change: transform; }
```

```js
(function (fx) {
  if (!fx.on || !fx.fine) return;
  var count = 8, dots = [], x = 0, y = 0, frame = 0, idle = 0;
  for (var i = 0; i < count; i++) {
    var dot = document.createElement('span');
    dot.className = 'fx-trail'; dot.setAttribute('aria-hidden', 'true');
    document.body.appendChild(dot);
    dots.push({ el: dot, x: 0, y: 0 });
  }
  function step() {
    var tx = x, ty = y;
    dots.forEach(function (p, n) {
      p.x += (tx - p.x) * 0.35; p.y += (ty - p.y) * 0.35;
      p.el.style.transform = 'translate(' + p.x.toFixed(1) + 'px,' + p.y.toFixed(1) + 'px) scale(' + (1 - n / count).toFixed(2) + ')';
      p.el.style.opacity = idle > 40 ? 0 : (0.7 * (1 - n / count)).toFixed(2);
      tx = p.x; ty = p.y;
    });
    frame = ++idle < 90 ? window.requestAnimationFrame(step) : 0;
  }
  window.addEventListener('pointermove', function (e) {
    if (e.pointerType && e.pointerType !== 'mouse') return;
    x = e.clientX; y = e.clientY; idle = 0;
    if (!frame) frame = window.requestAnimationFrame(step);
  }, { passive: true });
})(window.fx);
```

The loop stops by itself about a second after the pointer rests.

## S11 Ripple

A circle spreads from the point of a press. This one also runs on touch, because it answers the tap itself.

```xml
<button class='fx-btn fx-ripple' type='button'>Load more</button>
```

```css
.fx-ripple { position: relative; overflow: hidden; }
.fx-ripple__ink { position: absolute; border-radius: 50%; background: currentColor; opacity: .25; transform: scale(0); pointer-events: none; animation: fx-ripple .6s ease-out forwards; }
@keyframes fx-ripple { to { transform: scale(1); opacity: 0; } }
```

```js
(function (fx) {
  if (!fx.on) return;
  document.addEventListener('pointerdown', function (e) {
    var el = e.target.closest ? e.target.closest('.fx-ripple') : null;
    if (!el) return;
    var r = el.getBoundingClientRect(), size = Math.max(r.width, r.height) * 2, ink = document.createElement('span');
    ink.className = 'fx-ripple__ink'; ink.setAttribute('aria-hidden', 'true');
    ink.style.cssText = 'width:' + size + 'px;height:' + size + 'px;left:' + (e.clientX - r.left - size / 2) + 'px;top:' + (e.clientY - r.top - size / 2) + 'px';
    el.appendChild(ink);
    function drop() { if (ink.parentNode) ink.parentNode.removeChild(ink); }
    ink.addEventListener('animationend', drop);
    window.setTimeout(drop, 900);
  });
})(window.fx);
```

## I1 Icon set and styles

Icons are inline SVG, drawn on a 24-unit grid with strokes that take the text color. Define the set once as a hidden sprite directly after the opening body tag, then reference an icon anywhere with `use`. Include only the symbols the theme shows. These shapes are original to this skill.

```xml
<svg class='fx-sprite' aria-hidden='true' focusable='false' xmlns='http://www.w3.org/2000/svg'>
  <symbol id='i-menu' viewBox='0 0 24 24'><path pathLength='1' d='M4 7h16M4 12h16M4 17h16'/></symbol>
  <symbol id='i-close' viewBox='0 0 24 24'><path pathLength='1' d='M6 6l12 12M18 6L6 18'/></symbol>
  <symbol id='i-search' viewBox='0 0 24 24'><circle pathLength='1' cx='11' cy='11' r='6.5'/><path pathLength='1' d='M16 16l4.5 4.5'/></symbol>
  <symbol id='i-arrow' viewBox='0 0 24 24'><path pathLength='1' d='M4 12h15M13 6l6 6-6 6'/></symbol>
  <symbol id='i-arrow-up' viewBox='0 0 24 24'><path pathLength='1' d='M7 17L17 7M8 7h9v9'/></symbol>
  <symbol id='i-chevron' viewBox='0 0 24 24'><path pathLength='1' d='M6 9l6 6 6-6'/></symbol>
  <symbol id='i-heart' viewBox='0 0 24 24'><path pathLength='1' d='M12 20s-7-4.4-7-10a4 4 0 0 1 7-2.6A4 4 0 0 1 19 10c0 5.6-7 10-7 10z'/></symbol>
  <symbol id='i-bookmark' viewBox='0 0 24 24'><path pathLength='1' d='M7 4h10v16l-5-3.5L7 20z'/></symbol>
  <symbol id='i-share' viewBox='0 0 24 24'><circle pathLength='1' cx='6' cy='12' r='2.2'/><circle pathLength='1' cx='17.5' cy='6' r='2.2'/><circle pathLength='1' cx='17.5' cy='18' r='2.2'/><path pathLength='1' d='M8 11l7.5-4M8 13l7.5 4'/></symbol>
  <symbol id='i-mail' viewBox='0 0 24 24'><rect pathLength='1' x='3.5' y='5.5' width='17' height='13' rx='2'/><path pathLength='1' d='M4 7l8 6 8-6'/></symbol>
  <symbol id='i-sun' viewBox='0 0 24 24'><circle pathLength='1' cx='12' cy='12' r='4'/><path pathLength='1' d='M12 2.5V5M12 19v2.5M2.5 12H5M19 12h2.5M5.3 5.3l1.8 1.8M16.9 16.9l1.8 1.8M5.3 18.7l1.8-1.8M16.9 7.1l1.8-1.8'/></symbol>
  <symbol id='i-moon' viewBox='0 0 24 24'><path pathLength='1' d='M20 14.5A8 8 0 0 1 9.5 4 8 8 0 1 0 20 14.5z'/></symbol>
  <symbol id='i-rss' viewBox='0 0 24 24'><circle pathLength='1' cx='6' cy='18' r='1.6'/><path pathLength='1' d='M4.5 11a8.5 8.5 0 0 1 8.5 8.5M4.5 5A14.5 14.5 0 0 1 19 19.5'/></symbol>
  <symbol id='i-clock' viewBox='0 0 24 24'><circle pathLength='1' cx='12' cy='12' r='8.5'/><path pathLength='1' d='M12 7.5V12l3 2'/></symbol>
  <symbol id='i-tag' viewBox='0 0 24 24'><path pathLength='1' d='M3.5 12.5v-8h8l9 9-8 8z'/><circle pathLength='1' cx='8' cy='9' r='1.3'/></symbol>
  <symbol id='i-comment' viewBox='0 0 24 24'><path pathLength='1' d='M4.5 5.5h15V16H11l-4.5 3.5V16h-2z'/></symbol>
  <symbol id='i-bell' viewBox='0 0 24 24'><path pathLength='1' d='M6.5 16.5V11a5.5 5.5 0 0 1 11 0v5.5l1.5 1.5H5zM10 20.5a2 2 0 0 0 4 0'/></symbol>
  <filter id='i-rough'><feTurbulence type='fractalNoise' baseFrequency='.8' numOctaves='1' seed='4' result='n'/><feDisplacementMap in='SourceGraphic' in2='n' scale='1.3'/></filter>
</svg>
```

Using one. A decorative icon beside text is hidden from assistive technology; an icon-only control gets a text name. Each `svg` carries the SVG namespace, as all inline SVG in a template does:

```xml
<a class='more' href='/search'>All stories <svg xmlns='http://www.w3.org/2000/svg' class='ico ico--nudge' aria-hidden='true'><use href='#i-arrow'/></svg></a>
<button class='icon-btn' type='button' aria-label='Search'><svg xmlns='http://www.w3.org/2000/svg' class='ico' aria-hidden='true'><use href='#i-search'/></svg></button>
```

```css
.fx-sprite { position: absolute; width: 0; height: 0; overflow: hidden; }
.ico { width: 1.25em; height: 1.25em; flex: none; vertical-align: -.22em; fill: none; stroke: currentColor; stroke-width: var(--ico-w, 1.75); stroke-linecap: var(--ico-cap, round); stroke-linejoin: var(--ico-join, round); }
.icon-btn { display: inline-grid; place-items: center; min-width: 2.75rem; min-height: 2.75rem; padding: 0; border: 0; border-radius: 50%; background: none; color: inherit; cursor: pointer; }
.icon-btn:focus-visible { outline: 3px solid var(--fx-focus, currentColor); outline-offset: 2px; }
.icon-btn[hidden] { display: none; }
```

The interview's icon look is a few tokens on `:root`, so the whole set changes together. The user may pick several looks; they combine, because each row sets a different thing. Weight rows set only the stroke width, shape rows set only the line ends and corners, and the last three are classes.

| Icon look | What it sets |
|---|---|
| Thin outlines | `--ico-w: 1.25` |
| Bold outlines | `--ico-w: 2.25` |
| Neither thin nor bold chosen | leave `--ico-w` at its default, 1.75 |
| Rounded and friendly | `--ico-cap: round; --ico-join: round` |
| Sharp and geometric | `--ico-cap: butt; --ico-join: miter` |
| Minimal symbols | use only arrow, chevron, menu, close and search |
| Solid shapes | add class `ico--solid` |
| Two-color | add class `ico--duo` and set `--ico-accent` |
| Playful hand-drawn | add class `ico--rough` |

“Bold outlines” with “rounded and friendly” is `--ico-w: 2.25; --ico-cap: round; --ico-join: round`. “Thin” with “sharp” is `--ico-w: 1.25; --ico-cap: butt; --ico-join: miter`.

```css
.ico--solid { fill: currentColor; stroke-width: 1; }
.ico--duo { fill: color-mix(in srgb, var(--ico-accent, currentColor) 28%, transparent); }
.ico--rough { filter: url(#i-rough); stroke-width: 2; }
```

Solid suits closed shapes (heart, bookmark, moon, tag, comment, bell); keep open shapes such as arrows as outlines. To add an icon, draw it on the same 24-unit grid with 1.75-unit strokes in mind and give every shape `pathLength='1'`. A user's own logo or icon files can be pasted as further symbols.

## I2 Icon motion

Small movements that confirm an action. Each is a class on the `svg`. They trigger from the nearest link or button, so the whole control is the target, and from keyboard focus as well as the pointer.

```css
.ico { transition: transform .25s var(--fx-ease, ease); }
@media (hover: hover) {
  a:hover > .ico--nudge, button:hover > .ico--nudge { transform: translateX(.25em); }
  a:hover > .ico--rise, button:hover > .ico--rise { transform: translate(.15em, -.15em); }
  a:hover > .ico--turn, button:hover > .ico--turn { transform: rotate(90deg); }
  a:hover > .ico--back, button:hover > .ico--back { transform: translateX(-.25em) scaleX(-1); }
  a:hover > .ico--ring, button:hover > .ico--ring { animation: fx-ring .7s var(--fx-ease, ease); transform-origin: 50% 10%; }
}
a:focus-visible > .ico--nudge, button:focus-visible > .ico--nudge { transform: translateX(.25em); }
a:focus-visible > .ico--rise, button:focus-visible > .ico--rise { transform: translate(.15em, -.15em); }
a:focus-visible > .ico--turn, button:focus-visible > .ico--turn { transform: rotate(90deg); }
.ico--back { transform: scaleX(-1); }
a:focus-visible > .ico--back, button:focus-visible > .ico--back { transform: translateX(-.25em) scaleX(-1); }
a:active > .ico--pop, button:active > .ico--pop { transform: scale(.8); }
button[aria-pressed='true'] > .ico--pop { fill: currentColor; animation: fx-pop .45s var(--fx-spring, ease); }
@keyframes fx-ring { 20% { transform: rotate(14deg); } 45% { transform: rotate(-11deg); } 70% { transform: rotate(6deg); } 100% { transform: none; } }
@keyframes fx-pop { 0% { transform: scale(.6); } 100% { transform: none; } }
```

| Behavior the user chose | Class and where |
|---|---|
| When pointed at / focused | `ico--nudge` on arrows, `ico--back` on the same arrow used for “previous” (it points and nudges left), `ico--rise` on the up-right arrow, `ico--turn` on plus or chevron, `ico--ring` on the bell |
| When tapped | `ico--pop` on heart or bookmark inside a `button` that toggles `aria-pressed` |
| When a menu opens | [I3](#i3-menu-button-morph) |
| Gently when they first appear | the draw-in below |
| Only important actions | apply a class to the main call to action and nowhere else |
| Never | use `.ico` alone |

Draw-in: the icon's lines draw themselves once as it scrolls into view. It works through `use` because every shape in the sprite has `pathLength='1'`.

```xml
<span class='feature__icon' data-fx='icon'><svg xmlns='http://www.w3.org/2000/svg' class='ico' aria-hidden='true'><use href='#i-mail'/></svg></span>
```

```css
.fx-on [data-fx="icon"] { opacity: 1; }
.fx-on [data-fx="icon"] .ico { stroke-dasharray: 1; stroke-dashoffset: 1; transition: stroke-dashoffset 1s var(--fx-ease, ease) .15s; }
.fx-on [data-fx="icon"].is-in .ico { stroke-dashoffset: 0; }
```

## I3 Menu button morph

Three bars turn into a cross while the menu is open. The button, its styles, the open state and the script are all in [responsive](responsive.md#navigation-that-works-on-a-phone), where the change is instant. This one rule makes it a smooth turn:

```css
.fx-menu__bars i { transition: transform .3s var(--fx-ease, ease), opacity .2s; }
```

## U1 Light and dark toggle

For “light and dark with a toggle”. The page follows the device setting until the reader chooses; the choice is remembered. Define every color as a token so one attribute switches the whole theme. It needs three things from elsewhere: the sun and moon symbols and the `.icon-btn` rules from [I1](#i1-icon-set-and-styles), and `.fx-vh` from the [base skin](responsive.md#navigation-that-works-on-a-phone). Put the button in the header beside the menu button.

These lines go in the head so the stored choice applies before the first paint. Put them at the top of the head script the theme already has (the kit's gate or the menu gate), or in a script of their own directly before it:

```xml
<script>
//<![CDATA[
(function (d) {
  try {
    var t = window.localStorage.getItem('theme');
    if (t === 'dark' || t === 'light') d.documentElement.setAttribute('data-theme', t);
  } catch (e) {}
})(document);
//]]>
</script>
```

```css
:root { color-scheme: light dark; --bg: #fafaf9; --ink: #1c1917; --accent: #b45309; }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme='light']) { --bg: #0c0a09; --ink: #f5f5f4; --accent: #fbbf24; }
}
:root[data-theme='dark'] { color-scheme: dark; --bg: #0c0a09; --ink: #f5f5f4; --accent: #fbbf24; }
:root[data-theme='light'] { color-scheme: light; }
body { background: var(--bg); color: var(--ink); }
.fx-theme .ico--sun, [data-theme='dark'] .fx-theme .ico--moon { display: none; }
[data-theme='dark'] .fx-theme .ico--sun { display: inline; }
@media (prefers-color-scheme: dark) {
  :root:not([data-theme='light']) .fx-theme .ico--moon { display: none; }
  :root:not([data-theme='light']) .fx-theme .ico--sun { display: inline; }
}
```

The button is hidden until the script runs, because it does nothing without it:

```xml
<button class='icon-btn fx-theme' type='button' aria-pressed='false' hidden='hidden'>
  <svg xmlns='http://www.w3.org/2000/svg' class='ico ico--moon' aria-hidden='true'><use href='#i-moon'/></svg>
  <svg xmlns='http://www.w3.org/2000/svg' class='ico ico--sun' aria-hidden='true'><use href='#i-sun'/></svg>
  <span class='fx-vh'>Dark theme</span>
</button>
```

```js
(function () {
  var root = document.documentElement, btn = document.querySelector('.fx-theme');
  if (!btn) return;
  function isDark() {
    var t = root.getAttribute('data-theme');
    return t ? t === 'dark' : !!window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches;
  }
  btn.hidden = false;
  btn.setAttribute('aria-pressed', String(isDark()));
  btn.addEventListener('click', function () {
    var next = isDark() ? 'light' : 'dark';
    root.setAttribute('data-theme', next);
    btn.setAttribute('aria-pressed', String(next === 'dark'));
    try { window.localStorage.setItem('theme', next); } catch (e) {}
  });
})();
```

The dark values appear twice in the CSS on purpose: once for the device setting and once for the stored choice. Keep both lists identical. Check contrast in both themes, and check embedded comments, which keep their own colors.

When a theme that followed the device ([U2](#u2-follow-the-device-setting)) gains this button, every other rule the theme wrote inside `@media (prefers-color-scheme: dark)` needs the same twin under `[data-theme='dark']`, and the media version needs `:root:not([data-theme='light'])` in front. Print rules must reset the tokens for `:root[data-theme]` as well, or a reader who chose dark prints dark colors; [R5](effects-navigation-loading.md#r5-print) does this.

## U2 Follow the device setting

For “follow the reader's device setting”: light and dark, chosen by the device, with no button and no script. Define every color once as a token and give the dark set in one media query.

```xml
<meta content='light dark' name='color-scheme'/>
```

```css
/* alt: device */
:root { color-scheme: light dark; --bg: #faf8f4; --surface: #ffffff; --ink: #242424; --muted: #5f5a52; --line: #dcd6ca; --accent: #9c3324; }
@media (prefers-color-scheme: dark) {
  :root { --bg: #1a1917; --surface: #24221f; --ink: #e9e4da; --muted: #ada598; --line: #37342e; --accent: #e3a08e; }
}
body { background: var(--bg); color: var(--ink); }
```

Put the `meta` tag in the head so form fields and scrollbars follow as well. Never write a color outside these tokens, or it will be wrong in one of the two modes.

**Deriving the second mode.** The interview's palettes give one background, one text color and one accent. A theme needs more roles, and two modes need all of them twice:

| Role | Light mode | Dark mode |
|---|---|---|
| `--bg` | the palette's background | a near-black with a hint of the palette's hue, lightness 8 to 12% |
| `--surface` (cards, fields) | a step lighter or darker than `--bg` | a step lighter than the dark `--bg` |
| `--ink` | the palette's text color | the palette's light background, slightly dimmed |
| `--muted` (dates, captions) | `--ink` mixed toward `--bg` until it is still 4.5:1 on `--bg` | the same test against the dark `--bg` |
| `--line` (rules, borders) | `--ink` at about 15% over `--bg` | `--ink` at about 18% over the dark `--bg` |
| `--accent` | the palette's accent | the same hue made lighter until it is 4.5:1 on the dark `--bg` |

For a palette that is already dark, derive the light mode the same way in reverse. Compute every text pair with the [contrast formula](design.md#review-without-installed-tools) in both modes before using it; do not estimate. Embedded comment forms are always light: give the comments wrapper a fixed light background and dark text, as plain colors and not tokens, so the form does not float on a dark page.

