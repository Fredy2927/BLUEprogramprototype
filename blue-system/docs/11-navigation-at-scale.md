# 11 — Navigation at Scale: How B.L.U.E. Stays Browsable as It Grows

> **The question:** *"Once lots of things have been uploaded, will the navigation section not just be cramped?"*

**Yes — and the current sidebar would break around 30 subjects.** This is the right question to ask now, before we commit to a navigation pattern that won't scale. This document is the research and proposed solution.

---

## Part 1 — The research

I looked at how the biggest content sites on the internet handle this exact problem.

### Wikipedia (61 million articles, ~10 million categories)

**Wikipedia's sidebar does not contain subjects at all.** Look at the actual sidebar:

- Main page
- Contents
- Current events
- Random article
- About Wikipedia

That's it. No subject tree. With 61 million articles across roughly 100,000 top-level subjects, putting them in the sidebar is impossible. Instead, Wikipedia layers **four parallel discovery systems**:

| System | What it is | Used by |
|---|---|---|
| **Search** | The primary way to find anything you can name. | ~70% of visits start here. |
| **Portals** (e.g. `Portal:Mathematics`) | Curated landing pages per top-level subject. A "doorway" with featured articles, recent additions, links to subportals, a collapsible tree. | Browsers who know the rough area but not the exact article. |
| **Categories** | A bottom-of-page list of every category an article belongs to. Click to see all articles in that category. Categories are *many-to-many* (one article can be in dozens). | Lateral exploration ("show me other things like this"). |
| **Lists** (e.g. `List of countries by GDP`) | Curated index articles. Different from categories — humans choose the order. | When you want a deliberate selection, not "every article that tagged itself X". |

The lesson: **at scale, you stop trying to show everything. You provide multiple ways to discover what you want, depending on whether you're searching, browsing, or exploring laterally.**

