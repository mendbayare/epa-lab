# EPA@Lab Website — AI Assistant Knowledge Base

## Project Overview

Laboratory website for **Electrical Power Automation Lab (EPA@Lab)**
at Mongolian University of Science and Technology (MUST), School of
Power and Electrical Engineering (ЭХИС).

## Tech Stack

- **Static Site Generator:** Hugo
- **Theme:** [Ananke](https://github.com/theNewDynamic/gohugo-theme-ananke) (minimal starter theme — easy to customize, extend, and add custom content types)
- **CMS:** Decap CMS (form-based, Git-backed)
- **Hosting:** GitHub Pages or Netlify ($0/month)
- **Domain:** `*.must.edu.mn` subdomain (decided at project end; use
  free domain in the meantime)

Decision rationale: No ongoing maintenance; fast & secure; sufficient
for a lab site.

## Color / Brand

- **Primary:** Blue + White (lab's own choice, not MUST brand)
- **Status:** Mock phase — start with placeholder theme; finalize
  later.

## i18n / Multilingual

- **Languages:** Mongolian (mn) + English (en)
- Mongolian is the default language; English as fallback for
  interface strings
- UI strings in `i18n/mn.toml` and `i18n/en.toml`
- Content translated via Hugo's standard filename convention
  (`page.mn.md`, `page.en.md`)
- Untranslated content falls back to the other language

Rationale: Hugo's i18n is first-class — no plugins needed,
per-language menus, per-language URLs (`/en/about/`, `/mn/about/`).

## Content Architecture

6 pages:

1. **Home** — landing / hero + highlights
2. **About Us** — lab intro, goals, supervisor info, equipment list,
   research areas
3. **Members** — grouped by year/cohort; achievements added later
4. **Research / Projects** — 6 research areas + student projects
5. **News** — publications, training sessions, activities
6. **Contact** — address (VIII building, room 601), phone, map

## Content Model

### Member

```yaml
name        : string
title       : string (e.g. "ЦСА 4-р курсын оюутан")
photo       : image
year        : integer (e.g. 2026 for "I үе", 2027 for "II үе")
achievements: string[] (optional — structured as array of strings,
              each renders as a bullet point; section hides if empty)
social      : optional links
```

Group members by `year` in descending order.

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

Renders inside About Us page via a partial.

### Equipment Item

```yaml
name        : string
category    : string (e.g. "ABB relay", "SEL relay", "Testing")
description : text (optional)
```

Renders inside About Us page via a partial.

## Design Decisions

- Group members by year of membership (e.g. "member of 2026"), NOT by
  Захиргаа/position and NOT by generational label (I үе / II үе)
- Members page shows: name, title, photo, year, brief detail
- Achievements are an **array of strings** — empty = section hidden
- Equipment list lives inside **About Us**, not a separate page
- Research areas also live inside **About Us**
- Training & activities go into **News**
- Branding/color finalized during mock phase

## Source Documents

- `epalab.pdf` — official lab intro (members, equipment, research
  areas, training, contact)
- `Лабораторийн Вэбсайт Хөгжүүлэлт.md` — original project plan

## Staff Training Note

~1 hour admin training session needed. Decap CMS is form-based, not
WYSIWYG drag-drop like WordPress. Staff should be comfortable with:
- Logging into Decap CMS
- Creating/editing posts
- Adding members
- Publishing changes (auto-deploys via Git)

## Epics (Future Work)

### Epic 1: Project Scaffold
- [ ] Initialize Hugo site with Ananke theme
- [ ] Configure for Decap CMS
- [ ] Set up GitHub repo + GitHub Pages
- [ ] Deploy bare site

### Epic 2: Content Implementation
- [ ] Homepage hero + highlights
- [ ] About Us (intro + equipment + research areas)
- [ ] Members page (grouped by year, with photo + achievements)
- [ ] Research / Projects page
- [ ] News section
- [ ] Contact page

### Epic 3: Mock & Iterate
- [ ] Create mock design (blue/white)
- [ ] Show to lab leadership
- [ ] Iterate based on feedback

### Epic 4: Content Population
- [ ] Add all members from `epalab.pdf`
- [ ] Add equipment list
- [ ] Add research areas
- [ ] Add initial news posts (training sessions, activities)
- [ ] Lab supervisor profile

### Epic 5: Domain & Launch
- [ ] Set up `labname.must.edu.mn`
- [ ] Point DNS to GitHub Pages / Netlify
- [ ] Final QA
- [ ] Launch
