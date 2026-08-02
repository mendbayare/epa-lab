# EPA@Lab — Content Architecture & UI Pattern Guide

The content-side foundation of the site's design system (a component/pattern
inventory). It maps every content type to a repeatable UI pattern so the design
stays consistent, scales as staff add content, and each component has a defined
home. This is the blueprint Epic 3 (mock design) executes against.

## 1. Content inventory

| Section | Items | Front matter | Currently rendered as |
|---|---|---|---|
| Home | 1 | `_index` body | Hero/prose + recent news |
| About | 1 | prose | Prose blocks |
| Research Areas | 6 | `title`, `description` | `epalab-card` cards (3-up) |
| Equipment | 20 | `title`, `params_category` | Grouped `<ul>` list → **needs grid** |
| Members | 14 | `params_title/photo/year/supervisor/achievements` | Card grid, year-grouped, supervisor featured |
| Projects | 2 | `date/description/params_members/params_pdf` | News-style cards + PDF viewer single |
| News | 4 | `date`, body, `tags` | Cards with date/summary + article single |
| Contact | 1 | prose | Info block + map |

Key facts that constrain design:
- Content lives in MN + EN (`content/mn/`, `content/en/`) — every component must
  handle variable text length (Mongolian is longer than English).
- Members, research areas, and equipment render inside section pages — no
  per-item detail pages (members have `build.render: "never"`).
- Projects are the only items with detail pages (PDF reports).
- No images are uploaded yet (member photos unset) — components must degrade
  gracefully when media is missing.

## 2. UI pattern map (content → component)

Each component has one job. Keep them composable and reuse the base card.

| # | Pattern | Used for | Anatomy / notes |
|---|---|---|---|
| P1 | **Hero** | Home | Title + tagline + highlight list; no cards |
| P2 | **Prose section** | About intro/goals/outcomes | Wide measure, markdown, nested headings |
| P3 | **Area card** | Research areas | Title + `description`; 3-up grid |
| P4 | **Equipment grid** | Equipment | Categorized card grid — see §3 |
| P5 | **Member card** | Members | Photo → name → title → achievements; `:target` glow; featured variant for supervisor |
| P6 | **Project card** | Projects list | Date + title + description + member chips |
| P7 | **PDF viewer** | Project single | Inline `<iframe>` + download link |
| P8 | **News card** | News list | Date + title + summary + read-more |
| P9 | **Article** | News single | Standard post layout |
| P10 | **Contact block + map** | Contact | Info block + embedded iframe |
| P11 | **Nav + language switcher** | Global | Menu per language, `Хэл/Languages` toggle |

## 3. The equipment grid solution

**Problem:** 20 items, unevenly split (ABB 4, SEL 7, Testing 2, Other 7). A plain
`<ul>` under 4 headings is hard to scan.

**Recommended: categorized card grid** (CSS-only, no JS):
- Keep the 4 category group headings (ABB relay / SEL relay / Testing / Other)
  in the defined order
- Each item is a compact card: **model name** bold + a category-tinted accent,
  optional one-line description below
- Layout with CSS Grid `repeat(auto-fill, minmax(180px, 1fr))` — auto-adapts
  1→5 columns, no breakpoint math
- Reuse `.epalab-card` so it visually matches members/research

**Alternatives** (choose only if scope grows):
- **Filterable grid** — category chips (All/ABB/SEL/Testing/Other) + small
  progressive-enhancement JS; usable without JS
- **Data table** — `Model | Category | Description` rows; better once items gain
  metadata (manufacturer, year, status)
- **Chip cloud** — compact name pills; weakest scannability

**Decision:** categorized card grid now; add filtering only if item count
exceeds ~30.

## 4. Interaction & style conventions

- **Cards**: white, 1px border `#e2e8f0`, radius 8px, soft blue hover lift
- **Anchor targeting**: smooth scroll + flash-and-fade `:target` glow (built)
- **Colors**: `--epa-blue` CSS tokens as the single source of truth
- **Responsive**: CSS grid auto-fill + Tachyons `w-100/w-30-l`
- **Localization**: all component labels via i18n keys (`mn`/`en`)

## 5. Open design questions for the mock (Epic 3)

- Hero treatment (image vs. flat blue band) — no media assets exist yet
- Where member photos come from (CMS `image` widget → `static/uploads/`)
- Equipment card density vs. table once descriptions are written
- Footer content (contact summary / socials — currently unused)