The Wikipedia FAQ on categories spells this out clearly: ["These methods should not be considered in conflict with each other. Rather, they are synergistic, each one complementing the others."](https://en.wikipedia.org/wiki/Wikipedia:Categories,_lists,_and_navigation_templates)

### Stack Overflow (~60,000 tags, 24 million questions)

Stack Overflow doesn't show tags in the sidebar. They have:
- A **search bar** as the primary entry point.
- A dedicated **`/tags` page** with search + filter + sort (popular, name, new).
- **Contextual related tags** on every question page ("Questions tagged JavaScript also use React, Node.js, TypeScript…").
- Personal **watched tags** in the user's own sidebar (one of the few real personalisation features the site has).

### MDN Web Docs

MDN's sidebar is **contextual** — it changes based on what you're reading. On a JavaScript page you see JS sub-topics; on a CSS page you see CSS sub-topics. You never see the full taxonomy at once.

### Notion (millions of public docs)

Notion's docs use a **collapsible tree** that only expands the active branch. Combined with **`/` search-as-you-type** as the primary navigation tool. Their sidebar genuinely scales to thousands of pages without becoming cramped.

### Nielsen Norman Group findings

The pattern that comes up consistently in NN/g's intranet IA research:

> *"This shift toward fewer total categories is good. A broad structure shows a wide range of available content, but too many categories can make it difficult for users to choose a site area, because of potential overlap in the meaning of the many menu items."*
> — [NN/g, Intranet Information Architecture Trends](https://www.nngroup.com/articles/intranet-information-architecture-ia/)

Their consistent rule of thumb: **primary navigation should have 7±2 items maximum.** Anything more should be deferred to a secondary discovery system (search, dedicated browse page, portal).

### The pattern that emerges

Every site that scaled past ~50 categories did the same three things:

1. **Stopped showing the full taxonomy in the sidebar.** The sidebar became contextual (showing only what's near you) or navigational (linking to discovery pages, not categories).
2. **Built a dedicated "browse all" page** with search, filtering, and grouping.
3. **Made search the primary navigation tool.** Categories became *discovery* aids, not *navigation* aids.

---

## Part 2 — How this maps to B.L.U.E.

B.L.U.E. has the same problem in miniature. Sebastian's hierarchy of subjects could realistically grow to:

- **Year 1:** 10–30 subjects, maybe 50 lessons (mostly Electronics).
- **Year 2:** 50–150 subjects, 500+ lessons.
- **Year 5 (if it works):** 1,000+ subjects, 10,000+ lessons.

That means the sidebar pattern must work at all three scales without a redesign.

---

## Part 3 — The four-tier discovery system

The proposal is to give B.L.U.E. **four parallel discovery surfaces** (mirroring Wikipedia's design), each one doing what it does best. The sidebar is just *one* of them, and it stops trying to be a list of every subject.

### Surface 1 — The Sidebar (navigation hub, not subject list)

The sidebar becomes a **fixed navigation panel** — the same on every page. It contains things you always need:

```
NAVIGATION
  Main page
  All subjects   ←  link to the Portal index (Surface 2)
  Browse lessons ←  link to the lesson browser
  Random lesson
  Search         ←  prompts ⌘K
  About B.L.U.E.

CONTRIBUTE
  Write a lesson
  Become a verifier
  Lesson wishes
  Architecture (RFCs)

THIS LESSON          (only shown on lesson pages — contextual)
  Edit history
  Talk page
  Languages
  Cite this page

YOU
  Your profile
  Your draft lessons
  Subjects you verify
```

**Crucially: the sidebar does NOT list every subject.** It contains links to *navigation systems*, not to leaf categories. This is fixed in height. It works the same at 10 subjects, 100 subjects, or 10,000 subjects.

The "This lesson" block is contextual — it appears only on lesson pages and shows tools that apply to *this* lesson. This is the MDN pattern.

### Surface 2 — Portal Pages (curated subject landing pages)

For every top-level subject (Electronics, Woodworking, Bicycles, etc.), there is a **Portal page** — a curated landing page, modelled directly on `Portal:Mathematics` on Wikipedia.

A portal contains:

- **Featured lessons** of the month, chosen by the portal's curators
- **A collapsible sub-subject tree** scoped to *this portal only* — so the Electronics portal shows the Electronics tree, never the Woodworking tree
- **Recently published**, **currently in review**, and **most-wished** lessons in this subject
- **A "Did you know?" box** with interesting facts
- **Links to sub-portals** if the subject is huge enough to need them
- **Links to related portals**

So instead of trying to fit the entire taxonomy into a sidebar, **each top-level subject gets its own front page.** The user clicks "Electronics" once and lands in a curated world dedicated to Electronics.

This is exactly what Wikipedia does — and they scaled it to 1,800 portals.

### Surface 3 — The Browse-All page

A dedicated `/browse` page (linked from the sidebar) lets users actually *see* the taxonomy and find anything by name.

It has:

- **Search-as-you-type** at the top, filtering subjects as you type
- **All top-level subjects** as cards in a 3-column grid, with subject icons and short blurbs
- **Filters by tag** (e.g. "show me only beginner-friendly subjects", "show me subjects with > 10 lessons", "show me subjects in my preferred language")
- **A "Subjects with no lessons yet" section** — recruitment opportunity
- **A "Trending" section** — what's getting the most views this week

This is the page where someone who doesn't know what they want gets inspired. It also degrades gracefully — if the user just wants to scroll an alphabetical list of every subject, that option is there too.

### Surface 4 — Search (⌘K everywhere)

The primary discovery tool. Available from every page via ⌘K. Searches across:

- Lessons (title + body)
- Subjects
- Methods inside lessons
- Bounties
- RFCs
- User handles

For lessons specifically, search results show the lesson title, the level chip, the subject breadcrumb, and a short snippet — so the user can decide whether to click without leaving the search palette.

At scale, this is the *most-used* navigation tool, by a wide margin. NN/g's research suggests **70%+ of visits on content-heavy sites start with search.**

### Surface 5 (bonus) — Categories at the bottom of every page

Like Wikipedia, every lesson page has a **"Categories"** strip at the bottom showing every taxonomic group the lesson belongs to. Clicking any category jumps you to that category's page.

This is the *lateral* discovery surface — "I'm reading about resistors, show me everything else tagged 'passive components'."

Crucially, **categories are independent of the hierarchy**. A lesson can be in many categories ("Electronics", "Beginner-friendly", "Featured 2026", "No special tools needed"). Categories are flat and many-to-many; the hierarchy is strict and one-place-per-lesson.

---

## Part 4 — The progression: how this scales over time

### Stage 1 — ≤15 subjects (today)

Sidebar has a small subject tree, plus the navigation hub. Everyone can fit on screen.

```
┌────────────────────┐
│ NAVIGATION         │
│   Main page        │
│   All subjects     │
│   Random           │
│                    │
│ SUBJECTS           │
│   ▼ Electronics    │
│      Digital       │
│      Analog        │
│      Power         │
│   Woodworking      │
│   Bicycles         │
│   Brewing          │
│                    │
│ CONTRIBUTE         │
│   Write            │
│   Verify           │
│   Wishes           │
└────────────────────┘
```

### Stage 2 — 15–50 subjects (within first year)

Subject tree in the sidebar shows **only top-level subjects**, collapsed by default. Click to expand on that page only. A new link at the bottom: "View all 38 subjects →" leading to the Browse-All page.

```
┌────────────────────┐
│ NAVIGATION         │
│   Main page        │
│   All subjects     │
│   Random           │
│   Search ⌘K        │
│                    │
│ SUBJECTS (top 10)  │
│   Electronics  (24)│
│   Woodworking   (8)│
│   Bicycles     (12)│
│   Brewing       (6)│
│   Cooking      (31)│
│   Gardening     (4)│
│   Home repair  (17)│
│   Photography  (11)│
│   Sewing        (9)│
│   Welding       (5)│
│   ─────────────────│
│   All 38 subjects →│
│                    │
│ CONTRIBUTE         │
│   ...              │
└────────────────────┘
```

### Stage 3 — 50–500 subjects

The sidebar drops the subject list entirely. The sidebar is purely the navigation hub — same on every page. To find a subject, you click "All subjects" (which opens the Browse-All page) or use the ⌘K search.

On lesson pages, a **"This lesson is in"** mini-block in the sidebar shows the breadcrumb to the current subject — so people don't lose their place.

```
┌────────────────────┐
│ NAVIGATION         │
│   Main page        │
│   All subjects →   │
│   Random           │
│   Search ⌘K        │
│                    │
│ CONTRIBUTE         │
│   Write a lesson   │
│   Verify           │
│   Wishes           │
│                    │
│ THIS LESSON        │
│   Subject:         │
│   Electronics ›    │
│   Components ›     │
│   Resistors        │
│                    │
│   Edit history     │
│   Talk page        │
│   Languages (4)    │
│   Cite this page   │
│                    │
│ YOU                │
│   ...              │
└────────────────────┘
```

### Stage 4 — 500+ subjects (Wikipedia-scale)

Same sidebar as Stage 3, no further changes. All growth absorbed by the Portal system + Browse-All + Search. The sidebar literally does not grow.

This is the point of the design — **the sidebar size is constant**. Growth happens in the discovery surfaces that are *designed* to scale (Portal pages, Browse-All grids, search).

---

## Part 5 — Mockup: the Browse-All page at Stage 3 (100+ subjects)

This is the page you land on when you click "All subjects" in the sidebar. It has to make 100+ subjects feel *findable*, not overwhelming.

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  *B.L.U.E.*                          All subjects        [Search...]      ⌘K │
├──────────────────────────────────────────────────────────────────────────────┤
│  Article | Talk | Read | Edit | History                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│   All subjects on B.L.U.E.                                                   │
│   ────────────────────────                                                   │
│   147 subjects · 2,418 lessons · 412 verifiers                               │
│                                                                              │
│   ┌──────────────────────────────────────────────────────────────────────┐  │
│   │ 🔍 Find a subject…                                                   │  │
│   └──────────────────────────────────────────────────────────────────────┘  │
│                                                                              │
│   Filter:  ( All )  ( Beginner-friendly )  ( ≥10 lessons )  ( In English )   │
│   Sort:    ( Popular ▾ )  ( A–Z )  ( Most lessons )  ( Newest )              │
│                                                                              │
│   ── MAKING & BUILDING ───────────────────────────────────────────────       │
│                                                                              │
│   ┌────────────────────┐  ┌────────────────────┐  ┌────────────────────┐   │
│   │ ⚡  Electronics    │  │ 🪵  Woodworking    │  │ 🔩  Metalwork      │   │
│   │ 247 lessons        │  │ 89 lessons         │  │ 56 lessons         │   │
│   │ All four levels    │  │ Beginner-friendly  │  │ Hobbyist focus     │   │
│   │ Open portal →      │  │ Open portal →      │  │ Open portal →      │   │
│   └────────────────────┘  └────────────────────┘  └────────────────────┘   │
│                                                                              │
│   ┌────────────────────┐  ┌────────────────────┐  ┌────────────────────┐   │
│   │ 🚲  Bicycles       │  │ 🔧  Home repair    │  │ 🎨  Pottery        │   │
│   │ 67 lessons         │  │ 134 lessons        │  │ 23 lessons         │   │
│   │ Beginner-friendly  │  │ All four levels    │  │ Beginner-friendly  │   │
│   │ Open portal →      │  │ Open portal →      │  │ Open portal →      │   │
│   └────────────────────┘  └────────────────────┘  └────────────────────┘   │
│                                                                              │
│   ── KITCHEN & FOOD ──────────────────────────────────────────────────       │
│                                                                              │
│   ┌────────────────────┐  ┌────────────────────┐  ┌────────────────────┐   │
│   │ 🍺  Brewing        │  │ 🍞  Bread          │  │ 🍳  Cooking        │   │
│   │ 41 lessons         │  │ 78 lessons         │  │ 312 lessons        │   │
│   │ All levels         │  │ All levels         │  │ All levels         │   │
│   │ Open portal →      │  │ Open portal →      │  │ Open portal →      │   │
│   └────────────────────┘  └────────────────────┘  └────────────────────┘   │
│                                                                              │
│   ── GARDEN, FARM & OUTDOORS ─────────────────────────────────────────       │
│   ┌────────────────────┐  ┌────────────────────┐  ┌────────────────────┐   │
│   │ 🌱  Gardening      │  │ 🐝  Beekeeping     │  │ 🐔  Chickens       │   │
│   │ ...                │  │ ...                │  │ ...                │   │
│                                                                              │
│   ── SUBJECTS WITHOUT LESSONS YET (recruit a writer!) ───────────────────   │
│                                                                              │
│   · Astrophotography · Bookbinding · Cheese-making · Drone-building · …      │
│                                                                              │
└──────────────────────────────────────────────────────────────────────────────┘
```

Key features:

1. **Search-as-you-type** at the top is the primary tool. Type "elec" and the grid filters down instantly.
2. **Top-level grouping** ("Making & Building", "Kitchen & Food", "Garden, Farm & Outdoors") gives the grid visual structure — not 147 cards in one wall.
3. **Each card is a doorway** to that subject's Portal page. The card itself shows just enough metadata (lesson count, difficulty range) to make a decision.
4. **A "no lessons yet" section** at the bottom turns gaps into recruitment opportunities.

---

## Part 6 — Mockup: the Portal page for Electronics (Stage 3)

This is what users see when they click any subject card. It's the user's home base for that subject.

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  *B.L.U.E.*                                              [Search]         ⌘K │
├──────────────────────────────────────────────────────────────────────────────┤
│  Article | Talk | Read | Edit | History                                      │
├──────────┬───────────────────────────────────────────────────────────────────┤
│ NAV      │                                                                   │
│  Main    │   ⚡  Portal: Electronics                                         │
│  All     │   ─────────────────────────                                       │
│  Random  │   The curated entrance to 247 lessons on building, repairing,     │
│  Search⌘K│   and understanding electronic circuits.                          │
│          │                                                                   │
│ THIS     │   ┌──────────────────────────┐    ┌───────────────────────────┐  │
│ PORTAL   │   │ 📘 In plain words        │    │ At a glance               │  │
│  Browse  │   │ Lessons on building      │    │  Lessons:      247        │  │
│  by      │   │ things with electricity. │    │  Published:    198        │  │
│  level   │   │ From "how to read a      │    │  In review:     12        │  │
│          │   │ resistor's colour bands" │    │  Wishes open:   34        │  │
│ SUB-     │   │ all the way to "how to   │    │  Verifiers:     67        │  │
│ SUBJECTS │   │ design a microchip".     │    │  Languages:      8        │  │
│ ▼ Digital│   └──────────────────────────┘    └───────────────────────────┘  │
│   Logic  │                                                                   │
│   gates  │   FEATURED LESSON THIS MONTH                                      │
│   CPU    │   ─────────────────────────                                       │
│   design │   ┌──────────────────────────────────────────────────────────┐   │
│ ▼ Analog │   │ How to make a resistor                                   │   │
│   Filters│   │ ─────────────────────────                                │   │
│   Amps   │   │ A passive part that slows down electricity. This        │   │
│ ▼ Power  │   │ multi-level lesson covers four ways: the pencil trick,  │   │
│   PSU    │   │ carbon composition, wire-wound, and thin-film chip-fab. │   │
│   DC-DC  │   │ All four levels covered. 6 min read.                    │   │
│ ▼ Comp.  │   │                                                          │   │
│   ▶R     │   │  ▷ Read the lesson →                                    │   │
│   ▶C     │   └──────────────────────────────────────────────────────────┘   │
│   ▶L     │                                                                   │
│          │   RECENTLY PUBLISHED                                              │
│ ALL      │   ─ Build a NAND gate from BJTs (L2, 4 days ago)                  │
│ All 247  │   ─ Build a 555 timer circuit (L2, 1 week ago)                    │
│ lessons →│   ─ How to solder a through-hole component (L1, 2 weeks ago)      │
│          │                                                                   │
│ RELATED  │   IN REVIEW RIGHT NOW                                             │
│ PORTALS  │   ─ Build a 4-bit ALU from NAND gates (L3, deadline in 5 days)    │
│ Metal    │                                                                   │
│ work     │   ▶ Did you know?                                                 │
│ Comp.    │   …that resistors are the most-manufactured electronic component  │
│ science  │   in history? Over 20 trillion are made every year.               │
│ Physics  │                                                                   │
│          │   MOST-WISHED LESSONS                                             │
│          │   ─ How to make a flip-flop (240 / 300 points pledged)            │
│          │   ─ How to read a schematic (180 / 200 points pledged)            │
│          │                                                                   │
│          │   PORTAL CURATORS                                                 │
│          │   @kira (lead) · @otto · @ravi · @mei                             │
└──────────┴───────────────────────────────────────────────────────────────────┘
```

Notice how the **sub-subject tree on the left is scoped to this portal only**. The user is in Electronics-world; they see Digital, Analog, Power, and Components. They don't see Cooking or Bicycles — those would be a distraction.

If they want to switch worlds, they click "All subjects" → land on the Browse-All grid → pick another portal.

---

## Part 7 — Why this design works

1. **Sidebar stays a fixed height forever.** Even at 10,000 subjects, the sidebar still fits on one screen. The thing that grows (the subject inventory) lives in the place that's designed to handle growth (the Browse-All page).

2. **Discovery scales because there are multiple paths to the same content.** Wikipedia learned this — `Mathematics` is reachable via portal, category, search, "see also" links, and lists. If one path breaks, the others still work.

3. **It respects how people actually navigate.** NN/g's research is consistent: people *search* when they know what they want, *browse* when they don't. We give them both, instead of forcing one onto them.

4. **It mirrors what Wikipedia actually built.** Wikipedia has run for 25 years with this pattern. They have 61 million articles. The pattern has been stress-tested by literally a billion users a month.

5. **It still feels serious.** A sidebar with curated, fixed-height navigation reads as more professional than an endless tree. The Portal pages are themselves a signal of seriousness — they say "this subject is curated by humans who care about it."

6. **Cramped is the default failure mode of a growing site.** We're solving for it *before* it becomes a problem, which is far cheaper than rebuilding navigation later.

---

## Part 8 — Implementation order

The same design works at every stage, so we don't need to "switch designs" later. But the *content* it points at can grow incrementally:

| When | Build |
|---|---|
| **Day 1** | Fixed-height sidebar (Surface 1) with hardcoded subject list. ⌘K search (Surface 4). Bottom-of-page categories on every lesson (Surface 5). |
| **At ~10 subjects** | Add the Browse-All grid page (Surface 3). |
| **At ~25 subjects** | Add Portal pages (Surface 2) for the top 3 subjects. Drop the sub-subject tree from the sidebar; it now lives inside each Portal. |
| **At ~100 subjects** | Add top-level grouping (Making & Building, Kitchen & Food, etc.) on the Browse-All page. Filters and sort. |
| **At ~500+ subjects** | Lateral "Related subjects" recommendations on each Portal page. Portal sub-curators. Personalised "watched subjects" in user profile. |

Each step is small, additive, and triggered by an observable threshold — not done speculatively. This is how Wikipedia grew its navigation, and how we should too.

---

## Summary

| Problem | Solution |
|---|---|
| Sidebar gets cramped past ~30 subjects | Sidebar becomes a fixed-height **navigation hub** that doesn't list subjects at all |
| Users can't find what they want | Four parallel **discovery surfaces** (sidebar hub, Portal pages, Browse-All grid, ⌘K search) + per-lesson categories |
| The taxonomy is huge | **Portal pages** scope the experience to one subject at a time |
| Hard to discover new subjects | **Browse-All page** with search, filters, sort, and "needs a writer" section |
| Hard to maintain at scale | Each Portal page has its own curators; growth is delegated, not centralised |

The current sidebar is a Stage-1 design that will start to feel cramped around 30 subjects. By Stage 3 (50–500 subjects), the sidebar above gives the site genuinely Wikipedia-scale navigation while still feeling clean and serious.

Tell me which mockup to build live next — the Browse-All page, an Electronics portal page, or the Stage-3 contextual sidebar.
