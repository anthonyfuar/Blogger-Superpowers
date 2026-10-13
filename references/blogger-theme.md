# Theme Skeleton and Gadget Data

The starting point for every **new** theme: a complete, working Blogger theme with every view, plus the gadget data it relies on. When the user supplies their own theme XML, start from that file instead and use this one only to look things up.

Read it in the implementation stage before writing any XML. Build the user's design on this structure; do not write a Blog gadget from memory.

## Contents

- [What is established and what is not](#what-is-established-and-what-is-not)
- [The skeleton](#the-skeleton)
- [How to build on it](#how-to-build-on-it)
- [Blog gadget](#blog-gadget)
- [Navigation gadget](#navigation-gadget)
- [Other gadgets](#other-gadgets)
- [Owner-editable content](#owner-editable-content)
- [Favicon](#favicon)
- [Designer variables](#designer-variables)
- [If Blogger refuses to save the theme](#if-blogger-refuses-to-save-the-theme)

## What is established and what is not

No theme produced by this skill has been saved in Blogger by the skill itself. The skeleton is well-formed, passes the structural checklist, and renders correctly in a local simulation on six views at five widths. Whether Blogger's own compiler accepts each expression is confirmed only when the owner saves it.

Each Blogger-specific construct used here falls in one of three groups:

| Group | Constructs | Basis |
|---|---|---|
| Documented by Blogger Help | `b:section` and `b:widget` with their attributes, `b:includable`, `b:include`, `b:if` with `b:elseif` and `b:else`, `b:loop` with `index`, `b:eval`, `b:with`, `b:switch`, `expr:` attributes, the operators `==`, `!=`, `<`, `>`, `and`, `or`, `in`, `contains`, `? :`, `+` and `[0]`, `data:blog.title`, `pageTitle`, `pageType`, `url`, `homepageUrl`, `languageDirection`, `data:posts`, `post.title`, `post.url`, `post.body`, `post.labels` with `name` and `url`, `post.allowComments`, `olderPageUrl`, `newerPageUrl`, Label `labels` with `name`, `url`, `count`, HTML `title` and `content`, LinkList `links` with `name` and `target`, BlogArchive `data` with `name`, `url`, `post-count`, skin variables | The four Help pages in [sources](sources.md#source-register), read again on October 13, 2026 |
| Blogger's default version-2 Blog markup | `<b:include data='post' name='commentPicker'/>` and the other [native includables](#native-blog-includables) | Two community references that reproduce Blogger's default markup: there `commentPicker` takes `var='post'`, chooses between threaded and plain comments, and is called by Blogger's own `postCommentsAndAd` exactly as the skeleton calls it. A current third-party theme also overrides an includable of that id |
| Seen working in a current third-party theme | `data:view.*`, `data:posts.size`, `data:post.date format "…"`, `date.iso8601`, `post.author.name`, `post.featuredImage`, `post.snippets.short`, PageList `links` with `href`, `title`, `isCurrentPage`, `data:blog.locale`, `data:blog.searchUrl`, `cond` on `b:section` and `b:widget`, `b:widget-settings` with `content`, `b:class`, the helpers `snippet()`, `resizeImage()` and `sourceSet()`, and `featuredImage.isResizable` | The JetTheme and Pixel sources pinned in [sources](sources.md#code-from-its-authors) |

Blogger Help describes the first generation of the template language. Everything a version-2 theme needs beyond it comes from the other two groups. The last section of this file lists a fallback for every construct outside the first group. Say in the delivery note that the theme has not been saved in Blogger, and ask the user to send back the exact message if Blogger shows one.

## The skeleton

A plain, complete theme: sticky header with an owner-editable menu, a homepage-only welcome block, a card grid of posts, a full post view with comments, static pages, label, search and archive listings with headings, empty states, a 404 view, a footer with labels, about text and search, an embedded favicon and the hidden Blogger credit. It has the [phone menu](responsive.md#navigation-that-works-on-a-phone) and no effects; the [kit](effects.md#the-kit) and recipes are added on top.

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
    <!-- Theme: Plain-Start. The icon comes after the native head so it is the last one declared. -->
    <link href='data:image/svg+xml,%3Csvg xmlns=%27http://www.w3.org/2000/svg%27 viewBox=%270 0 32 32%27%3E%3Crect width=%2732%27 height=%2732%27 rx=%276%27 fill=%27%231c1917%27/%3E%3Cpath d=%27M9 22V10h6.5a4 4 0 0 1 0 8H9%27 fill=%27none%27 stroke=%27%23fafaf9%27 stroke-width=%272.6%27/%3E%3C/svg%3E' rel='icon' type='image/svg+xml'/>
    <b:skin><![CDATA[
/* Theme: Plain-Start. Built with the Blogger Superpowers skeleton. */

/* 1. Cancel Blogger's widget stylesheet where it bends the layout. */
.section, .widget { margin: 0; padding: 0; line-height: inherit; }
.widget ul, .widget ol { margin: 0; padding: 0; line-height: inherit; }
.widget li { margin: 0; padding: 0; text-indent: 0; }
h1, h2, h3, h4 { position: static; }
a img { position: static; }
#Attribution1, .widget.Attribution { display: none !important; }

/* 2. Foundations. */
*, *::before, *::after { box-sizing: border-box; }
:root {
  --bg: #fafaf9; --surface: #ffffff; --ink: #1c1917; --muted: #57534e; --line: #d6d3d1; --accent: #b45309;
  --font-head: Georgia, 'Times New Roman', serif;
  --font-body: system-ui, -apple-system, 'Segoe UI', Roboto, sans-serif;
  --gutter: clamp(1rem, 4vw, 2.5rem); --page: 74rem; --measure: 32em; --radius: .6rem;
  --step--1: clamp(.83rem, .8rem + .15vw, .9rem);
  --step-0: clamp(1rem, .96rem + .2vw, 1.125rem);
  --step-1: clamp(1.2rem, 1.1rem + .5vw, 1.5rem);
  --step-3: clamp(1.75rem, 1.35rem + 2vw, 2.9rem);
  --step-4: clamp(2.1rem, 1.4rem + 3.6vw, 4.25rem);
}
html { -webkit-text-size-adjust: 100%; text-size-adjust: 100%; scroll-padding-top: 5rem; }
body { margin: 0; padding: 0; background: var(--bg); color: var(--ink); font-family: var(--font-body); font-size: var(--step-0); line-height: 1.65; word-break: normal; overflow-wrap: break-word; }
h1, h2, h3, h4 { margin: 0 0 .5em; font-family: var(--font-head); line-height: 1.15; text-wrap: balance; overflow-wrap: break-word; }
h1 { font-size: var(--step-4); }
h2 { font-size: var(--step-3); }
h3 { font-size: var(--step-1); }
p { margin: 0 0 1em; }
img, svg, video, canvas { max-width: 100%; }
img, video { height: auto; }
a { color: inherit; text-underline-offset: .18em; }
:focus-visible { outline: 3px solid currentColor; outline-offset: 3px; }
button, input, select, textarea { font: inherit; max-width: 100%; }
[hidden] { display: none !important; }

/* 3. Page shell. */
.shell { width: min(100% - var(--gutter) * 2, var(--page)); margin-inline: auto; }
.grid { display: grid; gap: clamp(1rem, 3vw, 2rem); grid-template-columns: repeat(auto-fill, minmax(min(100%, 17rem), 1fr)); }
.grid > * { min-width: 0; }
.skip-link { position: absolute; left: var(--gutter); top: -10rem; z-index: 100; padding: .75rem 1rem; background: var(--surface); color: var(--ink); }
.skip-link:focus { top: .75rem; }
.fx-vh { position: absolute; width: 1px; height: 1px; margin: -1px; overflow: hidden; clip-path: inset(50%); white-space: nowrap; }
.kicker { margin: 0 0 .5rem; font-size: var(--step--1); letter-spacing: .08em; text-transform: uppercase; color: var(--muted); }

/* 4. Header and navigation. */
.site-top { border-bottom: 1px solid var(--line); background: var(--bg); }
.site-head { display: flex; flex-wrap: wrap; align-items: center; justify-content: space-between; gap: .5rem 1.5rem; padding-block: .75rem; }
.site-brand { display: inline-flex; align-items: center; min-height: 2.75rem; font-family: var(--font-head); font-size: var(--step-1); font-weight: 700; text-decoration: none; }
.site-nav ul { display: flex; flex-wrap: wrap; gap: 0 1.25rem; margin: 0; padding: 0; list-style: none; }
.site-nav li { margin: 0; padding: 0; }
.site-nav a { display: inline-flex; align-items: center; min-height: 2.75rem; text-decoration: none; }
.site-nav a:hover { text-decoration: underline; }
.site-nav a:focus-visible { text-decoration: underline; }
.site-nav .is-current > a { color: var(--accent); }
.fx-menu { display: inline-grid; place-items: center; width: 2.75rem; height: 2.75rem; padding: 0; border: 0; border-radius: var(--radius); background: none; color: inherit; cursor: pointer; }
.fx-menu__bars { display: grid; gap: 5px; width: 1.4rem; }
.fx-menu__bars i { display: block; height: 2px; border-radius: 2px; background: currentColor; }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(1) { transform: translateY(7px) rotate(45deg); }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(2) { opacity: 0; }
.fx-menu[aria-expanded='true'] .fx-menu__bars i:nth-child(3) { transform: translateY(-7px) rotate(-45deg); }
@media (max-width: 47.99em) {
  .js .site-top { position: sticky; top: 0; z-index: 50; }
  .js .site-nav { display: none; flex-basis: 100%; }
  .js .site-nav.is-open { display: block; max-height: calc(100vh - 5rem); overflow-y: auto; overscroll-behavior: contain; }
  .js .site-nav ul { flex-direction: column; }
  .js .site-nav a { display: flex; min-height: 3rem; }
}
@media (min-width: 48em) {
  .site-top { position: sticky; top: 0; z-index: 50; }
  .site-head .fx-menu { display: none; }
}

/* 5. Homepage welcome. */
.welcome { padding-block: clamp(2.5rem, 8vw, 5rem); }
.welcome p { max-width: 38em; font-size: var(--step-1); color: var(--muted); }

/* 6. Listings. */
.listing { padding-block: clamp(2rem, 6vw, 4rem); }
.listing__title { font-size: var(--step-3); }
.card { display: flex; flex-direction: column; height: 100%; border: 1px solid var(--line); border-radius: var(--radius); background: var(--surface); overflow: hidden; }
.card__media { display: block; aspect-ratio: 3 / 2; background: var(--line); }
.card__media img { display: block; width: 100%; height: 100%; object-fit: cover; }
.card__body { display: flex; flex-direction: column; gap: .5rem; padding: 1.25rem; }
.card__title { margin: 0; font-size: var(--step-1); }
.card__title a { text-decoration: none; }
.card__title a:hover { text-decoration: underline; }
.card__title a:focus-visible { text-decoration: underline; }
.card__meta, .card__excerpt { margin: 0; }
.card__meta { font-size: var(--step--1); color: var(--muted); }
.notice { max-width: 38em; padding-block: 2rem; }
.pager { display: flex; flex-wrap: wrap; justify-content: space-between; gap: .75rem 1.5rem; margin-top: 2.5rem; padding-top: 1.25rem; border-top: 1px solid var(--line); }
.pager a { display: inline-flex; align-items: center; min-height: 2.75rem; }
.pager__next { margin-inline-start: auto; }

/* 7. Post and page. */
.entry { padding-block: clamp(2rem, 6vw, 4rem); }
.entry__head { max-width: var(--measure); margin-inline: auto; }
.entry__title { font-size: var(--step-3); }
.entry__meta { display: flex; flex-wrap: wrap; gap: .25rem 1rem; margin: 0 0 2rem; font-size: var(--step--1); color: var(--muted); }
.post-body { width: auto; max-width: var(--measure); margin-inline: auto; overflow-wrap: anywhere; overflow-x: auto; }
.post-body h2 { margin-top: 1.8em; font-size: var(--step-1); }
.post-body h3 { margin-top: 1.6em; font-size: var(--step-0); }
.post-body img, .post-body video, .post-body iframe, .post-body embed, .post-body object { max-width: 100%; }
.post-body img { height: auto; }
.post-body iframe[src*='youtube'], .post-body iframe[src*='vimeo'], .post-body iframe[src*='blogger.com/video'] { width: 100%; height: auto; aspect-ratio: 16 / 9; }
.post-body pre { max-width: 100%; overflow-x: auto; white-space: pre; }
.post-body table { border-collapse: collapse; overflow-wrap: normal; word-break: normal; }
.post-body th, .post-body td { padding: .4rem .6rem; border: 1px solid var(--line); text-align: start; }
.post-body blockquote { margin: 1.5em 0; padding-inline-start: 1rem; border-inline-start: 3px solid var(--accent); }
.post-body .fx-scroll { max-width: 100%; overflow-x: auto; }
.widget .post-body ul, .widget .post-body ol { margin: 1em 0; padding: 0 0 0 1.4em; line-height: inherit; }
.widget .post-body li { margin: 0 0 .35em; padding: 0; }
.post-body .separator, .post-body .tr-caption-container { max-width: 100%; }
.post-body a[style*='margin-left'], .post-body a[style*='margin-right'] { margin-inline: 0 !important; }
.entry__foot, .entry-comments { max-width: var(--measure); margin-inline: auto; }
ul.tags { display: flex; flex-wrap: wrap; gap: .5rem; margin: 2rem 0 0; padding: 0; list-style: none; }
.tags > li { margin: 0; padding: 0; }
.tags a { display: inline-flex; align-items: center; min-height: 2.75rem; padding: 0 1rem; border: 1px solid var(--line); border-radius: var(--radius); text-decoration: none; }
.entry-comments #comments, .entry-comments .comments { margin-top: 3rem; padding-top: 1.5rem; border-top: 1px solid var(--line); }
.entry-comments iframe { max-width: 100%; }

/* 8. Footer. */
.site-foot { margin-top: clamp(3rem, 8vw, 6rem); padding-block: clamp(2rem, 6vw, 4rem); border-top: 1px solid var(--line); }
.site-foot__cols { display: grid; gap: 2rem; grid-template-columns: repeat(auto-fit, minmax(min(100%, 15rem), 1fr)); }
.site-foot h2 { font-size: var(--step-1); }
ul.label-list { display: flex; flex-wrap: wrap; gap: 0 1.25rem; margin: 0; padding: 0; list-style: none; }
.label-list > li { margin: 0; padding: 0; }
.label-list a { display: inline-flex; align-items: center; min-height: 2.75rem; }
.search { display: flex; flex-wrap: wrap; gap: .5rem; }
.search label { flex-basis: 100%; }
.search input { flex: 1 1 10rem; min-height: 2.75rem; padding: 0 .75rem; border: 1px solid var(--line); border-radius: var(--radius); background: var(--surface); color: var(--ink); }
.search button { min-height: 2.75rem; padding: 0 1.25rem; border: 0; border-radius: var(--radius); background: var(--ink); color: var(--bg); cursor: pointer; }
.site-foot__note { margin: 2rem 0 0; font-size: var(--step--1); color: var(--muted); }
    ]]></b:skin>
    <script>
//<![CDATA[
(function (d, w) {
  var c = d.documentElement.classList;
  c.add('js');
  w.setTimeout(function () { if (!c.contains('menu-ready')) c.remove('js'); }, 3000);
})(document, window);
//]]>
    </script>
  </head>
  <body>
    <a class='skip-link' href='#main'>Skip to content</a>

    <header class='site-top'>
      <div class='site-head shell'>
        <a class='site-brand' expr:href='data:blog.homepageUrl'><data:blog.title/></a>
        <button aria-controls='siteNav' aria-expanded='false' class='fx-menu' hidden='hidden' type='button'>
          <span aria-hidden='true' class='fx-menu__bars'><i></i><i></i><i></i></span>
          <span class='fx-vh'>Menu</span>
        </button>
        <nav aria-label='Main' class='site-nav' id='siteNav'>
          <b:section class='header' id='navlinks' maxwidgets='1' showaddelement='no'>
            <b:widget id='PageList1' locked='false' title='Menu' type='PageList' version='2'>
              <b:includable id='main'>
                <ul>
                  <b:loop values='data:links' var='link'>
                    <li>
                      <b:class cond='data:link.isCurrentPage' name='is-current'/>
                      <a expr:href='data:link.href'><data:link.title/></a>
                    </li>
                  </b:loop>
                </ul>
              </b:includable>
            </b:widget>
          </b:section>
        </nav>
      </div>
    </header>

    <main id='main' tabindex='-1'>
      <b:section class='main' cond='data:view.isHomepage or data:view.isLayoutMode' id='hometop' maxwidgets='2' showaddelement='yes'>
        <b:widget id='HTML1' locked='false' title='Welcome' type='HTML' version='2'>
          <b:widget-settings>
            <b:widget-setting name='content'><![CDATA[<h1>A plain starting point</h1><p>Replace this welcome text in Layout. It appears on the homepage only.</p>]]></b:widget-setting>
          </b:widget-settings>
          <b:includable id='main'>
            <div class='welcome shell'><data:content/></div>
          </b:includable>
        </b:widget>
      </b:section>

      <b:section class='main' id='posts' maxwidgets='1' showaddelement='no'>
        <b:widget id='Blog1' locked='true' title='Posts' type='Blog' version='2'>
          <b:includable id='main' var='this'>
            <b:if cond='data:view.isError'>
              <b:include name='themeNotFound'/>
            <b:elseif cond='data:view.isSingleItem'/>
              <b:loop values='data:posts' var='post'>
                <b:include data='post' name='themeEntry'/>
              </b:loop>
            <b:else/>
              <div class='listing shell'>
                <b:include name='themeListHeading'/>
                <b:if cond='data:posts.size == 0'>
                  <div class='notice'>
                    <p>Nothing has been published here yet.</p>
                    <p><a expr:href='data:blog.homepageUrl'>Back to the homepage</a></p>
                  </div>
                <b:else/>
                  <div class='grid'>
                    <b:loop values='data:posts' var='post'>
                      <b:include data='post' name='themeCard'/>
                    </b:loop>
                  </div>
                </b:if>
                <b:include name='themeListPager'/>
              </div>
            </b:if>
          </b:includable>

          <b:includable id='themeListHeading'>
            <b:if cond='data:view.isHomepage'>
              <h2 class='listing__title'>Latest posts</h2>
            <b:elseif cond='data:view.isSearch and data:view.search.label'/>
              <p class='kicker'>Label</p>
              <h1 class='listing__title'><data:view.search.label/></h1>
            <b:elseif cond='data:view.isSearch'/>
              <h1 class='listing__title'>Search results</h1>
            <b:elseif cond='data:view.isArchive'/>
              <p class='kicker'>Archive</p>
              <h1 class='listing__title'><data:blog.pageName/></h1>
            <b:else/>
              <h1 class='listing__title'>All posts</h1>
            </b:if>
          </b:includable>

          <b:includable id='themeCard' var='post'>
            <article class='card'>
              <b:if cond='data:post.featuredImage'>
                <a aria-hidden='true' class='card__media' expr:href='data:post.url' tabindex='-1'>
                  <img alt='' expr:src='data:post.featuredImage' height='427' loading='lazy' width='640'/>
                </a>
              </b:if>
              <div class='card__body'>
                <h3 class='card__title'>
                  <a expr:href='data:post.url'><b:if cond='data:post.title'><data:post.title/><b:else/>Untitled post</b:if></a>
                </h3>
                <p class='card__excerpt'><b:eval expr='snippet(data:post.body, {length: 150, links: false, linebreaks: false})'/></p>
                <p class='card__meta'><b:include data='post' name='themeDate'/></p>
              </div>
            </article>
          </b:includable>

          <b:includable id='themeDate' var='post'>
            <time expr:datetime='data:post.date.iso8601'><b:eval expr='data:post.date format "MMMM d, yyyy"'/></time>
          </b:includable>

          <b:includable id='themeEntry' var='post'>
            <article class='entry shell'>
              <header class='entry__head'>
                <h1 class='entry__title'><b:if cond='data:post.title'><data:post.title/><b:else/>Untitled post</b:if></h1>
                <b:if cond='data:view.isPost'>
                  <p class='entry__meta'>
                    <b:if cond='data:post.author.name'><span>By <data:post.author.name/></span></b:if>
                    <span><b:include data='post' name='themeDate'/></span>
                  </p>
                </b:if>
              </header>
              <div class='post-body'><data:post.body/></div>
              <b:if cond='data:view.isPost and data:post.labels'>
                <footer class='entry__foot'>
                  <ul class='tags'>
                    <b:loop values='data:post.labels' var='label'>
                      <li><a expr:href='data:label.url' rel='tag'><data:label.name/></a></li>
                    </b:loop>
                  </ul>
                </footer>
              </b:if>
              <b:if cond='data:view.isPost'>
                <div class='entry__foot'><b:include name='themePostPager'/></div>
              </b:if>
              <div class='entry-comments'>
                <b:include data='post' name='commentPicker'/>
              </div>
            </article>
          </b:includable>

          <b:includable id='themePostPager'>
            <b:if cond='data:newerPageUrl or data:olderPageUrl'>
              <nav aria-label='More posts' class='pager'>
                <b:if cond='data:olderPageUrl'><a expr:href='data:olderPageUrl' rel='prev'>Previous post</a></b:if>
                <b:if cond='data:newerPageUrl'><a class='pager__next' expr:href='data:newerPageUrl' rel='next'>Next post</a></b:if>
              </nav>
            </b:if>
          </b:includable>

          <b:includable id='themeListPager'>
            <b:if cond='data:newerPageUrl or data:olderPageUrl'>
              <nav aria-label='More posts' class='pager'>
                <b:if cond='data:newerPageUrl'><a expr:href='data:newerPageUrl' rel='prev'>Newer posts</a></b:if>
                <b:if cond='data:olderPageUrl'><a class='pager__next' expr:href='data:olderPageUrl' rel='next'>Older posts</a></b:if>
              </nav>
            </b:if>
          </b:includable>

          <b:includable id='themeNotFound'>
            <div class='listing shell'>
              <div class='notice'>
                <p class='kicker'>Error 404</p>
                <h1 class='listing__title'>Page not found</h1>
                <p>That address is not on this blog. <a expr:href='data:blog.homepageUrl'>Go to the homepage</a> or use the search below.</p>
              </div>
            </div>
          </b:includable>
        </b:widget>
      </b:section>
    </main>

    <footer class='site-foot'>
      <div class='shell'>
        <div class='site-foot__cols'>
          <b:section class='footer' id='footlabels' maxwidgets='1' showaddelement='no'>
            <b:widget id='Label1' locked='false' title='Topics' type='Label' version='2'>
              <b:includable id='main'>
                <h2><data:title/></h2>
                <ul class='label-list'>
                  <b:loop values='data:labels' var='label'>
                    <li><a expr:href='data:label.url'><data:label.name/></a></li>
                  </b:loop>
                </ul>
              </b:includable>
            </b:widget>
          </b:section>
          <b:section class='footer' id='footabout' maxwidgets='2' showaddelement='yes'>
            <b:widget id='HTML2' locked='false' title='About' type='HTML' version='2'>
              <b:widget-settings>
                <b:widget-setting name='content'><![CDATA[<p>Replace this text in Layout with a few lines about the blog.</p>]]></b:widget-setting>
              </b:widget-settings>
              <b:includable id='main'>
                <h2><data:title/></h2>
                <div class='about'><data:content/></div>
              </b:includable>
            </b:widget>
          </b:section>
          <div>
            <h2 id='searchTitle'>Search</h2>
            <form aria-labelledby='searchTitle' class='search' expr:action='data:blog.searchUrl' method='get' role='search'>
              <label class='fx-vh' for='searchField'>Search this blog</label>
              <input id='searchField' name='q' type='search'/>
              <button type='submit'>Search</button>
            </form>
          </div>
        </div>
        <p class='site-foot__note'><data:blog.title/> · <a expr:href='data:blog.homepageUrl + "feeds/posts/default"'>Feed</a></p>
      </div>
    </footer>

    <script>
//<![CDATA[
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
(function () {
  Array.prototype.forEach.call(document.querySelectorAll('.post-body table'), function (table) {
    if (table.scrollWidth <= table.parentNode.clientWidth || table.parentNode.classList.contains('fx-scroll')) return;
    var wrap = document.createElement('div');
    wrap.className = 'fx-scroll'; wrap.tabIndex = 0;
    wrap.setAttribute('role', 'region'); wrap.setAttribute('aria-label', 'Table, scroll sideways to see more');
    table.parentNode.insertBefore(wrap, table); wrap.appendChild(table);
  });
})();
//]]>
    </script>
  </body>
</html>
```

## How to build on it

1. **Keep the bones.** The head order, the section and gadget structure, the Blog gadget's `main` with its three branches, the body script and the first four parts of the skin stay. Replace the tokens, restyle everything, add sections and markup for the design. The header is sticky because most briefs choose a bar that stays visible; for a plain top bar, delete the two `position: sticky` rules. A side menu or a centered header changes the header's CSS, not its markup or script.
2. **Name things for the theme.** Rename the includables (`themeCard`, `themeEntry`…) and the theme's own classes with a short prefix taken from the theme title. Section ids, gadget ids, includable ids and `var` names are letters and numbers only: no hyphens, no underscores. Never give your own includable the name of a [native one](#native-blog-includables), or it replaces Blogger's. These class names stay as they are, because recipes, the menu script and Blogger's own markup select them: everything starting with `fx-`; `ico` and `icon-btn`; the base skin's `shell`, `grid`, `split`, `skip-link`; the header's `site-top`, `site-head`, `site-brand`, `site-nav`; and Blogger's `post-body` and `comments`. Everything else is the theme's own and takes its prefix.
3. **Put the theme title** in the first comment of the head and of the skin, and in the file name. It is not the blog's name: the blog's name comes from `data:blog.title`. The skin opens in this order: the title comment, then the designer-variable comment if the theme has one, then the cancel block as the first CSS rules, then everything else.
4. **Add effects on top**: replace the small head script with the kit's [head gate](effects.md#the-kit), add the kit styles and recipe styles to the skin, and put the kit and recipes into the one body script after the menu block.
5. **Add fonts** as in [responsive](responsive.md#fonts), between the favicon and the skin.
6. **Leave the Blog gadget's native behavior alone**: full post bodies, Blogger's own post addresses, the pager addresses and the comments include.
7. **Give every section a `class`.** Blogger Help names `header`, `main`, `sidebar` and `footer` as the common ones and uses them to carry the owner's gadgets over when they change theme. Use the one that matches where the section sits. The class also lands on the section's generated wrapper, so never use those four words as class names for your own elements.

A Blog gadget prints nothing above or below what its `main` prints. Anything that needs post data, such as a hero showing the newest post, must live inside the Blog gadget's homepage branch, because `data:posts` exists only there. Print the first post in its own markup, then loop the rest:

```xml
<!-- blogger -->
<b:loop index='i' values='data:posts' var='post'>
  <b:if cond='data:i == 0'>
    <b:include data='post' name='themeFeature'/>
  <b:else/>
    <b:include data='post' name='themeCard'/>
  </b:if>
</b:loop>
```

## Blog gadget

Data available inside the Blog gadget. Fields marked “seen” come from the second group in the table above.

| Data | What it is | Basis |
|---|---|---|
| `data:posts` | The posts of this view: one on a post or page, a batch on listings, none on the error view | Documented |
| `data:posts.size` | How many. Use `data:posts.size == 0` for the empty state | Seen |
| `data:post.title`, `data:post.url`, `data:post.body` | Title (may be empty), address, full HTML body | Documented |
| `data:post.labels` with `label.name`, `label.url` | The post's labels | Documented |
| `data:post.date` | The date; print with `<b:eval expr='data:post.date format "MMMM d, yyyy"'/>` | Seen |
| `data:post.date.iso8601`, `data:post.lastUpdated.iso8601` | Machine-readable dates for `time` and structured data | Seen |
| `data:post.author.name`, `data:post.author.profileUrl` | Author | Seen |
| `data:post.featuredImage` | Address of the post's main image, or empty | Seen |
| `data:post.snippets.short`, `data:post.snippets.long` | Ready-made plain-text excerpts | Seen |
| `data:post.numberOfComments`, `data:post.allowNewComments` | Comment count and whether new ones are open | Seen |
| `data:post.url.canonical` | The post's address without `?m=1` or other parameters; used by the share links of [R4](effects-navigation-loading.md#r4-share-links) | Seen |
| `data:olderPageUrl`, `data:newerPageUrl` | Pager addresses; empty when there is no such page | Documented |
| `data:view.isHomepage`, `isPost`, `isPage`, `isSingleItem`, `isMultipleItems`, `isSearch`, `isArchive`, `isError`, `isLayoutMode` | Which view this is | Seen |
| `data:view.search.label`, `data:view.search.query` | The label or the words of a search listing | Seen |
| `data:blog.title`, `pageTitle`, `homepageUrl`, `url`, `languageDirection` | Blog-wide values, available everywhere | Documented |
| `data:blog.pageName`, `searchUrl`, `locale` | The current page's own name, the search address, the blog's language | Seen |
| `.size` on a list | Seen on `data:posts` and `data:links`; the same test is used for `data:labels` | Seen |

Points that are easy to get wrong:

- **One `main`, three branches.** Error first, then single item, then listings. The error view still runs the Blog gadget, with no posts.
- **A static page is a single item** without labels, author line or post pager. Test `data:view.isPost` before printing those.
- **The header image.** A post's featured image is normally also the first image inside its body. Printing `data:post.featuredImage` above the body shows it twice. For a “header image” on the post view, style the first image of the body so it fills the text column: `.post-body > :first-child img { display: block; width: 100%; height: auto; border-radius: var(--radius); }`. It cannot be wider than the column, because the post body is what keeps wide content from spilling.
- **Comments wrapper.** The skeleton puts the dividing line on Blogger's own `#comments` element, not on the wrapper, so a page with comments switched off shows no stray line.
- **Next and previous.** The post pager is `themePostPager`. Delete that includable and its include when the brief did not ask for it.
- **Dates.** `format` takes ICU patterns: `"MMM d"`, `"MMMM d, yyyy"`, `"yyyy-MM-dd"`.
- **Excerpts.** `snippet(data:post.body, {length: 150, links: false, linebreaks: false})` or the ready-made `data:post.snippets.short`. Both are plain text.
- **Images in cards.** The skeleton prints the image address as it is. For lighter pages use `resizeImage` and `sourceSet` exactly as in [Blogger recipe 8](blogger-validation.md#8-modern-responsive-image), which first checks that the image can be resized.
- **Text a visitor typed.** `data:view.search.query` is whatever was in the address bar. Do not print it into the page as markup. If the design must show it, put it in an attribute with `expr:` (for example the search field's `expr:value`) and never build HTML from it in script.
- **Comments.** `<b:include data='post' name='commentPicker'/>` hands over to Blogger's own comment thread and form, which arrive with Blogger's markup and classes (`#comments`, `.comments-content`, `.comment-form`, an `iframe`). Style the wrapper and those classes lightly; do not rebuild the form. The comment form is an `iframe` that is always light, whatever the theme. When the theme is dark or can become dark, give the comments wrapper a fixed light background and dark text of its own, written as plain colors and not as theme tokens, so the form never floats on a dark page.
- **Structured data.** A custom `main` does not print article metadata by itself. Add it only when asked, following [structured data](blogger-design.md#structured-data).

### Native Blog includables

A version-2 Blog gadget inherits Blogger's own includables. Any of them can be called by name from a custom `main` without being defined; defining one with the same id replaces it. The list, from two community references of Blogger's default markup:

`main`, `aboutPostAuthor`, `addComments`, `commentAuthorAvatar`, `commentDeleteIcon`, `commentForm`, `commentFormIframeSrc`, `commentItem`, `commentList`, `commentPicker`, `comments`, `commentsTitle`, `feedLinks`, `feedLinksBody`, `homePageLink`, `iframeComments`, `inlineAd`, `nextPageLink`, `post`, `postBody`, `postBodySnippet`, `postCommentsAndAd`, `postCommentsLink`, `postFooter`, `postFooterAuthorProfile`, `postHeader`, `postJumpLink`, `postMeta`, `postPagination`, `postTitle`, `previousPageLink`, `threadedCommentForm`, `threadedCommentJs`, `threadedComments`.

Use only `commentPicker` from this list, as the skeleton does. The others print Blogger's own markup, which a custom theme has not styled; they are listed so that no custom includable takes one of these names by accident. An include of any other name must be defined in the same gadget: Blogger Help says an include “must match the ID of an existing `b:includable` in the same widget”.

## Navigation gadget

The owner edits the menu in Layout through a `PageList` (their pages) or `LinkList` (any links) gadget. Give it your own `main` so the markup is exactly the list the phone menu styles:

| Data | What it is |
|---|---|
| `data:links` | The list |
| `data:link.title`, `data:link.href` | Text and address (`LinkList` uses `data:link.name` and `data:link.target`) |
| `data:link.isCurrentPage` | True for the page being shown |
| `data:title` | The gadget's own title, usually not shown in a menu |

A brand-new `PageList` holds only “Home” until the owner adds pages, so the bar may be nearly empty on first install. Say so in the delivery note, and do not fill the gap with invented links. Links to parts of the theme that always exist, such as `#searchField`, are fine as fixed items beside the gadget.

## Other gadgets

| Gadget | Data for a custom `main` | Notes |
|---|---|---|
| `Label` | `data:title`, `data:labels` with `label.name`, `label.url`, `label.count` | The category index. Documented by Blogger Help |
| `HTML` | `data:title`, `data:content` | Free content; see below. Documented by Blogger Help |
| `BlogArchive` | `data:title`, `data:data` with `i.name`, `i.url`, `i.post-count` | Documented by Blogger Help. Shape depends on the gadget's own style setting; test both |
| `PopularPosts`, `FeaturedPost` | `data:title`, `data:posts` with the same post fields as Blog | Seen less; verify on save |
| `Header` | Leave it out. `data:blog.title` in the template gives the name and `data:blog.homepageUrl` the link | A logo image is an `Image` or `HTML` gadget |
| `BlogSearch` | Not needed: the plain form in the skeleton does the same with no script | `data:blog.searchUrl` |
| `Attribution` | Do not add one. The hiding rule stays in the skin in case Blogger restores it | See [branding](resources.md#branding-defaults) |

To show a gadget on some views only, put `cond` on its section, as the skeleton's welcome block does, and always include `or data:view.isLayoutMode` so the owner can still edit it in Layout. A section never sits inside a `b:if`, a `b:loop` or any other Blogger tag, and never inside a list, a table, a paragraph or a link: Blogger replaces it with a `div`. Blogger Help also documents a coarser, older way, the gadget attribute `pageType` with the values `all`, `archive`, `main` and `item`.

Blogger's feeds live at `/feeds/posts/default` (all posts) and `/feeds/posts/default/-/LABEL` (one label). A “subscribe” choice with no newsletter provider is a link to the first.

## Owner-editable content

Text the owner will want to change (a welcome line, an about paragraph, a row of numbers, a call to action) goes into an `HTML` gadget, not into the template. The gadget's `b:widget-settings` holds the starting content, and its `main` wraps `data:content` in the theme's markup. The owner then edits it in Layout without touching XML.

- The starting content is ordinary HTML inside `CDATA`. It can use the theme's classes and recipe attributes (`data-fx`, `fx-count`…), so effects apply to it as to any other markup.
- Write real starting text from the interview, never filler. Say in the delivery note which gadgets hold it.
- Blogger tags and data paths are not evaluated inside a gadget's content. A link there is a typed, root-relative address such as `/search/label/Guides` or `/p/about.html`; use only labels and pages the user named, and list each such link in the delivery note so the owner can correct it.
- Content that must never change shape, such as the post list or the menu structure, stays in the template.

## Favicon

The theme carries its own icon as an inline SVG, so no extra file is delivered:

```xml
<link href='data:image/svg+xml,%3Csvg xmlns=%27http://www.w3.org/2000/svg%27 viewBox=%270 0 32 32%27%3E%3Crect width=%2732%27 height=%2732%27 rx=%276%27 fill=%27%231c1917%27/%3E%3C/svg%3E' rel='icon' type='image/svg+xml'/>
```

- Place it after `all-head-content`. That include prints Blogger's own icon link, and browsers that are offered several icons generally take the last suitable one.
- Inside the address, write `%3C` for `<`, `%3E` for `>`, `%27` for a quote and `%23` for `#`. That keeps the attribute valid XML with no further escaping.
- Draw a simple original mark on a 32-unit grid: a letter, a shape from the theme, two colors. It uses the theme's colors as plain values, so when a brand color changes later, change it here too.
- This is a literal `data:image` address, not Blogger data, so it correctly has no `expr:`. The structural check for “literal `data:` values” is about `data:blog…` and `data:post…` written without `expr:`.
- Some browsers and all feed readers use the icon set in Blogger's Settings instead. Tell the owner to upload their icon there as well; that setting is theirs to change.

## Designer variables

Blogger's Theme Designer can change values that the skin declares as variables. Define them only when the user wants to adjust colors from Blogger's Customize screen (the interview's “change colors and fonts from Blogger”). Otherwise use CSS custom properties alone.

```css
/* alt: variables */
/*
<Variable name="bgcolor" description="Page background" type="color" default="#fafaf9" value="#fafaf9"/>
<Variable name="textcolor" description="Text" type="color" default="#1c1917" value="#1c1917"/>
<Variable name="accentcolor" description="Accent" type="color" default="#b45309" value="#b45309"/>
<Variable name="bodyfont" description="Body font" type="font" default="normal normal 18px Georgia, serif" value="normal normal 18px Georgia, serif"/>
*/
:root { --bg: $bgcolor; --ink: $textcolor; --accent: $accentcolor; }
body { font: $bodyfont; }
```

- Blogger Help gives the contract: the definitions sit inside a CSS comment in the skin; each has a `name` of letters and numbers that is unique in the theme, a `description` the owner sees, a `type` of `color` or `font`, and a `default`. A font default has the form style, weight, size, family. Blogger's own exports repeat the default in a `value` attribute, as above.
- A variable is used as `$name`. Blogger's own themes also use dotted names with `$(group.name)`; that form is not in Help, so prefer plain names.
- The comment block comes first in the skin, before the base skin.
- A `$(name)` or `$name` is replaced by Blogger before the browser sees the CSS. This is the only place a `$` may appear in a skin: any other `$` is read as an unknown variable and the theme is rejected.
- Feed each variable into one custom property and use the custom property everywhere else.
- Expose no more than five values. A shade derived from one of them is computed in CSS from its custom property, for example `color-mix(in srgb, var(--accent) 80%, black)`, never declared as another variable.
- With a light-only theme the page background control suits light tints only, because the text color does not follow it; say so in the delivery note.
- A theme that follows the device's light and dark setting cannot expose its colors this way, because one variable holds one value.

## If Blogger refuses to save the theme

Blogger reports the first problem it meets, with a line number. Ask the user for the exact message, fix that one thing, and send the file again. For each construct that is not documented by Blogger itself, this is the fallback:

| If the message points at | Replace it with |
|---|---|
| `commentPicker` | Delete the custom `themeEntry` branch for single items and call Blogger's whole default post markup instead: `<b:include data='this' name='super.main'/>` in the single-item branch. Style its classes (`.post-title`, `.post-body`, `.post-labels`, `#comments`) |
| `format` on a date | `<data:post.date/>`, which prints the date in the blog's own format |
| `.iso8601` | Remove the `datetime` attribute |
| `data:posts.size` | `data:posts.empty` for the empty test, or drop the empty state |
| `cond` on a `b:section` or `b:widget` | Remove the attribute and put a `b:if` with the same condition inside the gadget's `main`, around its markup. For a simple homepage-or-post split, the documented gadget attribute `pageType='main'` or `pageType='item'` also works |
| `b:class` | Remove it and set the class with a documented expression instead: `expr:class='data:link.isCurrentPage ? "is-current" : ""'` on the `li` |
| `snippet(…)` | `<data:post.snippets.short/>` |
| `b:widget-settings` | Remove the block; the owner then types the content in Layout |
| `data:blog.searchUrl` | `expr:action='data:blog.homepageUrl + "search"'`, the documented way to join an address |
| `data:blog.pageName` | `data:blog.pageTitle` |
| `.canonical` on a post address | `data:post.url` |
| `data:blog.locale` on `html` | Remove `expr:lang` and write the blog's language as a plain `lang` attribute |
| `resizeImage`, `sourceSet` or `.isResizable` | Print the image address as it is: `expr:src='data:post.featuredImage'`, as the skeleton does |
| `b:loop` with `index` | Remove the index and whatever depended on it |
| A `$` in the skin | Find the stray `$` in the CSS; only declared designer variables may use it |
| An entity such as `&nbsp;` | The numeric form, `&#160;` |

After any fix, run the [syntax conformance pass](blogger-validation.md#syntax-conformance-pass) again. When the user reports that the theme saved, ask them to look at a post with comments, the menu in Layout, and the phone view, which are the parts a local preview imitates least well.
