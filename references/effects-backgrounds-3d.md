# Effect Recipes: Backgrounds, Depth and 3D

Read [the index and kit](effects.md) first. A background sits behind content and never carries meaning. Use one moving background per page, keep text contrast measured against its lightest and darkest state, and prefer the CSS version of any recipe: it costs nothing to download and cannot fail to load.

Every background that moves is a loop and takes one of the page's [three loop slots](effects.md#limits-and-combinations); the kit pauses it off screen and in a hidden tab. Add the class `fx-wide` to keep it still on phones.

Every background here is a class on the section it decorates. The section's own content stays above it because the decoration is drawn on a pseudo-element or an inserted canvas with `z-index: -1` inside an isolated stacking context.

## Contents

- Backgrounds: [B1 flowing color](#b1-flowing-color), [B2 aurora](#b2-aurora), [B3 color mesh and soft gradient](#b3-color-mesh-and-soft-gradient), [B4 particles](#b4-particles), [B5 stars](#b5-stars), [B6 light rays](#b6-light-rays), [B7 animated grid](#b7-animated-grid), [B8 paper](#b8-paper)
- Effects on content: [B9 spreading ink](#b9-spreading-ink), [B10 liquid distortion](#b10-liquid-distortion), [B11 orbiting elements](#b11-orbiting-elements)
- Depth: [D1 photo depth on scroll](#d1-photo-depth-on-scroll), [D2 moving layers](#d2-moving-layers)
- 3D: [D3 3D object in CSS](#d3-3d-object-in-css), [D4 Spline scene](#d4-spline-scene)

## B1 Flowing color

Colors drift slowly across a section. The CSS version is the default.

```xml
<section class='hero fx-flow'>
  <h1>Slow mornings</h1>
</section>
```

```css
.fx-flow { background: linear-gradient(120deg, var(--fx-c1, #fde68a), var(--fx-c2, #fca5a5), var(--fx-c3, #a5b4fc), var(--fx-c1, #fde68a)); background-size: 300% 300%; }
.fx-on .fx-flow { animation: fx-flow 18s ease-in-out infinite alternate; }
@keyframes fx-flow { from { background-position: 0% 50%; } to { background-position: 100% 50%; } }
```

Loop: one slot, in either version.

**Shader variant.** For a richer, liquid-looking gradient, a small WebGL program is written straight into the theme: no library, about forty lines. It draws at a third of the section's resolution, stops when off screen, and leaves the CSS gradient in place whenever WebGL is missing, data saver is on, or motion is reduced. Give the section both classes and its three colors as hex attributes.

```xml
<section class='hero fx-flow fx-shader' data-a='#fde68a' data-b='#fca5a5' data-c='#a5b4fc'>
  <h1>Slow mornings</h1>
</section>
```

```css
.fx-shader { position: relative; isolation: isolate; }
.fx-shader__canvas { position: absolute; inset: 0; z-index: -1; width: 100%; height: 100%; pointer-events: none; }
```

```js
(function (fx) {
  if (!fx.on || fx.lite) return;
  fx.each('.fx-shader', function (box) {
    var cv = document.createElement('canvas');
    var gl = cv.getContext('webgl', { antialias: false, alpha: false, powerPreference: 'low-power' });
    if (!gl) return;
    function rgb(name, fallback) {
      var v = (box.getAttribute(name) || fallback).replace('#', '');
      return [parseInt(v.substr(0, 2), 16) / 255, parseInt(v.substr(2, 2), 16) / 255, parseInt(v.substr(4, 2), 16) / 255];
    }
    function compile(type, src) {
      var s = gl.createShader(type); gl.shaderSource(s, src); gl.compileShader(s);
      return gl.getShaderParameter(s, gl.COMPILE_STATUS) ? s : null;
    }
    var vs = compile(gl.VERTEX_SHADER, 'attribute vec2 p;void main(){gl_Position=vec4(p,0.,1.);}');
    var fs = compile(gl.FRAGMENT_SHADER, [
      'precision mediump float;uniform vec2 r;uniform float t;uniform vec3 a;uniform vec3 b;uniform vec3 c;',
      'void main(){vec2 p=gl_FragCoord.xy/r;p.x*=r.x/r.y;',
      'float n=sin(p.x*2.2+t*.25+sin(p.y*3.1-t*.2)*1.2)*.5+.5;',
      'float m=sin(p.y*2.7-t*.18+cos(p.x*2.4+t*.22)*1.4)*.5+.5;',
      'vec3 k=mix(a,b,smoothstep(.1,.9,n));k=mix(k,c,smoothstep(.25,.95,m*n+.12));',
      'gl_FragColor=vec4(k,1.);}'
    ].join(''));
    if (!vs || !fs) return;
    var pg = gl.createProgram();
    gl.attachShader(pg, vs); gl.attachShader(pg, fs); gl.linkProgram(pg);
    if (!gl.getProgramParameter(pg, gl.LINK_STATUS)) return;
    gl.useProgram(pg);
    gl.bindBuffer(gl.ARRAY_BUFFER, gl.createBuffer());
    gl.bufferData(gl.ARRAY_BUFFER, new Float32Array([-1, -1, 3, -1, -1, 3]), gl.STATIC_DRAW);
    var at = gl.getAttribLocation(pg, 'p');
    gl.enableVertexAttribArray(at); gl.vertexAttribPointer(at, 2, gl.FLOAT, false, 0, 0);
    gl.uniform3fv(gl.getUniformLocation(pg, 'a'), rgb('data-a', '#fde68a'));
    gl.uniform3fv(gl.getUniformLocation(pg, 'b'), rgb('data-b', '#fca5a5'));
    gl.uniform3fv(gl.getUniformLocation(pg, 'c'), rgb('data-c', '#a5b4fc'));
    var uR = gl.getUniformLocation(pg, 'r'), uT = gl.getUniformLocation(pg, 't'), frame = 0, t0 = window.performance.now();
    function draw(now) {
      var w = Math.max(1, Math.round(box.clientWidth / 3)), h = Math.max(1, Math.round(box.clientHeight / 3));
      if (cv.width !== w || cv.height !== h) { cv.width = w; cv.height = h; gl.viewport(0, 0, w, h); }
      gl.uniform2f(uR, w, h); gl.uniform1f(uT, (now - t0) / 1000);
      gl.drawArrays(gl.TRIANGLES, 0, 3);
      frame = window.requestAnimationFrame(draw);
    }
    cv.className = 'fx-shader__canvas'; cv.setAttribute('aria-hidden', 'true');
    cv.addEventListener('webglcontextlost', function (e) { e.preventDefault(); window.cancelAnimationFrame(frame); cv.style.display = 'none'; });
    box.insertBefore(cv, box.firstChild);
    fx.live(box, function () { window.cancelAnimationFrame(frame); frame = window.requestAnimationFrame(draw); }, function () { window.cancelAnimationFrame(frame); });
  });
})(window.fx);
```

This is original code. It is not the ShaderGradient renderer, which is a React package and cannot run in a theme.

## B2 Aurora

Soft blurred lights drift behind the content. Best on a dark section.

```xml
<section class='hero fx-aurora'>
  <h1>Northern notebook</h1>
</section>
```

```css
.fx-aurora { position: relative; isolation: isolate; overflow: hidden; background: var(--fx-aurora-base, #0b1026); color: var(--fx-aurora-ink, #f8fafc); }
.fx-aurora::before, .fx-aurora::after {
  content: ''; position: absolute; inset: -30%; z-index: -1; pointer-events: none; filter: blur(48px);
  background:
    radial-gradient(40% 50% at 30% 40%, var(--fx-a1, rgba(52, 211, 153, .55)), transparent 70%),
    radial-gradient(35% 45% at 70% 30%, var(--fx-a2, rgba(96, 165, 250, .5)), transparent 70%),
    radial-gradient(45% 40% at 55% 75%, var(--fx-a3, rgba(192, 132, 252, .45)), transparent 70%);
}
.fx-aurora::after { inset: -40%; opacity: .6; transform: rotate(25deg); }
.fx-on .fx-aurora::before { animation: fx-aurora 22s ease-in-out infinite alternate; }
.fx-on .fx-aurora::after { animation: fx-aurora 30s ease-in-out infinite alternate-reverse; }
@keyframes fx-aurora { from { transform: translate3d(-6%, -4%, 0) rotate(0deg); } to { transform: translate3d(6%, 5%, 0) rotate(12deg); } }
```

`overflow: hidden` on the section is required: the lights extend past its edges. Loop: one slot.

## B3 Color mesh and soft gradient

A still blend of soft color fields. This is the “soft gradient” answer, and the calm alternative to B1 and B2: no motion at all unless the drift class `fx-mesh--drift` is added, which makes it a loop.

```xml
<section class='hero fx-mesh'>
  <h1>Paper and sun</h1>
</section>
```

```css
.fx-mesh { position: relative; isolation: isolate; background-color: var(--fx-mesh-base, #fff7ed); }
.fx-mesh::before {
  content: ''; position: absolute; inset: 0; z-index: -1; pointer-events: none;
  background:
    radial-gradient(60% 55% at 12% 18%, var(--fx-m1, #fed7aa) 0, transparent 60%),
    radial-gradient(50% 50% at 88% 12%, var(--fx-m2, #fbcfe8) 0, transparent 60%),
    radial-gradient(55% 60% at 70% 92%, var(--fx-m3, #bfdbfe) 0, transparent 60%),
    radial-gradient(45% 45% at 18% 88%, var(--fx-m4, #d9f99d) 0, transparent 60%);
}
.fx-on .fx-mesh--drift::before { animation: fx-mesh 24s ease-in-out infinite alternate; }
@keyframes fx-mesh { to { filter: hue-rotate(28deg); transform: scale(1.06); } }
.fx-mesh--drift { overflow: hidden; }
```

## B4 Particles

Small dots float slowly upward. A 2D canvas the script inserts; it draws fewer dots on phones, none with data saver, and stops when off screen.

```xml
<section class='hero fx-particles'>
  <h1>Dust and daylight</h1>
</section>
```

```css
.fx-particles { position: relative; isolation: isolate; --fx-particle: rgba(120, 113, 108, .55); }
.fx-particles__canvas { position: absolute; inset: 0; z-index: -1; width: 100%; height: 100%; pointer-events: none; }
```

```js
(function (fx) {
  if (!fx.on || fx.lite) return;
  fx.each('.fx-particles', function (box) {
    var cv = document.createElement('canvas'), ctx = cv.getContext('2d');
    if (!ctx) return;
    var dpr = Math.min(window.devicePixelRatio || 1, 2), w = 0, h = 0, dots = [], frame = 0;
    var color = window.getComputedStyle(box).getPropertyValue('--fx-particle').trim() || 'rgba(120,113,108,.55)';
    cv.className = 'fx-particles__canvas'; cv.setAttribute('aria-hidden', 'true');
    box.insertBefore(cv, box.firstChild);
    function size() {
      w = box.clientWidth; h = box.clientHeight;
      cv.width = Math.round(w * dpr); cv.height = Math.round(h * dpr);
      ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
      var want = Math.min(fx.phone ? 28 : 70, Math.round(w * h / 16000));
      while (dots.length < want) dots.push({ x: Math.random() * w, y: Math.random() * h, r: 0.6 + Math.random() * 1.8, vx: (Math.random() - 0.5) * 0.18, vy: -0.05 - Math.random() * 0.2 });
      dots.length = want;
    }
    function draw() {
      ctx.clearRect(0, 0, w, h);
      ctx.fillStyle = color;
      dots.forEach(function (p) {
        p.x += p.vx; p.y += p.vy;
        if (p.y < -4) { p.y = h + 4; p.x = Math.random() * w; }
        if (p.x < -4) p.x = w + 4; else if (p.x > w + 4) p.x = -4;
        ctx.beginPath(); ctx.arc(p.x, p.y, p.r, 0, 6.2832); ctx.fill();
      });
      frame = window.requestAnimationFrame(draw);
    }
    size();
    if ('ResizeObserver' in window) new ResizeObserver(size).observe(box);
    fx.live(box, function () { window.cancelAnimationFrame(frame); frame = window.requestAnimationFrame(draw); }, function () { window.cancelAnimationFrame(frame); });
  });
})(window.fx);
```

Loop: one slot, and the page's one canvas. Set `--fx-particle` on the section to a color with some transparency. For snow, make `vy` positive and wrap at the bottom instead of the top.

## B5 Stars

A night sky that twinkles, with no script of its own and no canvas. Loop: one slot.

```xml
<section class='hero fx-stars'>
  <h1>Notes from the dark</h1>
</section>
```

```css
.fx-stars { position: relative; isolation: isolate; background: var(--fx-sky, #0b1026); color: var(--fx-sky-ink, #f8fafc); }
.fx-stars::before, .fx-stars::after {
  content: ''; position: absolute; inset: 0; z-index: -1; pointer-events: none; background-size: 300px 220px;
  background-image:
    radial-gradient(1px 1px at 20px 30px, #fff, transparent),
    radial-gradient(1px 1px at 90px 120px, #fff, transparent),
    radial-gradient(1.5px 1.5px at 160px 60px, #fff, transparent),
    radial-gradient(1px 1px at 230px 180px, #cfe3ff, transparent),
    radial-gradient(1.5px 1.5px at 280px 20px, #fff, transparent),
    radial-gradient(1px 1px at 130px 200px, #ffe9c4, transparent);
}
.fx-stars::after { background-size: 430px 340px; opacity: .6; }
.fx-on .fx-stars::before { animation: fx-twinkle 5s ease-in-out infinite alternate; }
.fx-on .fx-stars::after { animation: fx-twinkle 7s ease-in-out infinite alternate-reverse; }
@keyframes fx-twinkle { from { opacity: .35; } to { opacity: 1; } }
```

## B6 Light rays

Beams fan down from the top of a section and sway slightly. Loop: one slot.

```xml
<section class='hero fx-rays'>
  <h1>Morning service</h1>
</section>
```

```css
.fx-rays { position: relative; isolation: isolate; overflow: hidden; }
.fx-rays::before {
  content: ''; position: absolute; top: -40%; left: -25%; width: 150%; height: 180%; z-index: -1; pointer-events: none; transform-origin: 50% 0; filter: blur(6px);
  background: repeating-conic-gradient(from 200deg at 50% 0, transparent 0 8deg, var(--fx-ray, color-mix(in srgb, currentColor 12%, transparent)) 10deg 12deg, transparent 14deg 22deg);
  -webkit-mask-image: radial-gradient(60% 70% at 50% 0, #000, transparent 75%); mask-image: radial-gradient(60% 70% at 50% 0, #000, transparent 75%);
}
.fx-on .fx-rays::before { animation: fx-rays 16s ease-in-out infinite alternate; }
@keyframes fx-rays { from { transform: rotate(-6deg); } to { transform: rotate(6deg); } }
```

By default the beams take a faint tint of the section's text color, so they are dark on a light section and light on a dark one. Set `--fx-ray` to choose a color, for example `rgba(255, 220, 150, .2)` for warm light.

## B7 Animated grid

A fine grid that slides diagonally and fades toward the edges. Suits technical and futuristic styles. Loop: one slot.

```xml
<section class='hero fx-grid'>
  <h1>Patch notes</h1>
</section>
```

```css
.fx-grid { position: relative; isolation: isolate; --fx-cell: 48px; }
.fx-grid::before {
  content: ''; position: absolute; inset: 0; z-index: -1; pointer-events: none; background-size: var(--fx-cell) var(--fx-cell);
  background-image: linear-gradient(var(--fx-line, rgba(0, 0, 0, .09)) 1px, transparent 1px), linear-gradient(90deg, var(--fx-line, rgba(0, 0, 0, .09)) 1px, transparent 1px);
  -webkit-mask-image: radial-gradient(75% 75% at 50% 40%, #000, transparent); mask-image: radial-gradient(75% 75% at 50% 40%, #000, transparent);
}
.fx-on .fx-grid::before { animation: fx-grid 7s linear infinite; }
@keyframes fx-grid { to { background-position: var(--fx-cell) var(--fx-cell); } }
```

## B8 Paper

Two things: a grain that makes a flat color feel like paper, and a card that looks like a sheet pinned to the page. Both are still.

```xml
<section class='fx-paper'>
  <article class='fx-sheet'>
    <h3>Field recipe no. 12</h3>
    <p>Tomatoes, salt, time.</p>
  </article>
</section>
```

```css
.fx-paper { position: relative; isolation: isolate; background-color: var(--fx-paper, #f6f1e7); }
.fx-paper::before {
  content: ''; position: absolute; inset: 0; z-index: -1; pointer-events: none; opacity: .5; mix-blend-mode: multiply;
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='180' height='180'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='.85' numOctaves='2' stitchTiles='stitch'/%3E%3CfeColorMatrix values='0 0 0 0 .35 0 0 0 0 .3 0 0 0 0 .25 0 0 0 .55 0'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)'/%3E%3C/svg%3E");
}
.fx-sheet { position: relative; max-width: 26rem; padding: 1.75rem 1.5rem 1.5rem; background: var(--fx-sheet, #fffdf8); box-shadow: 0 1px 1px rgba(0, 0, 0, .12), 0 .6rem 1.2rem -.6rem rgba(0, 0, 0, .3); transform: rotate(-1.2deg); }
.fx-sheet::before { content: ''; position: absolute; top: -.7rem; left: 50%; width: 5.5rem; height: 1.6rem; background: var(--fx-tape, rgba(250, 204, 21, .55)); transform: translateX(-50%) rotate(2deg); }
.fx-sheet:nth-child(even) { transform: rotate(1deg); }
@media (max-width: 47.99em) { .fx-sheet, .fx-sheet:nth-child(even) { transform: none; } }
```

The grain is an inline SVG image, so nothing is downloaded. Sheets sit straight on phones, where a rotated card would touch the screen edge.

## B9 Spreading ink

A block appears as if ink were soaking outward from one point. Use it on one image, a pull quote or a colored panel.

```xml
<figure class='fx-frame' data-fx='ink' style='--ink-x:30%;--ink-y:70%'>
  <img alt='' src='https://example.com/print.jpg' width='1200' height='800' loading='lazy'/>
</figure>
```

```css
.fx-frame { margin: 0; overflow: hidden; }
.fx-frame img { display: block; width: 100%; height: auto; }
@property --fx-ink { syntax: '<percentage>'; inherits: false; initial-value: 0%; }
.fx-on [data-fx="ink"] {
  opacity: 1; --fx-ink: 0%; transition: --fx-ink 1.3s var(--fx-ease, ease);
  -webkit-mask-image: radial-gradient(circle at var(--ink-x, 50%) var(--ink-y, 60%), #000 calc(var(--fx-ink) - 14%), transparent var(--fx-ink));
  mask-image: radial-gradient(circle at var(--ink-x, 50%) var(--ink-y, 60%), #000 calc(var(--fx-ink) - 14%), transparent var(--fx-ink));
}
.fx-on [data-fx="ink"].is-in { --fx-ink: 180%; }
```

`--ink-x` and `--ink-y` set where the ink starts. In a browser without `@property` the block simply appears.

## B10 Liquid distortion

An image or large heading ripples like a reflection in water while the pointer is over it, then settles. Mouse only. One shared SVG filter does the work; place it once, directly after the opening body tag.

```xml
<svg class='fx-sprite' aria-hidden='true' focusable='false' xmlns='http://www.w3.org/2000/svg'>
  <filter id='fxLiquid' x='-10%' y='-10%' width='120%' height='120%'>
    <feTurbulence type='fractalNoise' baseFrequency='0.012 0.02' numOctaves='2' seed='3' result='noise'/>
    <feDisplacementMap in='SourceGraphic' in2='noise' scale='0' xChannelSelector='R' yChannelSelector='G'/>
  </filter>
</svg>
<figure class='fx-liquid'>
  <img alt='' src='https://example.com/lake.jpg' width='1200' height='800' loading='lazy'/>
</figure>
```

```css
.fx-sprite { position: absolute; width: 0; height: 0; overflow: hidden; }
.fx-liquid { margin: 0; overflow: hidden; }
.fx-liquid img { display: block; width: 100%; height: auto; }
```

```js
(function (fx) {
  if (!fx.on || !fx.fine) return;
  var turb = document.querySelector('#fxLiquid feTurbulence'), map = document.querySelector('#fxLiquid feDisplacementMap');
  if (!turb || !map) return;
  var active = null, power = 0, target = 0, frame = 0, t = 0;
  function step() {
    power += (target - power) * 0.12; t += 0.016;
    map.setAttribute('scale', (power * 22).toFixed(2));
    turb.setAttribute('baseFrequency', (0.012 + Math.sin(t) * 0.004).toFixed(4) + ' ' + (0.02 + Math.cos(t * 0.8) * 0.005).toFixed(4));
    if (target === 0 && power < 0.01) { if (active) active.style.filter = ''; active = null; frame = 0; return; }
    frame = window.requestAnimationFrame(step);
  }
  fx.each('.fx-liquid', function (el) {
    el.addEventListener('pointerenter', function () {
      if (active && active !== el) active.style.filter = '';
      active = el; el.style.filter = 'url(#fxLiquid)'; target = 1;
      if (!frame) frame = window.requestAnimationFrame(step);
    });
    el.addEventListener('pointerleave', function () { target = 0; });
  });
})(window.fx);
```

Filters are costly: use this on one or two elements, never on body text or a full-width hero.

## B11 Orbiting elements

Small shapes circle a center. Decoration around a logo, a portrait or a headline number. Set `--n` to the number of shapes and number them with `--i`.

```xml
<div class='fx-orbit' aria-hidden='true' style='--n:4'>
  <span class='fx-orbit__item' style='--i:0'><i></i></span>
  <span class='fx-orbit__item' style='--i:1'><i></i></span>
  <span class='fx-orbit__item' style='--i:2'><i></i></span>
  <span class='fx-orbit__item' style='--i:3'><i></i></span>
</div>
```

```css
.fx-orbit { position: relative; width: min(16rem, 62vw); aspect-ratio: 1; margin-inline: auto; border: 1px dashed var(--fx-orbit-line, rgba(0, 0, 0, .18)); border-radius: 50%; --fx-orbit-time: 28s; }
.fx-orbit__item { position: absolute; top: 50%; left: 50%; width: 0; height: 0; transform: rotate(calc(var(--i) * 360deg / var(--n))) translateX(calc(min(16rem, 62vw) / 2)); }
.fx-orbit__item > i { position: absolute; width: 1.1rem; height: 1.1rem; margin: -.55rem; border-radius: 50%; background: var(--fx-orbit-dot, #f59e0b); }
.fx-on .fx-orbit { animation: fx-spin var(--fx-orbit-time) linear infinite; }
```

Loop: one slot. To the browser the turning ring is a turning square, whose corners swing about a fifth of its width beyond the circle. Keep that much clear space between the ring and the edge of the page, or give its section `overflow-x: clip`, so the corners can never widen the page. To keep icons upright while they circle, replace the `i` with an `svg class='ico'` and add `.fx-on .fx-orbit__item > .ico { animation: fx-spin var(--fx-orbit-time) linear infinite reverse; }`. The ring is 62vw at most, so it fits a 320 px phone. The ring itself is hidden from assistive technology and everything inside it turns, so real content in the middle (a badge, a number, a portrait) is a sibling laid over it, not a child:

```xml
<div class='fx-orbit-box'>
  <div class='fx-orbit' aria-hidden='true' style='--n:3'>
    <span class='fx-orbit__item' style='--i:0'><i></i></span>
    <span class='fx-orbit__item' style='--i:1'><i></i></span>
    <span class='fx-orbit__item' style='--i:2'><i></i></span>
  </div>
  <p class='fx-orbit-box__center'>This weekend</p>
</div>
```

```css
.fx-orbit-box { display: grid; place-items: center; width: max-content; max-width: 100%; margin-inline: auto; }
.fx-orbit-box > * { grid-area: 1 / 1; }
.fx-orbit-box__center { margin: 0; text-align: center; }
```

## D1 Photo depth on scroll

A photograph moves a little slower than the page, inside a fixed frame. This is parallax. Off on phones and with reduced motion.

**Hardcoded (default).** A scroll-driven CSS animation: no script. Browsers without it show a still photo.

```xml
<figure class='fx-depth'>
  <img alt='Mountains above a lake at dawn' src='https://example.com/mountain.jpg' width='1800' height='1100'/>
</figure>
```

```css
.fx-depth { margin: 0; overflow: hidden; overflow: clip; }
.fx-depth img { display: block; width: 100%; height: auto; }
@supports (animation-timeline: view()) {
  @media (prefers-reduced-motion: no-preference) and (min-width: 48em) {
    .fx-depth img { scale: var(--fx-depth-scale, 1.12); animation: fx-depth linear both; animation-timeline: view(); animation-range: entry 0% exit 100%; }
    @keyframes fx-depth { from { translate: 0 calc(var(--fx-depth-move, 5%) * -1); } to { translate: 0 var(--fx-depth-move, 5%); } }
  }
}
```

| Strength the user chose | `--fx-depth-scale` | `--fx-depth-move` |
|---|---|---|
| Barely noticeable | `1.06` | `2.5%` |
| Gentle (default) | `1.12` | `5%` |
| Dramatic on large screens | `1.24` | `10%` |

The scale must stay larger than twice the move, or the frame shows an empty edge.

The frame clips with `overflow: clip`, and the `overflow: hidden` before it is only for browsers that do not know `clip`. Do not remove the second declaration: a box with `overflow: hidden` counts as a scrolling area, the animation would then follow that box instead of the page, and the photo would never move. The same holds for every box between the photo and the page. When the figure sits inside a card or media frame that clips its content, that box needs `overflow: clip` as well. To confirm, scroll the page and watch the photo slide inside its frame.

**CDN: simpleParallax.** Use it when the user named the library or wants the effect in every current browser. Load its [tag](effects.md#engines-from-a-cdn), give the image the `fx-parallax` class instead of wrapping it in `fx-depth`, and never apply both routes to one image.

```js
/* alt: simpleparallax */
fx.ready(function () {
  if (!window.SimpleParallax) return;
  var still = window.matchMedia('(prefers-reduced-motion: reduce)'), wide = window.matchMedia('(min-width: 48em)');
  fx.each('img.fx-parallax', function (img) {
    var instance = null;
    function sync() {
      if (instance) { instance.destroy(); instance = null; }
      if (!still.matches && wide.matches && img.complete && img.naturalWidth) instance = new window.SimpleParallax(img, { scale: 1.12, orientation: 'up', overflow: false, delay: 0.4 });
    }
    img.addEventListener('load', sync, { once: true });
    if (still.addEventListener) { still.addEventListener('change', sync); wide.addEventListener('change', sync); }
    sync();
  });
});
```

`scale` carries the same three strengths: 1.06, 1.12, 1.24. Whichever route is used, give the image real dimensions, and do not lazy-load it when it is the first large image on the page.

## D2 Moving layers

Several layers of a hero drift at different speeds as the reader scrolls, which reads as depth. Layers are decoration: shapes, illustration parts, a background photo. Text stays on a layer that does not move.

```xml
<header class='hero fx-layers'>
  <div class='fx-layer' aria-hidden='true' style='--depth:.9'><div class='hero__hills'></div></div>
  <div class='fx-layer' aria-hidden='true' style='--depth:.4'><div class='hero__sun'></div></div>
  <div class='hero__text'>
    <h1>Far and familiar</h1>
  </div>
</header>
```

**Hardcoded (default).**

```css
.fx-layers { position: relative; isolation: isolate; overflow: hidden; overflow: clip; }
.fx-layer { position: absolute; inset: 0; z-index: -1; pointer-events: none; }
@supports (animation-timeline: view()) {
  @media (prefers-reduced-motion: no-preference) and (min-width: 48em) {
    .fx-layer { animation: fx-layer linear both; animation-timeline: view(); animation-range: cover 0% cover 100%; }
    @keyframes fx-layer { from { translate: 0 calc(var(--depth, .3) * -8rem); } to { translate: 0 calc(var(--depth, .3) * 8rem); } }
  }
}
```

`--depth` runs from 0 (does not move) to 1 (moves most). Give far things small numbers and near things large ones. Halve the `8rem` for “barely noticeable”; raise it to `14rem` for “dramatic”. As in [D1](#d1-photo-depth-on-scroll), the box clips with `overflow: clip`: with `overflow: hidden` alone the layers would stand still.

**CDN: GSAP ScrollTrigger.** For the same effect in every current browser. Load GSAP and ScrollTrigger, keep the layer markup and the first two CSS rules, and leave out the `@supports` block.

```js
/* alt: scrolltrigger */
fx.ready(function () {
  if (fx.still || fx.phone || !window.gsap || !window.ScrollTrigger) return;
  window.gsap.registerPlugin(window.ScrollTrigger);
  fx.each('.fx-layers', function (box) {
    fx.each('.fx-layer', function (layer) {
      var depth = parseFloat(layer.style.getPropertyValue('--depth')) || 0.3;
      window.gsap.fromTo(layer, { y: -128 * depth }, { y: 128 * depth, ease: 'none', scrollTrigger: { trigger: box, start: 'top bottom', end: 'bottom top', scrub: true } });
    }, box);
  });
});
```

## D3 3D object in CSS

A real three-dimensional shape that turns slowly, built from six elements and CSS transforms. No library, no WebGL, no download. This is the answer to “a decorative 3D shape” and to “3D object” when the user has no scene of their own.

```xml
<div class='fx-3d' aria-hidden='true'>
  <div class='fx-cube'>
    <i class='fx-face fx-face--front'></i><i class='fx-face fx-face--back'></i>
    <i class='fx-face fx-face--right'></i><i class='fx-face fx-face--left'></i>
    <i class='fx-face fx-face--top'></i><i class='fx-face fx-face--bottom'></i>
  </div>
</div>
```

```css
.fx-3d { --s: min(10rem, 34vw); width: var(--s); height: var(--s); margin: calc(var(--s) * .45) auto; perspective: calc(var(--s) * 5); }
.fx-cube { position: relative; width: 100%; height: 100%; transform-style: preserve-3d; transform: rotateX(-24deg) rotateY(34deg); }
.fx-on .fx-cube { animation: fx-cube 22s linear infinite; }
.fx-face { position: absolute; inset: 0; border: 1px solid var(--fx-3d-edge, rgba(255, 255, 255, .5)); background: var(--fx-3d-face, rgba(245, 158, 11, .82)); }
.fx-face--front { transform: translateZ(calc(var(--s) / 2)); }
.fx-face--back { transform: rotateY(180deg) translateZ(calc(var(--s) / 2)); }
.fx-face--right { transform: rotateY(90deg) translateZ(calc(var(--s) / 2)); filter: brightness(.86); }
.fx-face--left { transform: rotateY(-90deg) translateZ(calc(var(--s) / 2)); filter: brightness(.86); }
.fx-face--top { transform: rotateX(90deg) translateZ(calc(var(--s) / 2)); filter: brightness(1.12); }
.fx-face--bottom { transform: rotateX(-90deg) translateZ(calc(var(--s) / 2)); filter: brightness(.7); }
@keyframes fx-cube { from { transform: rotateX(-24deg) rotateY(34deg); } to { transform: rotateX(-24deg) rotateY(394deg); } }
```

Variations from the same six faces:

- **Glass block**: set `--fx-3d-face` to a translucent color such as `rgba(255, 255, 255, .12)` and add `backdrop-filter: blur(4px)` to `.fx-face`.
- **Picture cube**: give each face a `background: url(…) center / cover` with the owner's real images.
- **Slab or tile**: scale the cube on one axis, for example `transform: rotateX(-24deg) rotateY(34deg) scaleY(.35)` in the rest rule and both keyframes.
- **Wireframe**: `--fx-3d-face: transparent` and a 2px edge.

The margin leaves room for the corners as the shape turns; do not remove it. Without motion the cube rests at a three-quarter angle.

Loop: one slot, and the page's one 3D element. On a phone it turns as well unless the element has the class `fx-wide`.

## D4 Spline scene

**CDN + the user's own scene.** An interactive 3D scene made in [Spline](https://spline.design/). This recipe needs one thing only the user can supply: the public URL of a scene they exported, which looks like `https://prod.spline.design/…/scene.splinecode`. If they do not have one, use D3 and say why. Never write a made-up or sample scene URL into a theme.

The viewer is a 3.6 MB script. This recipe therefore never loads it up front: it shows a still image, and fetches the viewer only when the scene scrolls near the viewport, on a wide screen, with motion allowed and data saver off. Everyone else keeps the still image.

```xml
<div class='fx-scene' data-scene='https://prod.spline.design/USER-SCENE-ID/scene.splinecode'>
  <img class='fx-scene__poster' alt='A paper boat on rippling water' src='https://example.com/scene-poster.jpg' width='1500' height='1000' loading='lazy'/>
</div>
```

```css
.fx-scene { position: relative; aspect-ratio: 3 / 2; overflow: hidden; border-radius: var(--fx-radius, 1rem); background: var(--fx-scene-bg, #e7e5e4); }
.fx-scene__poster { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: cover; transition: opacity .6s; }
.fx-scene spline-viewer { position: absolute; inset: 0; width: 100%; height: 100%; }
.fx-scene.is-live .fx-scene__poster { opacity: 0; pointer-events: none; }
```

```js
(function (fx) {
  var VIEWER = 'https://cdn.spline.design/@splinetool/viewer@2.0.76/build/spline-viewer.js';
  var HASH = 'sha384-75Ll5d5rOBHYtfrSCoz8KLIMbQyDBlMMq5o2GsJUT6m+friBP5NTUT3b3h0y1FhT';
  if (fx.still || fx.lite || fx.phone) return;
  fx.each('.fx-scene[data-scene]', function (box) {
    var url = box.getAttribute('data-scene');
    if (!/^https:\/\/prod\.spline\.design\/[\w-]+\/scene\.splinecode$/.test(url) || url.indexOf('USER-SCENE-ID') > -1) return;
    fx.seen(box, function () {
      if (!document.querySelector('script[data-fx-spline]')) {
        var s = document.createElement('script');
        s.type = 'module'; s.src = VIEWER; s.integrity = HASH; s.crossOrigin = 'anonymous'; s.setAttribute('data-fx-spline', '');
        document.head.appendChild(s);
      }
      var view = document.createElement('spline-viewer');
      view.setAttribute('url', url);
      view.setAttribute('loading', 'lazy');
      view.setAttribute('events-target', 'local');
      view.addEventListener('load-complete', function () { box.classList.add('is-live'); });
      box.appendChild(view);
    }, '300px');
  });
})(window.fx);
```

What to settle with the user before using it:

- **The poster.** A screenshot of their scene, uploaded to their blog, with real dimensions. It is what phones, slow connections and reduced-motion readers see, and what everyone sees until the scene is ready.
- **Scrolling.** If the scene has zoom or pan enabled, the mouse wheel will move the scene instead of the page. Ask them to switch those off in Spline's export settings, or keep the scene out of the first screen.
- **Phones.** Off by default. To allow it, remove `|| fx.phone` and say plainly that it costs several megabytes.
- **The Spline mark.** A scene from a free Spline plan shows Spline's logo. Do not hide it.

The viewer URL and hash were verified on October 12, 2026 against Spline's own CDN. When the version changes, recompute the hash; a wrong hash blocks the script and the poster simply stays. The README of that package also offers a smaller WebGL-only file for scenes exported for WebGL; use it only if the user's export panel names it.
