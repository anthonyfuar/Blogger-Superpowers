# Interview and Choice Bank

## Conversation rules

Ask in English, using short rounds of two or three plain-language questions. Start with purpose and visual direction, then ask about motion early. Offer numbered choices, combinations, free text, “recommend something” and “show more.” Explain visual names simply. Reuse actual prior answers. If an answer is unavailable, do not invent it or count it as answered.

The lists below are choice banks, not scripts to paste. For each question show about four to six choices that fit what is already known about the project, plus “recommend something” and “show more”; reveal the rest of a bank only when asked. Ask each topic once: where a numbered topic and a focused question below cover the same decision, use the focused wording and treat both as answered.

Normally cover about 20 topics. Ask additional focused questions whenever animation, icons, parallax, loading, interactions or content need clarification; there is no rigid 20-question cap. Skip irrelevant branches after a clear answer. Honor an explicit request to skip the interview, recording assumptions instead. Do not run the theme interview while maintaining this skill.

Every movement, icon, background, 3D, transition and loading choice in this file has a working recipe; the [effects choice index](effects.md#choice-index) lists them by the same wording. Offer only what is listed there, and note the recipe ID beside each chosen effect in the brief. Two choices need something from the user: a 3D scene needs their own Spline scene address and a still image of it, and photo effects need real photographs.

A theme is always responsive, so never ask whether it should adapt to phones. The phone questions below are about priorities.

Moving effects have a budget: at most three things that keep moving on one view, one of which may be a background, and one 3D element. Count as the user chooses. When a choice would go over, say so in plain words and ask which they would rather keep moving; the others can stay as still versions.

Store the working brief in conversation or private temporary scratch, not a shipped JSON file. Distinguish user choices, faithfully translated answers, assumptions, and unknowns. Every chosen motion feature gets a trigger, location, intensity and mobile/reduced-motion alternative. Do not silently replace a requested resource or assume an exported scene exists.

## Opening sequence

This sequence is the single source for question order; SKILL.md and the interview role defer to it.

1. Business or project name, what it offers or publishes, and who it serves.
2. The main visitor goal, the style, and [motion amount](#motion-amount).
3. Icon appearance and behavior, and parallax.
4. Between-page transitions and loading appearance.
5. Layout, palette, type, content and the remaining relevant branches.

Keep each round to two or three questions; split a step into two rounds rather than asking four at once. Existing answers shorten or remove a round. A “no motion” answer in step 2 removes the movement-detail questions from steps 3 and 4; still ask what the icons should look like.

## Topic 1: purpose

What is your business, brand or project called, and what do you offer or write about? What would you most like visitors to do?

Project type: Personal; Travel; Recipes; News; Technology; Art; Fashion; Education; Reviews; Portfolio; Business; Community; Other.

Visitor goal: Read stories; Learn something; Explore my work; Discover my services; Contact me; Visit my shop; Join my community; Other. Ask for real destinations when the chosen action needs a link; do not invent a working service or form.

## Topic 2: audience

Who will read it, and what feeling would you like to convey?

Warmth; Trust; Energy; Calm; Curiosity; Elegance; Fun; Rebellion; Nostalgia; Clarity.

## Topic 3: style

Which style would you like? Choose a main style and, optionally, a supporting one.

Warm minimalist; Black-and-white minimalist; Raw brutalist; Colorful neobrutalist; Editorial maximalist; Colorful and playful; Luxury magazine; Classic newspaper; Geometric Swiss; Bauhaus; Art deco; 1970s retro; 1990s retro; Chrome Y2K; Neon cyberpunk; Space futurist; Digital terminal; Dark academia; Light academia; Natural botanical; Handcrafted and organic; Serene Japandi; Scandinavian; Delicate romantic; Pastel kawaii; Collage and zine; Grunge; Pop art; Comic book; Translucent glass; Museum and gallery; Immersive photography; Personal notebook; Technical and precise; Mediterranean; Tropical; Nighttime elegance; Dynamic sports; Illustrated for children; Understated professional.

## Topic 4: palette

Which family of colors do you like?

Ink and paper; Black and white; Forest; Ocean; Terracotta; Lavender; Dusty pink; Coffee; Editorial blue; Mustard; Cherry; Midnight emerald; Neon violet; Neon lime; Midnight cyan; Charcoal and gold; Candy pastel; Tropical; Sand and sea; Pink clay; Industrial gray; Yellow pop; Red and blue; Mint; Peach; Sky; Wine; Olive; Lilac and lemon; Custom.

## Topic 5: mode

What kind of background would you prefer?

Light; Dark; Follow the reader’s device setting; Light and dark with a toggle.

The last two need the palette in two modes. The palette table gives one; derive the other with the [role table](effects-interaction.md#u2-follow-the-device-setting) and check its contrast.

## Topic 6: typography

How would you like the lettering to look?

Clean and familiar; Elegant magazine; Literary; Geometric; Friendly and rounded; Classic luxury; Newspaper; Technological; Code-inspired details; Strong headlines; Bold poster; Swiss; Humanist; Soft modern; Rounded geometric; Cultural; Handcrafted; Personal handwriting; Nostalgic; Contemporary editorial; Wide and expressive; Long-form reading; Spacious and accessible; No font downloads.

## Topic 7: density

How much space and detail would you like?

Very spacious; Balanced; Compact; Many elements and layers.

## Topic 8: home

How would you like the homepage to look?

Latest posts; Large welcome area and posts; Magazine organized by category; Image gallery; Personal introduction; One featured story; Card mosaic; Editorial list; Homepage without images.

## Topic 9: sections

Which sections would you like, and in what order?

Welcome; Highlights; Latest posts; Categories; About me; Archive; Popular; Search; Gallery; Frequently asked questions; Contact; Social links; Subscription; Services; Testimonials; Advertisements; Topic index; Resources; Legal pages.

## Topic 10: navigation

How would you prefer to move around the blog?

Top bar; Bar that stays visible while scrolling; Side menu; Centered logo with links below; Compact dropdown menu; Large menu organized by category.

## Topic 11: cards

How should posts look on the homepage?

Titles only; Photo above a summary; Photo beside the text; Large image with title below; Uneven mosaic; Bordered cards; Cards with shadows; Separated by lines; One post per row.

## Topic 12: article

What should an opened post include?

Clean reading layout; Header image; Author and date; Estimated reading time; Article contents; Progress bar; Sharing; Comments; Related posts; Next and previous; Sidebar table; Print.

Each of these has working code: reading time, contents, sharing, print and progress are the [reading recipes](effects-navigation-loading.md#r1-reading-progress); related posts is [L6](effects-navigation-loading.md#l6-skeletons-with-a-real-request); author, date, comments and next/previous are part of the [theme skeleton](blogger-theme.md#the-skeleton). “Header image” means the post's own first image shown large; it is styled, not added a second time.

## Topic 13: shape

Which shapes and finishes do you like?

Square corners; Slightly rounded corners; Very rounded corners; Pill-shaped buttons; Thick borders; Thin lines; Soft shadow; Hard offset shadow; Glass; Paper texture.

## Topic 14: motion

Which movements would you like to see? You may choose several. Open with the short [animation families](#animation-families) list; this full menu is its “show more” bank.

No motion; Button interactions only; Gentle fade-in; Reveal on scroll; Staggered entrances; Lifting cards; Gentle photo zoom; Drawing underline; Animated icons; Letter-by-letter text; Changing words; Waving letters; Circular text; Ribbon text; Cylindrical text; Stacking text; Count-up number; Reading progress bar; Manually sliding gallery; Slow carousel; Photos with depth on scroll; Moving layers; Flowing color background; Aurora; Color mesh; Subtle particles; Stars; Light rays and beams; Animated grid; Glowing borders; Shimmering button; Reflective card; Tilting card; Cursor-following light; Magnetic button; Cursor trail; 3D object; Textured text; Liquid distortion; Transition curtain; Image reveal; Paper effect; Placeholder silhouettes during actual loading; Orbiting elements; Gentle spring; Spreading ink.

## Topic 15: intensity

How much movement would you prefer? This is the same decision as [motion amount](#motion-amount), asked early in the opening sequence; do not ask it twice.

None; Very subtle; Balanced; Eye-catching on the homepage only; Experimental, but simpler on mobile.

## Topic 16: mobile

What matters most on a phone?

Quick and comfortable reading; Large photographs; Easy access to categories; Keeping almost the entire design; Using little data.

## Topic 17: brand

Which of your own materials do you already have?

Name and description; Logo; Photographs; Brand colors; A reference website; Nothing yet, suggest a direction.

## Topic 18: features

Which extra features do you need?

None; Search; Newsletter through an existing provider; Form through an existing service; Download links; Videos; Advertisements; Galleries; Category filters; Reading mode.

## Topic 19: assets

What would you prefer for images, icons, and backgrounds?

Photographs; Illustrations; Text only; Outline icons; Filled icons; Animated icons; Geometric shapes; Flowing backgrounds; A 3D scene I already have.

Icon look and movement are settled by [icon appearance](#icon-appearance) and [icon behavior](#icon-behavior); skip the icon choices here once those are answered.

## Topic 20: delivery

Do you already have a theme we should preserve, and what would you like to change later?

New theme; I do not have a file; I have a base XML file; Preserve the logo and colors; Preserve gadgets and menus; Change colors and fonts from Blogger; Change blocks from Layout; Explain everything step by step.

## Focused motion and interaction questions

Ask every relevant unanswered topic below. No-motion users need not answer effect-strength or loader-animation details. A transition request never implies a fake wait, AJAX navigation or a real measurable completion percentage.

### Motion amount

How much movement should your blog have?

Still and calm; tiny details only; smooth and balanced; expressive on the homepage; animated throughout; experimental but simpler on phones.

### Animation families

What kinds of movement would you enjoy?

Soft fades; sliding entrances; springy cards; letters appearing; rotating words; glowing buttons; drawing lines; orbiting shapes; flowing backgrounds; light particles; image reveals; surprise me with a coherent mix.

### Icon appearance

What should the icons look like?

Thin outlines; bold outlines; solid shapes; rounded and friendly; sharp and geometric; two-color; playful hand-drawn; minimal symbols; no decorative icons.

### Icon behavior

When should the icons move?

Never; when pointed at; when focused with the keyboard; when tapped; when a menu opens; gently when they first appear; only important actions.

### Parallax

Would you like photos or background layers to move at different speeds as you scroll?

No; a subtle photo effect; only the welcome section; layered backgrounds; several photo sections; recommend where it fits.

### Parallax strength

How noticeable should that depth feel?

Barely noticeable; gentle; dramatic on large screens; disable it on phones; keep it subtle everywhere.

### Page transitions

When opening another page, what would you prefer to see?

Instant navigation; a quick fade; a color wipe; a sliding panel; a tiny top progress line; recommend the simplest effect that fits.

### Loading appearance

If something actually takes time to load, what should appear?

Nothing extra; a small spinner; a pulsing dot; a brand mark; placeholder cards; a short message; a slim progress line; different styles for page navigation and content.

### Loading timing

When should a loading cue be visible?

Only for real delays; only for images or galleries; only between pages; both; never cover the article.

### Background and 3D

Which background feels right?

Plain color; paper texture; soft gradient; flowing color; tiny particles; a decorative 3D shape; my existing interactive scene; no moving background.

### Pointer and touch

How should buttons and cards react?

Simple color change; lift slightly; tilt gently; glow at the pointer; a small ripple; keep everything still; pointer effects on desktop only.

### Motion boundaries

Where should movement stop?

Keep articles calm; pause once an effect finishes; avoid looping; animate the homepage only; no custom cursor; no full-page overlays; prioritize speed and low data.

## Palettes

Names are user-facing choices. Hex values are internal design starting points; verify final contrast.

| Palette | Background | Text | Accent |
|---|---|---|---|
| Ink and paper | #FAF8F4 | #242424 | #9C3324 |
| Black and white | #FFFFFF | #111111 | #333333 |
| Forest | #F5F4EA | #183F32 | #87601C |
| Ocean | #F2FAFC | #09354A | #00768A |
| Terracotta | #FFF6EC | #442C24 | #A33C25 |
| Lavender | #FAF6FF | #35254A | #7041A6 |
| Dusty pink | #FFF4F5 | #532F40 | #A52C5C |
| Coffee | #F8F1E7 | #38271E | #795338 |
| Editorial blue | #F5F7FC | #172D50 | #244AB5 |
| Mustard | #FFF9E8 | #332B14 | #7A5500 |
| Cherry | #FFF6F3 | #481D26 | #AD183D |
| Midnight emerald | #0D211B | #F2FFF8 | #79E5B0 |
| Neon violet | #161024 | #F8F4FF | #C6A0FF |
| Neon lime | #151A10 | #F8FFE8 | #C4EE65 |
| Midnight cyan | #071B29 | #EAFBFF | #5FDEEE |
| Charcoal and gold | #171717 | #FAF6E8 | #DABB71 |
| Candy pastel | #FFF8EE | #492F38 | #A52A62 |
| Tropical | #F2FFF6 | #143F39 | #AE381B |
| Sand and sea | #FCF6E8 | #223C4C | #276783 |
| Pink clay | #FBEFEA | #512F36 | #983D50 |
| Industrial gray | #EDEDED | #222222 | #A53015 |
| Yellow pop | #FFF4A8 | #242020 | #AE2253 |
| Red and blue | #FDF7F2 | #24326B | #AC2828 |
| Mint | #EFFFF5 | #203F36 | #1C7552 |
| Peach | #FFF1E7 | #4D2B25 | #A74021 |
| Sky | #EEF8FF | #18394E | #176E9F |
| Wine | #27121D | #FFF3F7 | #F4ACC7 |
| Olive | #F6F4E9 | #343922 | #67751D |
| Lilac and lemon | #F7F0FF | #392551 | #74419E |
| Custom | Choose | Choose | Choose |

## Font pairings

Describe the mood first; the user does not need to recognize font names. Verify selected font availability, weights and rights. For “no font downloads” the table gives a serif for headings and the system sans for body; when the user also asks for a serif or bookish feel for long reading, use the serif for body text too and keep the sans for small interface text.

| Mood | Headings | Body |
|---|---|---|
| Clean and familiar | Inter | Inter |
| Elegant magazine | Playfair Display | Source Sans 3 |
| Literary | Lora | Lora |
| Geometric | Space Grotesk | DM Sans |
| Friendly and rounded | Nunito | Nunito Sans |
| Classic luxury | Cormorant Garamond | Montserrat |
| Newspaper | Libre Baskerville | Source Sans 3 |
| Technological | Space Grotesk | IBM Plex Sans |
| Code-inspired details | IBM Plex Mono | IBM Plex Sans |
| Strong headlines | Oswald | Open Sans |
| Bold poster | Anton | Roboto |
| Swiss | Archivo | Archivo |
| Humanist | Manrope | Source Sans 3 |
| Soft modern | Outfit | DM Sans |
| Rounded geometric | Poppins | Open Sans |
| Cultural | Bodoni Moda | Inter |
| Handcrafted | Bitter | Work Sans |
| Personal handwriting | Caveat | Nunito |
| Nostalgic | DM Serif Display | DM Sans |
| Contemporary editorial | Fraunces | Manrope |
| Wide and expressive | Unbounded | Inter |
| Long-form reading | Merriweather | Source Sans 3 |
| Spacious and accessible | Atkinson Hyperlegible | Atkinson Hyperlegible |
| No font downloads | Georgia | system-ui |

## Branching and defaults

Readers/news: article comfort, categories and comments. Photography: proportions, captions and missing-image states. Existing themes: preserve gadgets, obtain the actual XML, and ask only about the requested changes as [editing](editing.md) describes. Forms/newsletters: require real destinations. Spline: require a real exported scene or agree on an alternative. No motion: use icons, type and layout without introducing animations of your own later; if the owner later asks for one movement, add exactly that one. Conflicts: ask one concrete tradeoff rather than inventing priorities.

Blogger platform credit and badges are hidden by default; a non-Blogger favicon is used. These are skill defaults, not questions the user must answer again. Preserve required third-party notices and the site owner’s copyright.
