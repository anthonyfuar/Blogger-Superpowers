# Effects: Index, Kit and Tool Routes

This is the entry point for every animation, icon, parallax, 3D, background, transition and loading choice the interview can offer. Each choice maps to a recipe with working code. Open this file whenever a brief selects any effect, find the choice in the [index](#choice-index), and open only the recipe files that index points to.

A Blogger theme is one static XML file. Nothing here needs a server, a build step, npm, React, Tailwind or a compiler. Every recipe is one of two things:

- **Hardcoded**: plain CSS, inline SVG and vanilla JavaScript written directly into the XML. This is the default and covers almost every choice.
- **CDN**: one pinned, original browser file loaded by a script tag, with its integrity hash. Used only where an engine earns its weight.

If a choice has no recipe here, do not improvise a library. Say so, offer the nearest recipe, and settle it with the user.

## Contents

- [Rules for every effect](#rules-for-every-effect)
- [The kit](#the-kit)
- [Choice index](#choice-index)
- [Tool routes](#tool-routes)
- [Engines from a CDN](#engines-from-a-cdn)
- [Limits and combinations](#limits-and-combinations)
- [Checking an effect](#checking-an-effect)

Recipe files: [text and entrances](effects-text.md), [interaction and icons](effects-interaction.md), [backgrounds, depth and 3D](effects-backgrounds-3d.md), [navigation and loading](effects-navigation-loading.md). Layout rules live in [responsive](responsive.md).

## Rules for every effect

1. **Content first.** Text, images and links are visible and usable with JavaScript off, with a blocked CDN, and with reduced motion. An effect only ever adds to a finished page. Hidden start states exist only under the `fx-on` class, which the kit sets after it has confirmed that motion is allowed.
2. **Reduced motion is a real mode.** With `prefers-reduced-motion: reduce` the kit never sets `fx-on`, scripts return early, and loops do not start. Do not shorten an animation and call it reduced.
3. **Phones get less.** Pointer effects run only with a fine pointer and hover. Scroll-linked depth and the Spline scene are off below 48em unless the user asked to keep them; a canvas draws less there and nothing with data saver on. Horizontal entrances become vertical on phones so nothing widens the page. Any loop can be held still on phones with the class `fx-wide`.
4. **One script, one kit.** All inline JavaScript goes into a single comment-guarded block just before the closing body tag, in this order: the [phone menu](responsive.md#navigation-that-works-on-a-phone) block, then the kit, then each selected recipe, then blocks that need neither, such as the table wrapper. The menu comes first so that nothing later can break it. One head gate, one body block.
5. **One engine at most.** Prefer the hardcoded recipe. Load GSAP or Anime.js only when the brief needs a coordinated timeline, and never both.
6. **Guard every target.** Homepage-only elements do not exist on post, page, search or error views. Every recipe looks its elements up and does nothing when they are missing.
7. **Loops are quiet and stoppable.** A decorative loop (a background, a turning shape, a ring of repeated words) is hidden from assistive technology, never carries meaning, and pauses by itself when it is off screen or the tab is hidden: the kit does this for every loop in these files. Readable content that keeps moving for more than five seconds (a ribbon of phrases, a carousel) also has a visible pause button. Text that changes by itself stops after a few rounds.
8. **An effect on the first screen delays what it animates.** That is the price of a hero entrance, and it is fine when the user chose one. Keep it under a second, never hide the page's main photograph behind it, and never let it wait on a download: every recipe that hides something releases it on a timer.
9. **Name the source honestly.** A hardcoded recipe is original code. Say “inspired by” a project only when the user picked that project; never say a theme “uses” a library whose code is not in the XML.

### Writing recipe code into Blogger XML

- CSS goes inside `b:skin`. A `$` there means a Blogger [designer variable](blogger-theme.md#designer-variables) and nothing else: no recipe uses one, and a stray `$` makes Blogger reject the theme.
- JavaScript goes inside comment-guarded CDATA, so `<`, `>` and `&&` need no escaping there. See [XML contexts](blogger-language.md#xml-and-escaping-contexts).
- Markup in the template is XML: quote every attribute, close every element (`<img … />`, `<br/>`), give boolean attributes a value (`hidden='hidden'`), and write `&amp;` for an ampersand. Markup inside an HTML gadget's CDATA is ordinary HTML.
- Inline SVG in the template carries `xmlns='http://www.w3.org/2000/svg'`. Decorative SVG gets `aria-hidden='true'`; meaningful SVG gets `role='img'` and an `aria-label`.
- Element ids must be unique on the page. Rename the ids in a recipe when it is used twice.
- Keep the `fx-` class names as they are: the kit finds its loops and buttons by them. The theme's own components get the theme's prefix.
- Colors, sizes and timings in recipes are placeholders. Replace them with the design plan's tokens; keep the structure.

## The kit

Add the kit once whenever at least one effect from these files is selected. A “no motion” brief adds none of it.

**Head gate.** Place directly after the closing `b:skin` tag. It marks that JavaScript runs, and turns motion on only when the reader allows it. The two timeouts are safety nets: if the body script never arrives, the phone menu's links are shown again and hidden start states are released after three seconds. This gate replaces the smaller one in the [responsive reference](responsive.md#navigation-that-works-on-a-phone); a theme has one or the other, never both.

```xml
<script>
//<![CDATA[
(function (d, w) {
  var c = d.documentElement.classList;
  c.add('js');
  w.setTimeout(function () { if (!c.contains('menu-ready')) c.remove('js'); }, 3000);
  if (!w.matchMedia || w.matchMedia('(prefers-reduced-motion: reduce)').matches || !('IntersectionObserver' in w)) return;
  c.add('fx-on');
  w.setTimeout(function () { if (!c.contains('fx-ready')) c.remove('fx-on'); }, 3000);
})(document, window);
//]]>
</script>
```

**Kit styles.** These provide every scroll entrance through one attribute, `data-fx`.

```css
:root {
  --fx-dur: .7s;
  --fx-ease: cubic-bezier(.2, .7, .2, 1);
  --fx-spring: cubic-bezier(.34, 1.56, .64, 1);
  --fx-shift: 1.5rem;
  --fx-gap: 80ms;
}
@supports (transition-timing-function: linear(0, 1)) {
  :root { --fx-spring: linear(0, .009, .035 2.1%, .141, .281 6.7%, .723 12.9%, .938 16.7%, 1.017, 1.077, 1.121, 1.149 24.3%, 1.159, 1.163, 1.161, 1.154 29.9%, 1.129 32.8%, 1.051 39.6%, 1.017 43.1%, .991, .977 51%, .974 53.8%, .975 57.1%, .997 69.8%, 1.003 76.9%, 1.004 83.8%, 1); }
}
.fx-on [data-fx] {
  opacity: 0;
  transition: opacity var(--fx-dur) var(--fx-ease), transform var(--fx-dur) var(--fx-ease), filter var(--fx-dur) var(--fx-ease);
  transition-delay: calc(var(--fx-i, 0) * var(--fx-gap));
}
.fx-on [data-fx="up"] { transform: translateY(var(--fx-shift)); }
.fx-on [data-fx="down"] { transform: translateY(calc(var(--fx-shift) * -1)); }
.fx-on [data-fx="left"] { transform: translateX(var(--fx-shift)); }
.fx-on [data-fx="right"] { transform: translateX(calc(var(--fx-shift) * -1)); }
.fx-on [data-fx="zoom"] { transform: scale(.94); }
.fx-on [data-fx="blur"] { filter: blur(10px); }
.fx-on [data-fx="spring"] { transform: translateY(var(--fx-shift)) scale(.96); transition-timing-function: var(--fx-spring); transition-duration: calc(var(--fx-dur) * 1.3); }
.fx-on [data-fx].is-in { opacity: 1; transform: none; filter: none; }
.fx-on [data-fx="reveal"] { opacity: 1; }
.fx-on [data-fx="reveal"] > * { clip-path: inset(0 0 100% 0); transition: clip-path calc(var(--fx-dur) * 1.4) var(--fx-ease), transform calc(var(--fx-dur) * 2) var(--fx-ease); }
.fx-on [data-fx="reveal"].is-in > * { clip-path: inset(0); }
@media (max-width: 47.99em) {
  .fx-on [data-fx="left"], .fx-on [data-fx="right"] { transform: translateY(var(--fx-shift)); }
}
.fx-vh { position: absolute; width: 1px; height: 1px; margin: -1px; overflow: hidden; clip-path: inset(50%); white-space: nowrap; }
@keyframes fx-spin { to { transform: rotate(360deg); } }
/* A paused loop: set by the kit when the loop is off screen or the tab is hidden, and by a pause button. */
.fx-paused, .fx-paused::before, .fx-paused::after, .fx-paused *:not(.fx-ripple__ink), .fx-paused *::before, .fx-paused *::after { animation-play-state: paused !important; }
/* fx-wide on a looping element switches its motion off on phones ("simpler on phones"). */
@media (max-width: 47.99em) {
  .fx-wide, .fx-wide::before, .fx-wide::after, .fx-wide *:not(.fx-ripple__ink), .fx-wide *::before, .fx-wide *::after { animation: none !important; }
}
.fx-pause { display: inline-grid; place-items: center; min-width: 2.75rem; min-height: 2.75rem; padding: 0; border: 0; background: none; color: inherit; cursor: pointer; }
.fx-pause[hidden] { display: none; }
.fx-pause::before { content: ''; width: .7rem; height: .8rem; border-inline: .22rem solid currentColor; }
.fx-pause[aria-pressed='true']::before { width: 0; height: 0; border-block: .45rem solid transparent; border-inline: .75rem solid currentColor; border-inline-end-width: 0; }
.fx-pause:focus-visible { outline: 3px solid currentColor; outline-offset: 2px; }
/* Paper has no motion: print everything, including what has not scrolled into view yet. */
@media print {
  .fx-on [data-fx], .fx-on [data-fx] *, .fx-on [data-hero] { opacity: 1 !important; transform: none !important; filter: none !important; clip-path: none !important; -webkit-mask-image: none !important; mask-image: none !important; transition: none !important; animation: none !important; }
}
```

**Kit script.** This goes in the single body script, after the phone menu block and before any recipe. It exposes one small object, `fx`, that the recipes read.

```js
window.fx = (function (w, d) {
  var mq = function (q) { return !!w.matchMedia && w.matchMedia(q).matches; };
  var fx = {
    on: d.documentElement.classList.contains('fx-on'),
    still: mq('(prefers-reduced-motion: reduce)'),
    fine: mq('(hover: hover) and (pointer: fine)'),
    phone: mq('(max-width: 47.99em)'),
    lite: !!(navigator.connection && navigator.connection.saveData),
    each: function (sel, fn, ctx) { Array.prototype.forEach.call((ctx || d).querySelectorAll(sel), fn); },
    ready: function (fn) { if (d.readyState === 'loading') d.addEventListener('DOMContentLoaded', fn); else fn(); },
    // Run fn once when el first enters the viewport.
    seen: function (el, fn, margin) {
      if (!('IntersectionObserver' in w)) { fn(el); return; }
      var io = new IntersectionObserver(function (es) {
        es.forEach(function (e) { if (e.isIntersecting) { io.disconnect(); fn(el); } });
      }, { rootMargin: margin || '0px 0px -10% 0px', threshold: 0 });
      io.observe(el);
    },
    // Call start() while el is on screen and the tab is visible, stop() otherwise.
    live: function (el, start, stop) {
      var on = null, vis = false;
      function sync() { var want = vis && !d.hidden; if (want === on) return; on = want; (on ? start : stop)(); }
      if (!('IntersectionObserver' in w)) { vis = true; sync(); return; }
      new IntersectionObserver(function (es) { vis = es[es.length - 1].isIntersecting; sync(); }).observe(el);
      d.addEventListener('visibilitychange', sync);
    },
    // Wrap each letter in a span, keeping words unbroken and the text readable to assistive technology.
    split: function (el) {
      if (el.fxChars) return el.fxChars;
      if (el.children.length) return [];
      var text = el.textContent.replace(/\s+/g, ' ').trim(), chars = [], n = 0;
      el.setAttribute('aria-label', text);
      el.textContent = '';
      text.split(' ').forEach(function (word, wi, all) {
        var ws = d.createElement('span'); ws.className = 'fx-w'; ws.setAttribute('aria-hidden', 'true');
        Array.from(word).forEach(function (ch) {
          var s = d.createElement('span'); s.className = 'fx-ch'; s.textContent = ch; s.style.setProperty('--i', n++);
          ws.appendChild(s); chars.push(s);
        });
        el.appendChild(ws);
        if (wi < all.length - 1) el.appendChild(d.createTextNode(' '));
      });
      el.classList.add('fx-split');
      return (el.fxChars = chars);
    }
  };
  var root = d.documentElement;
  if (root.classList.contains('fx-on')) {
    fx.each('[data-fx-group]', function (g) {
      var kind = g.getAttribute('data-fx-group') || 'up';
      Array.prototype.forEach.call(g.children, function (c, i) {
        if (!c.hasAttribute('data-fx')) c.setAttribute('data-fx', kind);
        c.style.setProperty('--fx-i', Math.min(i, 8));
      });
    });
    fx.each('[data-fx]', function (el) {
      fx.seen(el, function () {
        el.classList.add('is-in');
        // When the entrance is over, let go of the element so its own hover, focus and transition rules apply again.
        var delay = (parseFloat(w.getComputedStyle(el).transitionDelay) || 0) * 1000;
        w.setTimeout(function () { el.removeAttribute('data-fx'); el.style.removeProperty('--fx-i'); }, delay + 2600);
      });
    });
    // Every loop from these recipes, and anything marked data-fx-loop, pauses off screen and in a hidden tab.
    fx.each('.fx-ring, .fx-ribbon, .fx-cyl, .fx-texture--move, .fx-shine, .fx-glow, .fx-shimmer, .fx-flow, .fx-aurora, .fx-mesh--drift, .fx-stars, .fx-rays, .fx-grid, .fx-orbit, .fx-3d, [data-fx-loop]', function (el) {
      fx.live(el, function () { if (!el.fxHeld) el.classList.remove('fx-paused'); }, function () { el.classList.add('fx-paused'); });
    });
    // A pause button stops the loop it sits in (or the one named by aria-controls) until pressed again.
    fx.each('.fx-pause', function (btn) {
      var target = d.getElementById(btn.getAttribute('aria-controls') || '') || btn.parentNode;
      btn.hidden = false;
      btn.addEventListener('click', function () {
        target.fxHeld = !target.fxHeld;
        target.classList.toggle('fx-paused', target.fxHeld);
        btn.setAttribute('aria-pressed', String(target.fxHeld));
      });
    });
  }
  root.classList.add('fx-ready');
  return fx;
})(window, document);
```

What the kit does on its own: it reveals every `data-fx` element once and then removes the attribute, so the element's own hover and focus styles work normally afterwards; it pauses every looping recipe while it is off screen or the tab is hidden; and it wires any `fx-pause` button. Keep the kit whole. When trimming a theme, remove unused recipes, not parts of the kit.

`fx.on` is true when motion is allowed and the head gate ran; recipes that create hidden start states check it first. `fx.still` is the reader's reduced-motion setting, `fx.fine` a mouse or trackpad with hover, `fx.phone` a viewport under 48em, and `fx.lite` the browser's data-saver signal. All are read once at load. A reader who changes a system preference sees the new behavior on the next page, which is the next click on a blog.

## Choice index

The left column is the wording the interview shows the user. IDs point to a recipe heading in the named file. The last column says what the recipe needs. “Kit” means the head gate, kit styles and kit script must be in the theme, which is true of every recipe that starts or loops by itself, even when its own code is only CSS. “No kit” recipes work alone.

### Entrances and scrolling

| Interview choice | Recipe | Needs |
|---|---|---|
| No motion; Still and calm | No kit and no moving recipe. Use the [responsive](responsive.md) base, the still states of [S1](effects-interaction.md#s1-button-interactions), and static icons from [I1](effects-interaction.md#i1-icon-set-and-styles). Record the row as “still by choice” | No kit |
| Gentle fade-in; Soft fades | [E1](effects-text.md#e1-gentle-fade-in) | Kit |
| Reveal on scroll; Sliding entrances | [E2](effects-text.md#e2-reveal-on-scroll) | Kit |
| Staggered entrances | [E3](effects-text.md#e3-staggered-entrances) | Kit |
| Gentle spring; Springy cards | [E4](effects-text.md#e4-gentle-spring) | Kit |
| Image reveal; Image reveals | [E5](effects-text.md#e5-image-reveal) | Kit |
| A choreographed opening the user asks for by name, or a request for GSAP or Anime.js | [E6](effects-text.md#e6-hero-sequence-with-an-engine) | Kit + CDN engine |
| Motion amount (“tiny details”, “smooth and balanced”, “expressive on the homepage”, “animated throughout”) | Not a recipe. It sets how many recipes are used and where: details only (S1, I2); balanced (plus E2 or E3); expressive (plus one text effect and one background on the homepage). It never selects an engine. When the user also names specific effects, those choices win over the amount | — |
| Surprise me with a coherent mix; recommend something | E3 for sections, S1 and S2 for controls and cards, I2 nudge on arrows, plus at most one of B1, B3 or T1 that suits the style | Kit |

### Text

| Interview choice | Recipe | Needs |
|---|---|---|
| Letter-by-letter text; Letters appearing | [T1](effects-text.md#t1-letter-by-letter-text) | Kit |
| Changing words; Rotating words | [T2](effects-text.md#t2-changing-words) | Kit + JS |
| Waving letters | [T3](effects-text.md#t3-waving-letters) | Kit |
| Circular text | [T4](effects-text.md#t4-circular-text) | Kit |
| Ribbon text | [T5](effects-text.md#t5-ribbon-text) | Kit + JS |
| Cylindrical text | [T6](effects-text.md#t6-cylindrical-text) | Kit |
| Stacking text | [T7](effects-text.md#t7-stacking-text) | Kit |
| Count-up number | [T8](effects-text.md#t8-count-up-number) | Kit + JS |
| Drawing underline; Drawing lines | [T9](effects-text.md#t9-drawing-underline-and-lines) | No kit for the underline; Kit for drawn lines |
| Textured text | [T10](effects-text.md#t10-textured-and-shining-text) | No kit when still; Kit when it moves |

### Buttons, cards and pointer

| Interview choice | Recipe | Needs |
|---|---|---|
| Button interactions only; Simple color change | [S1](effects-interaction.md#s1-button-interactions) | No kit |
| Lifting cards; Lift slightly | [S2](effects-interaction.md#s2-lifting-cards) | No kit |
| Gentle photo zoom | [S3](effects-interaction.md#s3-gentle-photo-zoom) | No kit |
| Glowing borders; Glowing buttons | [S4](effects-interaction.md#s4-glowing-borders) | Kit |
| Shimmering button | [S5](effects-interaction.md#s5-shimmering-button) | Kit |
| Reflective card | [S6](effects-interaction.md#s6-reflective-card) | No kit |
| Tilting card; Tilt gently | [S7](effects-interaction.md#s7-tilting-card) | Kit + JS |
| Cursor-following light; Glow at the pointer | [S8](effects-interaction.md#s8-cursor-following-light) | Kit + JS |
| Magnetic button | [S9](effects-interaction.md#s9-magnetic-button) | Kit + JS |
| Cursor trail | [S10](effects-interaction.md#s10-cursor-trail) | Kit + JS |
| A small ripple | [S11](effects-interaction.md#s11-ripple) | Kit + JS |
| Glass; Translucent glass | [S12](effects-interaction.md#s12-glass-surface) | No kit |
| Light and dark with a toggle | [U1](effects-interaction.md#u1-light-and-dark-toggle) | No kit, own script |
| Follow the reader's device setting (light and dark, no button) | [U2](effects-interaction.md#u2-follow-the-device-setting) | No kit, CSS only |

### Icons

| Interview choice | Recipe | Needs |
|---|---|---|
| Thin, bold, solid, rounded, sharp, two-color, hand-drawn, minimal | [I1](effects-interaction.md#i1-icon-set-and-styles) | No kit |
| Animated icons; move when pointed at, focused or tapped | [I2](effects-interaction.md#i2-icon-motion) | No kit |
| Move when a menu opens | [I3](effects-interaction.md#i3-menu-button-morph) | No kit; needs the [phone menu](responsive.md#navigation-that-works-on-a-phone) |
| Move gently when they first appear | [I2](effects-interaction.md#i2-icon-motion), draw-in variant | Kit |

### Backgrounds

| Interview choice | Recipe | Needs |
|---|---|---|
| Flowing color background; Flowing backgrounds; Flowing color | [B1](effects-backgrounds-3d.md#b1-flowing-color) | Kit |
| Aurora | [B2](effects-backgrounds-3d.md#b2-aurora) | Kit |
| Color mesh; Soft gradient | [B3](effects-backgrounds-3d.md#b3-color-mesh-and-soft-gradient) | No kit when still |
| Subtle particles; Light particles; Tiny particles | [B4](effects-backgrounds-3d.md#b4-particles) | Kit + JS |
| Stars | [B5](effects-backgrounds-3d.md#b5-stars) | Kit |
| Light rays and beams | [B6](effects-backgrounds-3d.md#b6-light-rays) | Kit |
| Animated grid | [B7](effects-backgrounds-3d.md#b7-animated-grid) | Kit |
| Paper effect; Paper texture | [B8](effects-backgrounds-3d.md#b8-paper) | No kit |
| Spreading ink | [B9](effects-backgrounds-3d.md#b9-spreading-ink) | Kit |
| Liquid distortion | [B10](effects-backgrounds-3d.md#b10-liquid-distortion) | Kit + JS |
| Orbiting elements; Orbiting shapes | [B11](effects-backgrounds-3d.md#b11-orbiting-elements) | Kit |

### Depth and 3D

| Interview choice | Recipe | Needs |
|---|---|---|
| Photos with depth on scroll; a subtle photo effect; several photo sections | [D1](effects-backgrounds-3d.md#d1-photo-depth-on-scroll) | No kit (CSS), or Kit + CDN: simpleParallax |
| Moving layers; Layered backgrounds; only the welcome section | [D2](effects-backgrounds-3d.md#d2-moving-layers) | No kit (CSS), or Kit + CDN: GSAP ScrollTrigger |
| 3D object; A decorative 3D shape | [D3](effects-backgrounds-3d.md#d3-3d-object-in-css) | Kit |
| A 3D scene I already have; My existing interactive scene | [D4](effects-backgrounds-3d.md#d4-spline-scene) | Kit + CDN: Spline viewer + the user's scene URL |

Parallax strength maps to the numbers in D1 and D2: barely noticeable, gentle, dramatic on large screens. “Disable it on phones” is already the default.

### Between pages and loading

| Interview choice | Recipe | Needs |
|---|---|---|
| Instant navigation | Add nothing | Nothing |
| A quick fade | [N1](effects-navigation-loading.md#n1-quick-fade) | No kit |
| A color wipe | [N2](effects-navigation-loading.md#n2-color-wipe) | No kit |
| A sliding panel | [N3](effects-navigation-loading.md#n3-sliding-panel) | No kit |
| Transition curtain | [N4](effects-navigation-loading.md#n4-curtain) | No kit |
| A tiny top progress line | [N5](effects-navigation-loading.md#n5-top-progress-line) | No kit, own script |
| Reading progress bar; Progress bar | [R1](effects-navigation-loading.md#r1-reading-progress) | No kit |
| Estimated reading time | [R2](effects-navigation-loading.md#r2-reading-time) | No kit, own script |
| Article contents | [R3](effects-navigation-loading.md#r3-article-contents) | No kit, own script |
| Sharing | [R4](effects-navigation-loading.md#r4-share-links) | No kit |
| Print | [R5](effects-navigation-loading.md#r5-print) | No kit |
| Related posts | [L6](effects-navigation-loading.md#l6-skeletons-with-a-real-request) | No kit, own script |
| A small spinner | [L1](effects-navigation-loading.md#l1-spinner) | No kit |
| A pulsing dot | [L2](effects-navigation-loading.md#l2-pulsing-dot) | No kit |
| A brand mark | [L3](effects-navigation-loading.md#l3-brand-mark) | No kit |
| A short message | [L4](effects-navigation-loading.md#l4-status-message) | No kit |
| A slim progress line (for content) | [L5](effects-navigation-loading.md#l5-content-progress-line) | No kit |
| Placeholder cards; Placeholder silhouettes during actual loading | [L6](effects-navigation-loading.md#l6-skeletons-with-a-real-request) | No kit, own script |
| Different styles for page navigation and content | N5 between pages, and one of L1 to L6 for fetched content | No kit |
| Manually sliding gallery | [G1](effects-navigation-loading.md#g1-sliding-gallery) | No kit, own script |
| Slow carousel | [G2](effects-navigation-loading.md#g2-slow-carousel) | Kit + JS |

## Tool routes

The interview and the [source map](resources.md#source-map) name thirteen visual projects. This table says what each one actually is and how its kind of effect reaches a static Blogger theme. Distribution facts were checked against each project's published package or repository on October 12, 2026.

| Project | How it is distributed | Route in a static theme |
|---|---|---|
| [GSAP](https://gsap.com/) | Browser script on a CDN | **CDN.** Core, ScrollTrigger and SplitText tags are in [Engines](#engines-from-a-cdn); used by E6 and D2 |
| [Anime.js](https://animejs.com/) | Browser bundle on a CDN | **CDN.** Tag in [Engines](#engines-from-a-cdn); used by E6 and T9 |
| [simpleParallax](https://simpleparallax.com/) | Vanilla browser build on a CDN | **CDN.** Used by D1 |
| [Spline](https://spline.design/) | `<spline-viewer>` web component on Spline's CDN, plus a scene the user exports | **CDN + the user's scene URL.** D4. Without a real scene, use D3 |
| [Fontpair](https://fontpair.co/) | A website of pairing ideas | **Hardcoded link.** Pick the pair, then load the families from Google Fonts as in [responsive](responsive.md#fonts) |
| [React Bits](https://reactbits.dev/) | React components (JSX or TSX, CSS or Tailwind) | **Hardcoded.** No browser build exists. Recreate the chosen effect with E1–E5, T1–T10, S4–S10, B1–B7 |
| [Magic UI](https://magicui.design/) | React + Tailwind + Motion components | **Hardcoded.** Same: S4, S5, T5, T10, B4, B7, B11 |
| [Kokonut UI](https://kokonutui.com/) | React + Tailwind + Motion components | **Hardcoded.** Same: T10, S2, S6, S7, S9 |
| [Its Hover](https://www.itshover.com/icons) | React components built on `motion/react` | **Hardcoded.** I1–I3 give original inline SVG icons with CSS motion |
| [Originkit](https://www.originkit.dev/?sort=trending) | Not established from its public pages | **Hardcoded.** Treat it as a source of ideas and build the chosen effect from these recipes |
| [Boneyard](https://boneyard.vercel.app/overview) | `boneyard-js`: framework adapters and a capture CLI, no browser file | **Hardcoded.** L6 skeletons for a real request |
| [ShaderGradient](https://shadergradient.co/) | React packages (`@shadergradient/react`) | **Hardcoded.** B1 shader variant: a small WebGL gradient written into the theme |
| [Space Type Generator](https://spacetypegenerator.com/) | Browser tools that export video or images | **Hardcoded.** T4–T7 build live kinetic lettering in SVG and CSS. A user's exported loop can be embedded as their own media |

A React component cannot run in a Blogger theme: there is no bundler, JSX is not JavaScript, and Tailwind classes have no stylesheet. A hardcoded recipe is the working equivalent, not a port. When the user names one of these projects, look at the component they mean, pick the recipe that produces that behavior, and describe the result as an original recreation inspired by it.

## Engines from a CDN

Hashes and URLs were fetched on October 12, 2026. Before delivery, request each selected URL again and confirm it answers; never change a version without recomputing the hash. Include only the files the theme calls. The head has one order after the closing `b:skin` tag: first the kit's [head gate](#the-kit), with the stored light or dark choice of [U1](effects-interaction.md#u1-light-and-dark-toggle) as its first lines when the theme has that button; then these tags, in this order.

```xml
<script defer='defer' crossorigin='anonymous'
        src='https://cdn.jsdelivr.net/npm/gsap@3.15.0/dist/gsap.min.js'
        integrity='sha384-XmJ9SoHtVOHoQUcKvFAzVXwdkKo1Ie3bhmSoIAkcdsHGaIrVJIkmozyq0FJeb/Ly'></script>
<script defer='defer' crossorigin='anonymous'
        src='https://cdn.jsdelivr.net/npm/gsap@3.15.0/dist/ScrollTrigger.min.js'
        integrity='sha384-wl5TeDVvOWt30Pbf8aSo2ZrzsOjddu3avOBvHe+p+OhJt9gP6w9YXmDkN5DK2/dF'></script>
<script defer='defer' crossorigin='anonymous'
        src='https://cdn.jsdelivr.net/npm/gsap@3.15.0/dist/SplitText.min.js'
        integrity='sha384-SWJ0lLVRoipvHh59xj0pL7uC7Ih51F+5smaFtrG+2nr+TlDZU5SYJHmxfolbeNTr'></script>
```

```xml
<script defer='defer' crossorigin='anonymous'
        src='https://cdn.jsdelivr.net/npm/animejs@4.5.0/dist/bundles/anime.umd.min.js'
        integrity='sha384-InMmvD3VoYcY7hGjSC80aLb2bNNE4CzpX+Eq6FVDlmB0IKgDvmfPw4UY8L/M++iG'></script>
```

```xml
<script defer='defer' crossorigin='anonymous'
        src='https://cdn.jsdelivr.net/npm/simple-parallax-js@7.0.0/dist/vanilla/simpleParallaxVanilla.umd.js'
        integrity='sha384-1xVvdTHreoKI17SwnaBtl4bMO6FHg2p4ml6kIePtPWe5q8Rp8z7grwqq5bUUInfU'></script>
```

| Engine | Global | Size | Calls verified in these recipes |
|---|---|---|---|
| GSAP 3.15.0 | `gsap` | 73 KB | `gsap.timeline`, `gsap.from`, `gsap.to`, `gsap.registerPlugin` |
| ScrollTrigger 3.15.0 | `ScrollTrigger` | 45 KB | `scrollTrigger: { trigger, start, end, scrub }` |
| SplitText 3.15.0 | `SplitText` | 8 KB | `SplitText.create(el, { type: 'words,chars' })` |
| Anime.js 4.5.0 | `anime` | 118 KB | `anime.animate`, `anime.stagger`, `anime.createTimeline`, `anime.svg.createDrawable` |
| simpleParallax 7.0.0 | `SimpleParallax` | 7 KB | `new SimpleParallax(img, { scale, orientation, overflow, delay })`, `instance.destroy()` |
| Spline viewer 2.0.76 | `<spline-viewer>` | 3.6 MB | Loaded on demand by [D4](effects-backgrounds-3d.md#d4-spline-scene), never from the head |

A deferred script has run by the time `DOMContentLoaded` fires, so engine recipes start inside `fx.ready(…)` and check `window.gsap` or `window.anime` before the first call. If the CDN is blocked, the check fails and the page stays as it is, fully visible. A CDN that accepts the request and then stalls is different: it holds `DOMContentLoaded` back, so a recipe that hides anything while it waits also releases it on its own timer, as E6 does. An element animated by an engine must not also carry `data-fx`.

A script tag is static: the file is also downloaded by a reader who asked for reduced motion, and then does nothing. That cost is accepted, because the tag keeps its integrity check and its place in the head; keep it small by loading only the core file unless a plugin is used.

Load an engine only on the views that use it. A homepage-only effect wraps its script tag in a view condition, so posts never download it:

```xml
<!-- blogger -->
<b:if cond='data:view.isHomepage'>
  <script defer='defer' crossorigin='anonymous'
          src='https://cdn.jsdelivr.net/npm/gsap@3.15.0/dist/gsap.min.js'
          integrity='sha384-XmJ9SoHtVOHoQUcKvFAzVXwdkKo1Ie3bhmSoIAkcdsHGaIrVJIkmozyq0FJeb/Ly'></script>
</b:if>
```

## Limits and combinations

- At most three continuous loops on one view. A moving background is one of the three, and there is never more than one of those per page. Recipes that loop say so in a line that starts “Loop:”.
- At most one engine, one canvas and one 3D element per view.
- When the user's choices add up to more than this, do not silently drop one: say which effects exceed the budget and ask which to keep. The usual answer is to make one of them still, which every loop recipe supports by leaving out its animation line.
- A looping element given the class `fx-wide` stays still on phones. Use it when the brief says “simpler on phones”.
- One transform per element. An element that a script moves (S7, S9) should not be the same element that carries `data-fx`; put `data-fx` on a wrapper. A hover lift (S2) is fine on a `data-fx` element, because the kit lets go of it once it has entered.
- Text effects go on headings of a few words. Body text and post content never animate.
- Posts and static pages stay calm by default: entrances and hover feedback are fine; backgrounds, loops, pointer trails and 3D belong to the homepage unless the user asked for them everywhere.
- Scroll entrances fire once. Nothing replays when the reader scrolls back up.
- Budget for a rich homepage: at most 200 KB of scripts from CDNs (the Spline viewer is the stated exception and loads only on demand), no layout shift from any effect, and no effect on a full-width photograph on the first screen. A “little data” phone priority means no engine at all: use the hardcoded recipes.

## Checking an effect

For each selected recipe, at 375 px and 1280 px wide:

1. With JavaScript off, the content is fully visible and no hidden start state remains. With reduced motion on, nothing moves except a reading progress bar and a loading spinner that is really waiting.
2. With the engine's CDN blocked, the content is fully visible and the console shows no script error from the theme.
3. On a post and on the error view, the console shows no error and no “target not found” warning.
4. The page never scrolls sideways before, during or after the effect.
5. With a keyboard, every control the effect touches still shows focus and still works.
6. Hide the tab or scroll away: loops and canvases stop (the looping element carries `fx-paused`).
7. Print preview shows all content, including sections that were never scrolled into view, and shows it black on white when the device is in dark mode.
8. For an effect tied to scrolling ([D1](effects-backgrounds-3d.md#d1-photo-depth-on-scroll), [D2](effects-backgrounds-3d.md#d2-moving-layers), [R1](effects-navigation-loading.md#r1-reading-progress)): scroll the page and confirm the element really moves. An animation that exists but stands still is the sign of a clipping box with `overflow: hidden` around it.

Run effect checks in a tab that is in front. A background tab stops drawing frames, so entrances look stuck when nothing is wrong. A theme with no effects records these checks as not applicable.
