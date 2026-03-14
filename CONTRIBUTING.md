# contributing to rhoweb.github.io

this is the website for **ramaiah hardware org (rho)**. it's built with [Zola](https://www.getzola.org/), a fast static site generator.

## quick start

```bash
# install zola (macOS)
brew install zola

# dev server with live reload
zola serve

# build for production
zola build
```

the site builds to `public/` and deploys to github pages from the `main` branch.

## project structure

```
.
├── config.toml              # zola configuration
├── content/
│   ├── blogs/               # blog posts
│   │   ├── _index.md
│   │   └── <slug>/index.md
│   ├── events/              # event announcements
│   │   ├── _index.md
│   │   └── <slug>/index.md
│   └── divisions/           # the four org divisions
│       ├── _index.md
│       ├── alpha/index.md   # α  analog & rf design
│       ├── lambda/index.md  # λ  rtl & verification
│       ├── phi/index.md     # ϕ  physical design
│       └── psi/index.md     # ψ  emerging silicon tech
├── templates/
│   ├── base.html            # master template (JS, math, footer)
│   ├── index.html           # landing page + division navbar
│   ├── blog.html            # blog listing
│   ├── blogpost.html        # individual blog post
│   ├── events.html          # events listing
│   ├── eventpost.html       # individual event post
│   ├── divisions.html       # divisions listing (2x2 grid)
│   └── division.html        # individual division page
└── static/
    ├── favicon.ico
    ├── logo.svg
    ├── rho.svg
    ├── css/static.css       # all site CSS
    └── fonts/
        ├── fa-solid-900.woff2
        └── fa-brands-400.woff2
```

## adding content

### new blog post

create `content/blogs/<slug>/index.md`:

```toml
+++
title = "your post title"
date = 2026-04-01
+++

your markdown content here.
```

images go in the same folder and can be referenced as `![alt](image.png)`.

### new event

create `content/events/<slug>/index.md`:

```toml
+++
title = "event name"
date = 2026-04-01
[extra]
hide_toc = true   # optional: hides the table of contents
+++
```

### new division subpage

division pages live in `content/divisions/<slug>/index.md` and use these front matter fields:

```toml
+++
title = "division name"
date = 2026-03-10
[extra]
symbol = "α"                          # greek letter (α λ ϕ ψ)
short = "alpha"                       # url-friendly name
tagline = "short description"         # shown in nav cards
description = "longer description"    # metadata
+++
```

if you add a new division, you also need to add it to the navbar grid in `templates/index.html`.

### adding a person

all team members are defined in `content/people/_index.md` as TOML front matter. to add someone:

1. drop their photo in `content/people/photos/<firstname>.png`
2. add an entry to the appropriate `[[extra.groups]]` in `content/people/_index.md`:

```toml
{ name = "Full Name", photo = "firstname.png", role = "role title", chip_team = true }
```

set `chip_team = true` for chip design team members (shown with a teal border). to add a new group, append another `[[extra.groups]]` block.

## templates

all templates extend `base.html` using Tera's `{% extends %}` / `{% block %}` system.

### base.html

contains everything shared across pages:
- **JS** (image zoom, code copy button)
- **math rendering** (KaTeX CSS + MathJax with custom code-block handling)
- **footer** (rho symbol, email, instagram, discord)

exposes two blocks: `{% block head %}` for page-specific additions and `{% block content %}` for page body.

### static/css/static.css

all site CSS lives here. organized into sections:
- fonts (Font Awesome 6 face declarations)
- base (body, `.rho`, `hr`)
- links
- breadcrumbs
- footer & contact
- images & zoom
- code blocks & copy button
- line numbers (giallo)
- table of contents & counters
- blog post heading counters
- post/event listing (`.post-list`, `.post-item`)
- landing page layout
- division navbar, grid, cards, hero
- responsive (single `@media` block at the bottom)

### post/event listings

blog and event index pages (and the homepage previews) use a shared `.post-list` / `.post-item` pattern:

```html
<div class="post-list">
    <a href="..." class="post-item">
        <span class="post-date">2026-01-20</span>
        <span class="post-title">post title</span>
    </a>
</div>
```

each item is a ruled row with the date left-aligned and the title to its right. on hover the row highlights with a light teal background and the title turns teal. on mobile the date stacks above the title.

### breadcrumbs

every page (except the landing page) shows a small gray path above the title:

```
/blogs/workshop1
```

these are built manually in each template using `page.slug` — not auto-generated.

## styling

### colors

| token | hex | usage |
|-------|-----|-------|
| teal | `#4C9D9B` | primary — links, accents, division symbols, rho branding |
| text | `#111` | body text |
| gray | `#888` | breadcrumbs, secondary text |
| light gray | `#f8f8f8` | toc background |
| border | `lightgrey` | `<hr>` elements |
| white | `#ffffff` | page background |

### typography

the entire site uses `'Lucida Console', monospace` at 18px base size.

### responsive

single breakpoint at `600px`:
- division navbar: 4 columns → 2 columns
- division card grid: 2 columns → 1 column
- post list items: inline (date + title) → stacked
- footer contact: inline → stacked

## javascript features

both scripts are inline at the bottom of `base.html`:

1. **image zoom** — click any non-SVG image to toggle between 40% and 100% width
2. **code copy button** — hover over any `<pre>` block to reveal a "copy" button

## math support

pages support LaTeX math via MathJax:
- inline: `\( ... \)`
- display: `$$ ... $$` or `\[ ... \]`

a custom MathJax startup script strips math delimiters from `<pre>` and `<code>` elements so code blocks aren't accidentally rendered as math.

## the four divisions

| symbol | slug | full name | focus |
|--------|------|-----------|-------|
| α | alpha | analog and rf design | op-amps, PLLs, RFICs, SPICE |
| λ | lambda | rtl and verification | SoCs, digital logic, HDL, UVM |
| ϕ | phi | physical design | synthesis, P&R, tapeout, OpenROAD |
| ψ | psi | emerging silicon tech | device physics, photonics, quantum |

division symbols appear in three places:
- the 4-column navbar on the landing page (`templates/index.html`, hardcoded)
- the division listing grid (`templates/divisions.html`, from `page.extra.symbol`)
- the division hero header (`templates/division.html`, from `page.extra.symbol`)

## deployment

the site is hosted on github pages at `https://rhoweb.github.io`. push to `main` to deploy.

```bash
# check the build locally before pushing
zola build
```
