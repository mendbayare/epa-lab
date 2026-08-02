# EPA@Lab Website — AI Assistant Knowledge Base

## Project Overview

Laboratory website for **Electrical Power Automation Lab (EPA@Lab)**
at Mongolian University of Science and Technology (MUST), School of
Power and Electrical Engineering (ЭХИС).

## Tech Stack

- **Static Site Generator:** Hugo (v0.164.0+extended)
- **Theme:** [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke) (minimal starter theme — easy to customize, extend, and add custom content types; git submodule at `themes/ananke`)
- **CMS:** Decap CMS (form-based, Git-backed)
- **Hosting:** Netlify (free tier, auto-deploys from GitHub `main` on push)
- **Domain:** `*.must.edu.mn` subdomain (decided at project end; use
  free domain in the meantime)
- **Live site:** https://boisterous-buttercream-880ab6.netlify.app/ (Netlify
  auto-generated name; changeable)
- **Deploy config:** `netlify.toml` (build command `hugo --minify`, publish dir `public`)

Decision rationale: No ongoing maintenance; fast & secure; sufficient
for a lab site.

## Color / Brand

- **Primary:** Blue + White (lab's own choice, not MUST brand)
- **Status:** Mock phase — start with placeholder theme; finalize
  later.
- **Custom CSS:** `assets/ananke/css/custom.css`, registered via
  `site.Params.ananke.custom_css = ["custom.css"]` in `hugo.toml`. The
  theme looks up entries under `assets/ananke/css/` (it concatenates them
  into the main CSS bundle — a path like `css/custom.css` would 404).

## i18n / Multilingual

- **Languages:** Mongolian (mn) + English (en)
- Mongolian is the default language; English as fallback for
  interface strings
- UI strings in `i18n/mn.toml` and `i18n/en.toml`
- **Content lives in explicit language directories**:
  - `content/mn/` → Mongolian (`contentDir = "content/mn"`)
  - `content/en/` → English (`contentDir = "content/en"`)
  - URLs: `/about/` (MN), `/en/about/` (EN)
- Section `_index.md` files need matching `translationKey` in both
  languages so Hugo matches them as translations (otherwise the
  language switcher won't appear on section pages)
- Language switcher shows the language code (`en`/`mn`) in nav;
  heading uses i18n key `translations` ("Хэл"/"Languages")

Rationale: Hugo's i18n is first-class — no plugins needed,
per-language menus, per-language URLs (`/en/about/`, `/mn/about/`).

## Content Architecture

6 pages:

1. **Home** — landing / hero + highlights
2. **About Us** — lab intro, goals, supervisor info, equipment list,
   research areas
3. **Members** — grouped by year/cohort; achievements added later
4. **Research / Projects** — student projects with PDF reports
   (`/projects/`); research areas live on About Us
5. **News** — publications, training sessions, activities
6. **Contact** — address (VIII building, room 601), phone, map

## Content Model

Front matter uses a `params_` prefix to avoid colliding with Hugo's reserved
keys (e.g. `title`, `date`). Hugo stores these **literally** under
`.Params.*` — i.e. `params_title` → `.Params.params_title`,
`params_year` → `.Params.params_year`. Do **not** read them as
`.Params.title`/`.Params.year` (the un-prefixed names collide with reserved
keys and return the page title/nothing). Body text is markdown.

### Member

```yaml
title       : string (name)
params_title: string (e.g. "ЦСА 4-р курсын оюутан")
params_photo: image
params_year : integer (e.g. 2025 for "I үе", 2026 for "II үе")
params_supervisor: bool (optional — pins the member in a highlighted card at top)
params_achievements: string[] (optional — structured as array of strings,
              each renders as a bullet point; section hides if empty)
body        : markdown (optional)
build:
  render: "never"   # member files do NOT get their own detail page
```

Group members by `params_year` in descending order. The lab supervisor
(`params_supervisor: true`) is pinned above the year groups.

Member cards are anchor targets (`id` = file slug). Links to members
(e.g. on a project's "Team members") point at `/members/#<slug>` — the
card on the Members page — not to a per-member page. A `:target` CSS glow
highlights the card on arrival (flash-and-fade, in `custom.css`).

### News / Post

```yaml
title   : string
date    : date
body    : markdown
tags    : string[] (optional)
```

### Research Area

```yaml
title       : string
description : markdown
```

Renders inside About Us page via a partial. Data lives in
`content/mn/research/`; this section has **no nav link** (it is a data
source only — the Research nav page is the Projects section below).

### Project (report)

```yaml
title         : string
date          : date
description   : markdown (summary shown on the listing)
params_members: string[] (member slugs, e.g. "demberelzodov"; each is
              resolved to its member page via `site.GetPage` and rendered
              as a link; missing pages are skipped gracefully)
params_pdf    : file (PDF report path, e.g. "uploads/report.pdf")
body          : markdown (optional)
```

Renders as the **Research / Projects** nav page (`/projects/`, `/en/projects/`).
Listing is news-style (date, title, description, linked members), paginated.
The single page embeds the PDF in a native browser viewer (`<iframe>`) plus a
download link.

### Equipment Item

```yaml
title       : string (e.g. "RED670")
params_category: string (e.g. "ABB relay", "SEL relay", "Testing", "Other")
body        : text (optional description)
```

Equipment is **not** in Decap CMS — data lives in `content/mn/equipment/`
markdown files, edited directly in GitHub. Renders inside About Us page
via a partial. Equipment items are in MN only (names are already English).

## Design Decisions

- Group members by year of membership (e.g. "member of 2026"), NOT by
  Захиргаа/position and NOT by generational label (I үе / II үе)
- The lab supervisor (`params_supervisor: true`) is pinned in a highlighted
  card above the year groups
- Members page shows: name, title, photo, year, brief detail
- Achievements are an **array of strings** — empty = section hidden
- Equipment list lives inside **About Us**, not a separate page
- Research areas also live inside **About Us** (not on the Research page)
- Training & activities go into **News**
- Research / Projects page is a **projects** listing with embedded PDF reports
- Branding/color finalized during mock phase
- Equipment list is rendered as a card grid (pending styling)

## Source Documents

- `epalab.pdf` — official lab intro (members, equipment, research
  areas, training, contact)
- `Лабораторийн Вэбсайт Хөгжүүлэлт.md` — original project plan

## Decap CMS

- Config: `static/admin/config.yml`, entry point `static/admin/index.html`
- Backend: `git-gateway` (Netlify Identity — email/password, no OAuth proxy needed)
- Admin URL: `/admin/`
- Collections grouped by type, MN + EN side by side:
  - News (MN + EN), Members (MN + EN), Research Areas (MN + EN),
    Projects (MN + EN)
  - Equipment is NOT in the CMS (static data in GitHub)
- Media uploads (PDFs, images) go to `static/uploads/`, served at `/uploads/`
- The Projects collections use a `file` widget for the PDF report and a
  `relation` widget (→ Members collection) for associated members
- MN collections write to `content/mn/<section>/`, EN collections to
  `content/en/<section>/`

## Staff Training Note

~1 hour admin training session needed. Decap CMS is form-based, not
WYSIWYG drag-drop like WordPress. Staff should be comfortable with:
- Logging into Decap CMS
- Creating/editing posts
- Adding members
- Publishing changes (auto-deploys via Git)

## Epics (Future Work)

### Epic 1: Project Scaffold ✅
- [x] Initialize Hugo site with Ananke theme
- [x] Configure for Decap CMS
- [x] Set up GitHub repo + Netlify hosting
- [x] Deploy bare site

### Epic 2: Content Implementation ✅
- [x] Homepage shows only news posts (`mainSections = ["news"]`)
- [x] About Us (intro + equipment + research areas) — custom section template + partials
- [x] Members page (supervisor pinned + grouped by year, photo + achievements) — custom section template + card partial
- [x] Research / Projects page (news-style listing + native PDF viewer on single pages)
- [x] News section listing (date, title, summary)
- [x] Contact page (address + Google Maps embed)

### Epic 3: Mock & Iterate
- [ ] Create mock design (blue/white)
- [ ] Show to lab leadership
- [ ] Iterate based on feedback

### Epic 4: Content Population ✅ (from `epalab.pdf`)
- [x] Add all members from `epalab.pdf` (14 members, MN)
- [x] Add equipment list (20 items, MN)
- [x] Add research areas (6 areas, MN)
- [x] Add initial news posts (4 training activities, MN)
- [x] Lab supervisor profile (Б.Түвшинбаяр)
- [ ] English content still needs population

### Epic 5: Domain & Launch
- [ ] Set up `labname.must.edu.mn`
- [ ] Point DNS to Netlify
- [ ] Final QA
- [ ] Launch
