# 10 — B.L.U.E. Fundamental Architecture: A Concrete Proposal

> **Sebastian's third unsolved problem, from his forum post:**
> *"If no one is able to agree on how the site should be structured, then it will be impossible to move forward with a design or strategy. It's one thing to talk about possibilities, it's another thing to actually pick a specific path & stick with it. There must be agreement (at least among collaborators) on how to move forward with the system structure before code changes & pull requests can start being implemented."*

This document is one such *specific path*. It is researched, opinionated, and concrete enough to start building from. It also includes ASCII mockups of every key screen so the architecture isn't just words — you can see what it actually looks like.

---

## Part 1 — What Sebastian himself said the architecture must do

Re-reading the video transcript, Sebastian made eight design commitments that any architecture must respect. These are the non-negotiables.

1. **Hierarchy of knowledge.** Every lesson sits on a numbered level. A Level *N* lesson must be completable using only Level 1…N−1 content.
2. **One canonical guide per topic.** No duplicates. Updates happen by edit or replacement.
3. **Methods system.** Inside a guide, multiple methods are allowed; one is the "main" method; the rest are alternatives. Methods can be promoted if their usage exceeds the main.
4. **Verifier juries.** New submissions are reviewed by an *odd number* of randomly-selected verifiers. Majority vote publishes.
5. **Mandatory justifications.** Every verifier vote must include a written reason. Silent voting risks losing verifier status.
6. **Dispute mechanism.** Multiple dispute types (accuracy, hierarchy placement, verifier conduct, cross-niche conflict, taxonomy). Some disputes can result in a *spin-off* — the guide forks into niche-specific variants.
7. **Monetization, if any, is topical only.** No banner ads. No paywalls. Only "materials & tools for this build" links beside the relevant lesson.
8. **No single point of failure.** The system must work without him.

These eight rules are the constitution. Everything below is one way to make them real.

---

## Part 2 — Research: how comparable projects actually work

Before designing anything, I checked what's already proven at scale.

### Wikipedia / MediaWiki

The piece of B.L.U.E. that looks most like an existing system is the *content layer*. Wikipedia / MediaWiki has been solving "how do you let anyone write, organise, and revise structured knowledge" for 25 years.

Key things they got right that we should copy:

- **Page table + Revision table + Text table** (a 3-table content model). The page row holds metadata; the revision row holds *who edited, when, why*; the text row holds the actual content blob. **No revision is ever deleted.** Every edit creates a new revision row. ([MediaWiki architecture docs](https://www.mediawiki.org/wiki/Manual:MediaWiki_architecture))
- **Revisions stored as gzipped diff chains** against the previous revision. Compression ratio approaches 98%. The full text of every old version is preserved without exploding storage.
- **Talk pages.** Every article has a parallel "Talk:Article-name" page where editors discuss changes before making them. This separates content from process.
- **Categories.** A many-to-many tag system (`page ↔ category`) on top of the article namespace. Articles can belong to many categories.
- **Namespaces.** Articles, Talk, User, File, Help, etc. are all stored in the same table with a `namespace` integer column. A clean way to keep different *kinds* of content in one system.
- **Append-only philosophy.** The database never forgets anything. This is what makes Wikipedia trustworthy — every claim is auditable.

### Python PEP / Rust RFC / Linkerd RFC

The piece that looks most like *governance* — the part that decides hierarchy disputes, taxonomy, jury rules — has been solved by language communities for decades.

- **Python**: PEPs (Python Enhancement Proposals). A numbered, public, immutable proposal format with explicit status states: `Draft → Open → Accepted | Rejected | Withdrawn | Superseded`. Even "rejected" PEPs are kept forever as a record of what was considered. ([PEP 1](https://peps.python.org/pep-0001/))
- **Rust**: RFCs. Similar idea, hosted in a GitHub repo, with a **"Final Comment Period"** mechanism — a 10-day window after consensus where reactions are still allowed before merge. Prevents rushed decisions. ([Rust RFC process](https://github.com/rust-lang/rfcs))
- **Linkerd / Kubernetes**: separate the proposal into **three phases**: *Problem statement → Design proposal → Implementation*. Each phase has its own review. Stops architecture debates from happening inside pull requests.

The consistent pattern: **architecture decisions are made in writing, in public, before code is written. The decision is preserved forever. Even the alternatives that were rejected are preserved.**

### Stack Overflow / Reddit

The piece that looks most like *reputation, verifiers, and dispute resolution* has been solved by Q&A sites.

- **Reputation as a gatekeeper.** Different actions unlock at different reputation thresholds (e.g., 50 reputation to comment, 2,000 to suggest edits, 10,000 to access moderation tools). This gives newcomers a path to authority without requiring an explicit application.
- **Tag-experts.** Reputation is tracked *per tag*, not globally. A 50,000-rep Python expert is not automatically trusted to moderate a Cooking question.
- **Flagging and review queues.** When content is flagged, it enters a review queue. Trusted users (above a reputation threshold) work through the queue. The queue itself is part of the architecture.

### Git

The piece that looks most like the *revision and edit-conflict story* has been solved by Git.

- **Append-only commits.** Every change is a commit. No commit is ever destroyed.
- **Three-way merges.** When two people edit the same file in different ways, the system tries to merge automatically and only asks a human to intervene when the same line was changed in both.
- **Branches as cheap forks.** Sebastian's "spin-off" rule (when two niches can't agree, fork the guide) is exactly what Git branches solve.

---

## Part 3 — The architecture, in one diagram

Combining Sebastian's eight rules with the proven patterns above gives this five-layer architecture:

```
╔═══════════════════════════════════════════════════════════════════════╗
║                          THE EIGHT NON-NEGOTIABLES                    ║
║   (hierarchy, canonical guide, methods, juries, justifications,       ║
║    disputes/spin-offs, topical-ads-only, no single point of failure)  ║
╚════════════════════════════════════╤══════════════════════════════════╝
                                     │
        ┌────────────────────────────┴────────────────────────────┐
        │                                                          │
   ┌────▼─────────────┐  ┌─────────────────┐  ┌──────────────────▼┐
   │  L5 · PRESENT    │  │ L4 · GOVERN     │  │  L4 · GOVERN      │
   │   (the website)  │  │  (verifier      │  │   (RFC / policy   │
   │                  │  │   juries +      │  │    decisions)     │
   │  4-level reader  │  │   disputes)     │  │                   │
   │  view, sticky    │  │                 │  │  numbered, public,│
   │  TOC, infobox,   │  │  per-(subject,  │  │  immutable        │
   │  jury banner     │  │  level) roles   │  │                   │
   └──────────────────┘  └─────────────────┘  └───────────────────┘
                                ▲                       ▲
                                │                       │
                ┌───────────────┴───────────────────────┴────┐
                │      L3 · WORKFLOW (state machines)        │
                │                                            │
                │   Submission FSM:                          │
                │   DRAFT → SUBMITTED → IN_REVIEW            │
                │            → PUBLISHED | RETURNED          │
                │                                            │
                │   Dispute FSM:                             │
                │   OPEN → UNDER_REVIEW → RESOLVED/DISMISSED │
                │                                            │
                │   RFC FSM:                                 │
                │   DRAFT → PROPOSED → FCP → ACCEPTED/REJ.   │
                └──────────────────┬─────────────────────────┘
                                   ▲
                                   │
            ┌──────────────────────┴──────────────────────┐
            │   L2 · DOMAIN MODEL (the entities)          │
            │                                             │
            │   Subject ─┐                                │
            │            ├── Guide ──── Revision ── Text  │
            │            │     │                          │
            │            │     ├── Method (1..N)          │
            │            │     ├── VendorLink (0..N)      │
            │            │     ├── Talk (parallel page)   │
            │            │     └── Category (M..N)        │
            │            │                                │
            │   User ── VerifierRole(Subject, MaxLevel)   │
            │     └── Vote ── Review ── Guide             │
            │     └── Reputation (per Subject, per Level) │
            └──────────────────┬──────────────────────────┘
                               ▲
                               │
        ┌──────────────────────┴────────────────────────────┐
        │   L1 · STORAGE  (Postgres, append-only)            │
        │                                                    │
        │   Every write creates a new row. Nothing is        │
        │   ever updated in place except status fields.      │
        │   Old revisions stored as gzipped diff chains.     │
        │   Daily snapshots to S3-compatible cold storage.   │
        └────────────────────────────────────────────────────┘
```

The rest of this document drills into each layer.

---

## Part 4 — The data model (L1 + L2)

### Tables that exist on day one

```
USERS
├─ id                  text  (PK)
├─ handle              text  (unique)
├─ email               text
├─ joined_at           timestamp
├─ reputation_total    int   (denormalised cache)
└─ is_steward          bool

REPUTATION                              -- per subject + per level
├─ user_id             text  (FK)
├─ subject_id          text  (FK)
├─ level               int
├─ points              int

SUBJECTS
├─ id                  text  (PK)
├─ slug                text
├─ parent_id           text  (FK, nullable)
├─ name                text
├─ created_at          timestamp
└─ approved_by_rfc     text  (FK to RFC that approved this subject)

GUIDES                                   -- the "page" row
├─ id                  text  (PK)
├─ subject_id          text  (FK)
├─ slug                text
├─ level               int
├─ status              enum: DRAFT | SUBMITTED | IN_REVIEW | PUBLISHED | RETURNED | DEPRECATED
├─ current_revision_id text  (FK to REVISIONS)
├─ created_at          timestamp
├─ created_by          text  (FK to USERS)
└─ UNIQUE (subject_id, slug)             -- enforces "one canonical guide per topic"

REVISIONS                                -- every edit is a new row, never deleted
├─ id                  text  (PK)
├─ guide_id            text  (FK)
├─ text_id             text  (FK to TEXTS)
├─ parent_revision_id  text  (FK, nullable)
├─ edited_by           text  (FK to USERS)
├─ edited_at           timestamp
├─ edit_summary        text                -- "fixed typo in step 4"
├─ is_minor            bool

TEXTS                                    -- the actual content blobs
├─ id                  text  (PK)
├─ payload             bytea               -- gzip(diff vs parent) or gzip(full)
├─ is_full_snapshot    bool
└─ created_at          timestamp

PREREQUISITES                            -- enforces the hierarchy rule
├─ guide_id            text  (FK)
├─ requires_guide_id   text  (FK)
└─ CHECK: requires_guide.level < guide.level
└─ CHECK: requires_guide.status = 'PUBLISHED'

METHODS                                  -- multiple ways per guide
├─ id                  text  (PK)
├─ guide_id            text  (FK)
├─ name                text
├─ body_revision_id    text  (FK to REVISIONS, scoped to method)
├─ is_main             bool                -- exactly one TRUE per guide
├─ usage_count         int                 -- ticked up when readers mark "I used this"

VENDOR_LINKS                             -- topical "materials & tools"
├─ id                  text  (PK)
├─ guide_id            text  (FK)
├─ vendor_name         text
├─ url                 text
├─ avg_rating          float
└─ disclosed_affiliate bool

VERIFIER_ROLES                           -- per (user, subject, max_level)
├─ user_id             text  (FK)
├─ subject_id          text  (FK)
├─ max_level           int
├─ granted_at          timestamp
└─ revoked_at          timestamp (nullable)

REVIEWS                                  -- a jury for a single submission
├─ id                  text  (PK)
├─ guide_id            text  (FK)
├─ jury_size           int   (odd)
├─ deadline            timestamp
├─ status              enum: OPEN | CLOSED_APPROVED | CLOSED_REJECTED | EXPIRED
└─ outcome_recorded_at timestamp

REVIEW_VOTERS                            -- who is on the jury
├─ review_id           text  (FK)
├─ user_id             text  (FK)

VOTES
├─ id                  text  (PK)
├─ review_id           text  (FK)
├─ user_id             text  (FK)
├─ decision            enum: APPROVE | REJECT | ABSTAIN
├─ justification       text                -- min 50 chars enforced in code
├─ cast_at             timestamp

DISPUTES
├─ id                  text  (PK)
├─ opener_id           text  (FK to USERS)
├─ guide_id            text  (FK, nullable)
├─ type                enum: ACCURACY | HIERARCHY_PLACEMENT | VERIFIER_CONDUCT
│                            | CROSS_NICHE_CONFLICT | TAXONOMY
├─ body                text
├─ status              enum: OPEN | UNDER_REVIEW | RESOLVED | DISMISSED
├─ resolution          text  (nullable)
├─ opened_at           timestamp
└─ resolved_at         timestamp (nullable)

RFCS                                     -- governance proposals
├─ id                  text  (PK)
├─ number              int   (unique, auto-incrementing)
├─ title               text
├─ author              text  (FK to USERS)
├─ status              enum: DRAFT | PROPOSED | FCP | ACCEPTED | REJECTED | SUPERSEDED
├─ summary             text
├─ body                text
├─ created_at          timestamp
├─ entered_fcp_at      timestamp (nullable)
├─ decided_at          timestamp (nullable)
└─ supersedes_rfc_id   text  (FK, nullable)

RFC_VOTES
├─ rfc_id              text  (FK)
├─ user_id             text  (FK)
├─ vote                enum: FOR | AGAINST | ABSTAIN
├─ reasoning           text
└─ cast_at             timestamp

TALK_PAGES                               -- parallel discussion (Wikipedia-style)
├─ id                  text  (PK)
├─ guide_id            text  (FK)
└─ created_at          timestamp

TALK_POSTS
├─ id                  text  (PK)
├─ talk_page_id        text  (FK)
├─ author              text  (FK to USERS)
├─ parent_post_id      text  (FK, nullable)            -- threaded
├─ body                text
└─ posted_at           timestamp

CATEGORIES
├─ id                  text  (PK)
├─ slug                text
└─ name                text

GUIDE_CATEGORIES                         -- many-to-many
├─ guide_id            text  (FK)
├─ category_id         text  (FK)
```

### Why this shape

- **Append-only.** Every edit, vote, dispute message and RFC reasoning is recorded forever. This is what makes the platform auditable — the same property that makes Wikipedia trustworthy.
- **`REVISIONS` separate from `TEXTS`.** Lifted directly from MediaWiki. Lets us store one revision's body and let the next revision be a small diff against it, achieving ~98% compression for typical edits.
- **`PREREQUISITES` table with CHECK constraints.** The hierarchy rule (Level N requires Level <N) is enforced *in the database*, not in application code. You cannot bypass it by accident.
- **`UNIQUE(subject_id, slug)` on GUIDES.** The "one canonical guide per topic" rule is enforced by the database. Two people can't accidentally publish duplicate lessons.
- **`VERIFIER_ROLES` per (user, subject, max_level).** Exactly the structure Sebastian described in the video. A user can be a verifier in Electronics up to L3 and in Woodworking up to L1 simultaneously.
- **Reputation per (subject, level).** Stack Overflow learned this the hard way — global reputation lets people gain authority in topics they don't know. We split from day one.
- **`METHODS` with `is_main` boolean + `usage_count`.** Implements the methods promotion rule: when a non-main method's usage exceeds the main's for a long enough window, an automatic RFC fires to promote it.

---

## Part 5 — The workflow layer (L3)

Three state machines do most of the work. They are simple, but writing them down explicitly avoids the "we sort of know what happens" problem.

### Submission FSM (the heart of the system)

```
                     submit
   ┌──────┐ ─────────────────────► ┌───────────┐
   │ DRAFT│                        │ SUBMITTED │
   └──┬───┘                        └─────┬─────┘
      │ author edits                     │ system selects jury
      │                                  ▼
      │                            ┌────────────┐
      │                            │ IN_REVIEW  │
      │                            └──┬─────┬───┘
      │                               │     │
      │  return-to-author             │     │  majority APPROVE
      │  (majority REJECT             │     │  before deadline
      │   OR deadline missed)         │     │
      │                               │     ▼
      │ ◄─────────────────────┐       │  ┌───────────┐
      │                       │       │  │ PUBLISHED │
      │                       │       │  └─────┬─────┘
      │                       │       │        │
      │                       │       ▼        │ author edit accepted
      │                       └─── ┌──────────┐│
      │                            │ RETURNED │◄┘
      │                            └──────────┘
      │
      └── author abandons → DRAFT stays as DRAFT forever (never deleted)
```

### Verifier jury selection (the part that makes #4–5 of Sebastian's rules real)

```python
def select_jury(guide, n=5):
    """
    Sebastian's rule: 'an odd number of verifiers are selected at random,
    almost like a jury'.
    Returns exactly n users.
    """
    # 1. Eligible = users with VerifierRole covering this (subject, level).
    eligible = users_with_role(guide.subject_id, min_level=guide.level)
    
    # 2. Exclude the author and any co-authors.
    eligible -= {guide.author, *guide.co_authors}
    
    # 3. Exclude anyone who voted on this guide's previous review.
    eligible -= prior_jury(guide)
    
    # 4. If fewer than n eligible, escalate the level upward
    #    (a Level 3 expert can vote on a Level 2 guide).
    while len(eligible) < n:
        eligible |= users_with_role(guide.subject_id, min_level=level + 1)
        level += 1
        if level > MAX_LEVEL:
            raise NotEnoughVerifiers
    
    # 5. Weighted random selection: weight = reputation_in_subject_at_level + 1
    #    (the +1 means even brand-new verifiers can be picked.)
    return weighted_random(eligible, n)
```

### Vote auto-close rules

```
A review auto-closes when ANY of these is true:
  1. All N jury members have voted.
  2. Deadline reached, and at least ⌈N/2⌉ votes are cast.
  3. ⌈N/2⌉ APPROVEs already cast — review wins early.
  4. ⌈N/2⌉ REJECTs already cast — review fails early.

When auto-closed:
  - APPROVE wins → guide.status := PUBLISHED, published_at := now
  - REJECT wins  → guide.status := RETURNED, deadline_for_resubmit := 30 days
  - DEADLINE EXPIRED with too few votes → reselect jury, double its size
```

### Dispute FSM

```
                                  open
   anyone with                ┌──────────────┐
   ≥ 10 reputation ─────────► │     OPEN     │
                              └──────┬───────┘
                                     │ auditor picks it up
                                     ▼
                              ┌──────────────┐
                              │ UNDER_REVIEW │
                              └──┬─────────┬─┘
                  resolution     │         │     dismissed
                  written        │         │     (no merit)
                                 ▼         ▼
                          ┌──────────┐  ┌──────────┐
                          │ RESOLVED │  │DISMISSED │
                          └─────┬────┘  └──────────┘
                                │
                                │ if type = CROSS_NICHE_CONFLICT
                                ▼
                          ┌───────────────────────┐
                          │  SPIN-OFF triggered:  │
                          │  guide is forked into │
                          │  two niche-specific   │
                          │  variants. Each gets  │
                          │  its own canonical    │
                          │  slug & verifier pool.│
                          └───────────────────────┘
```

The **spin-off branch** is Sebastian's own idea from the video. It is the escape hatch that prevents one niche's needs from blocking another. Mechanically it's a `git branch`-style operation on the GUIDES table.

### RFC FSM

```
                  ┌───────┐ submit ┌──────────┐ 7 days of  ┌─────┐
                  │ DRAFT ├───────►│ PROPOSED ├───────────►│ FCP │
                  └───┬───┘        └─────┬────┘ discussion └──┬──┘
   author edits      │                  │                    │ 10-day final
                     │                  │ withdrawn          │ comment period
                     │                  ▼                    │
                     │           ┌────────────┐              ▼
                     └─────────► │ WITHDRAWN  │       ┌──────────┐
                                 └────────────┘       │ ACCEPTED │
                                                      │    or    │
                                                      │ REJECTED │
                                                      └──────────┘
```

The **FCP (Final Comment Period)** is borrowed directly from Rust's RFC process. It's a 10-day window after consensus appears reached, during which strong objections can still be raised. It prevents a vote that ran while half the community was asleep from sailing through.

---

## Part 6 — Reputation and how authority is earned

The single most important architectural decision is *how authority is distributed.* Get this wrong and you either get an oligarchy (a small group decides everything) or chaos (no one decides anything).

### Reputation rules

| Action | Reputation change |
|---|---|
| Submit a lesson | +1 immediately |
| Lesson is published | +10 to author, +3 to each co-author |
| Cast a verifier vote with justification ≥ 50 chars | +2 |
| Vote on a guide that subsequently passes (you voted yes) | +5 bonus |
| Receive a "thanks" from a reader | +1 |
| Open an RFC | +0 (free) |
| RFC is accepted | +20 to author |
| Open a dispute that is later resolved as valid | +5 |
| Open a dispute that is dismissed as spam | −10 |
| Have a published guide reach 100 / 1000 / 10000 helpful votes | +25 / +100 / +500 |
| Lose a vote because of malicious justification | −20 |

All reputation is **scoped to (subject, level)**, never global. This means a 5000-rep Electronics expert is treated as a newcomer in Cooking.

### Capability gates

```
Reputation  │ What this unlocks (in that subject)
   ─────────┼──────────────────────────────────────────────────
       0    │ Read everything. Vote on lesson helpfulness.
      10    │ Submit a lesson. Comment on Talk pages. Open a dispute.
      50    │ Edit suggestions to existing lessons (held in queue).
     200    │ Pass the verifier exam for L1. Vote on L1 reviews.
     500    │ Pass the verifier exam for L2. Vote on L2 reviews.
    1000    │ Pass for L3. Mentor newcomers (+rep when they pass).
    2000    │ Pass for L4. Eligible to be elected an Auditor.
    5000    │ Open subject-scope RFCs (taxonomy changes etc.).
    
Reputation across ALL subjects, combined:
   ─────────┼──────────────────────────────────────────────────
   10000+   │ Eligible to be elected a Steward.
```

This is borrowed from Stack Overflow's privilege ladder, which has run for 16 years on essentially this idea. The numbers are tuneable via RFC.

---

## Part 7 — Mockups: what the user actually sees

### Mockup A — The Main Page

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  *B.L.U.E.*                                              [Search.....]  ⌘K   │
│  the universal skills encyclopedia                       your page · log in  │
├──────────────────────────────────────────────────────────────────────────────┤
│  Article | Talk | Read | Edit | History                                      │
├──────────┬───────────────────────────────────────────────────────────────────┤
│ NAV      │  ┌───────────────────────────────────────────────────────────┐    │
│ ─────    │  │ 📘 In plain words                                         │    │
│ Main     │  │ A free website where anyone can read or write step-by-   │    │
│ All      │  │ step lessons on how to do real things. Every lesson is   │    │
│ Random   │  │ checked by people who know the topic before it goes up.  │    │
│          │  └───────────────────────────────────────────────────────────┘    │
│ SUBJECTS │                                                                   │
│ ─────    │     Lessons: 11   Published: 8   Subjects: 10   Verifiers: 6     │
│ Electronics
│ ·Digital │  ┌───────────────────┐   ┌──────────────────────────────────┐   │
│ ··Logic  │  │ Featured lesson   │   │ From the editors                  │   │
│ ··CPU    │  │                   │   │                                   │   │
│ ·Analog  │  │ How to make a     │   │ B.L.U.E. is in its pilot phase.   │   │
│ Wood     │  │ resistor          │   │ The first subject being built out │   │
│ Bikes    │  │                   │   │ is Electronics. Volunteers welcome│   │
│ Brewing  │  │ A passive part... │   │                                   │   │
│          │  │  ▷ read more →    │   │  Write · Wish · RFC               │   │
│ CONTRIB  │  └───────────────────┘   └──────────────────────────────────┘   │
│ Write    │                                                                   │
│ Wish     │  ┌───────────────────┐   ┌──────────────────────────────────┐   │
│ Verify   │  │ Currently in      │   │ Recently published                │   │
│ Disputes │  │ review            │   │  · How to make a resistor (L1–L4)│   │
│          │  │  · Build a 4-bit  │   │  · Build a NAND gate from BJTs   │   │
│ TOOLS    │  │    ALU (L3)       │   │  · Build a transistor (BJT)      │   │
│ Search   │  │    5 days left    │   │                                   │   │
│ Recent   │  └───────────────────┘   └──────────────────────────────────┘   │
│ Cite     │                                                                   │
│          │  ▶ Did you know?                                                  │
│ LANGUAGES│  …that resistors are the most-manufactured electronic component  │
│ ES DE JA │  in history? Over 20 trillion are made every year.                │
│          │  ┌────────────────────────────────────────────────────────────┐  │
│          │  │ 📘 In plain words: That's so many that every person on    │  │
│          │  │ Earth gets 2,500 of them per year.                         │  │
│          │  └────────────────────────────────────────────────────────────┘  │
├──────────┴───────────────────────────────────────────────────────────────────┤
│  CC BY-SA 4.0 · From an idea by Sebastian Rey · About                        │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Mockup B — A lesson page (the multi-level reader view)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  Article | Talk | Read | Edit | History                                      │
├──────────┬───────────────────────────────────────────────────────────────────┤
│ NAV      │  Electronics › Components › Resistors                            │
│ ─────    │  ──────────────────────────────────────────────────────────────  │
│ Main     │  How to make a resistor                                          │
│ All      │  ─────────────────────────────────────────────────────────────   │
│ Random   │  Lesson in Electronics · 6 min read · 14 days ago                │
│          │                                                                  │
│ SUBJECTS │  ┌────────────────────────────┐    ┌──────────────────────────┐ │
│ Wood     │  │ 📘 In plain words          │    │ Make a resistor          │ │
│ Bikes    │  │ A resistor is a small      │    ├──────────────────────────┤ │
│ Brewing  │  │ part that slows down       │    │ Subject  Electronics     │ │
│          │  │ electricity. Like pinching │    │ Difficulty  L1–L4        │ │
│ TOOLS    │  │ a hose to slow water.      │    │ Time     10 min → 1 day  │ │
│ Search   │  └────────────────────────────┘    │ Authors  @kira @otto     │ │
│ Recent   │                                    │ Reviewed 5 verifiers      │ │
│          │  ┌────────────────────────────┐    │ Votes    ▲1247 ▼14       │ │
│          │  │ ⚡ In short                │    │ Methods  4               │ │
│          │  │ • A passive 2-pin device   │    │ License  CC BY-SA 4.0    │ │
│          │  │ • Measured in ohms (Ω)     │    └──────────────────────────┘ │
│          │  │ • V = I × R                │                                  │
│          │  │ • 4 methods, 4 levels      │    Contents                      │
│          │  └────────────────────────────┘    1. How it works (4 levels)    │
│          │                                    2. Ways to make one           │
│          │  HOW IT WORKS — at four levels     2.1 L1 — Pencil trick         │
│          │  ─────────────────────────────     2.2 L2 — Carbon comp.         │
│          │  ┌──────────┬───────────────────┐  2.3 L3 — Wire-wound           │
│          │  │ Level 1  │ Like a pinched    │  2.4 L4 — Thin-film            │
│          │  │ Anyone   │ hose…             │  3. Reading colour bands       │
│          │  ├──────────┼───────────────────┤  4. References                 │
│          │  │ Level 2  │ Ohm's Law V=IR…   │                                │
│          │  │ School   │                   │                                │
│          │  ├──────────┼───────────────────┤                                │
│          │  │ Level 3  │ R = ρL/A …        │                                │
│          │  │ Hobbyist │                   │                                │
│          │  ├──────────┼───────────────────┤                                │
│          │  │ Level 4  │ Johnson noise…    │                                │
│          │  │ Engineer │                   │                                │
│          │  └──────────┴───────────────────┘                                │
│          │                                                                  │
└──────────┴──────────────────────────────────────────────────────────────────┘
```

### Mockup C — A lesson currently in review (the jury banner)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  Article | Talk | Read | Edit | History                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│  Electronics › Digital › CPU design                                          │
│  Build a 4-bit ALU from NAND gates                                           │
│  ─────────────────────────────────────────────────────────────────────────   │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │ ⚐  This lesson is being checked.                                    │   │
│  │     2 of 5 verifiers have voted. Deadline in 5 days.                │   │
│  │     ─────────────────────────────────────────────                   │   │
│  │     @ravi    APPROVE   "Level 3 placement appropriate. Section on   │   │
│  │                         full-adder derivation is the clearest…"     │   │
│  │     @sven    REJECT    "Carry-lookahead method skips derivation of  │   │
│  │                         generate/propagate logic. Needs a worked…"  │   │
│  │     @kira    pending                                                │   │
│  │     @mei     pending                                                │   │
│  │     @otto    pending                                                │   │
│  │                                                                     │   │
│  │     [You qualify for this jury — cast your vote →]                  │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  Prerequisites: this lesson assumes familiarity with                         │
│    📚 Build a NAND gate from BJTs (Level 2)                                  │
│                                                                              │
│  [… rest of the lesson body …]                                               │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Mockup D — RFC index page (governance)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  *B.L.U.E.*                              Architecture · Requests for Comment │
├──────────────────────────────────────────────────────────────────────────────┤
│  Every decision about how this site works happens here, in writing, in       │
│  public, before any code is written. Even rejected proposals are kept.       │
│                                                                              │
│  [ + Open a new RFC ]      Filter: [All] [Proposed] [FCP] [Accepted] [Rej]   │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ RFC-006  Constitution: mission, anti-ads rule, license-lock         │   │
│  │          status: PROPOSED                          by @kira · 2d ago│   │
│  │          7 days of comments left before FCP                         │   │
│  │          ──────────────────────────────────────────────────────     │   │
│  │          Lock the mission ("free, no ads, CC BY-SA") at a level    │   │
│  │          that requires 60-day window + ⅔ supermajority to change.  │   │
│  │                                                                     │   │
│  │          For: 14   Against: 2                                       │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ RFC-007  Method promotion: when should a sub-method become canonical│   │
│  │          status: FCP                       by @ravi · entered FCP 3d│   │
│  │          ──────────────────────────────────────────────────────     │   │
│  │          If a non-main method's usage exceeds the main's by 20% for │   │
│  │          90 consecutive days, auto-open a promotion review.         │   │
│  │                                                                     │   │
│  │          For: 18   Against: 1   FCP closes in 7 days                │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ RFC-005  Verifier jury size scales with level (5 → 7 → 9)           │   │
│  │          status: ACCEPTED · merged 5d ago        by @mei            │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ RFC-004  Use Postgres + Next.js for the MVP                         │   │
│  │          status: ACCEPTED · merged 12d ago       by @ravi           │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Mockup E — Talk page (Wikipedia-style discussion)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  Article | TALK | Read | Edit | History                                      │
├──────────────────────────────────────────────────────────────────────────────┤
│  Talk: How to make a resistor                                                │
│  ─────────────────────────────────────────────────────────────────────────   │
│                                                                              │
│  [+ start a new topic]                                                       │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ # Should we add a fifth method for SMD resistors?                   │   │
│  │ @otto · 6d ago                                                      │   │
│  │ ─────────────────────────────────────────────────────────────       │   │
│  │ Modern electronics is almost entirely surface-mount now. The four   │   │
│  │ methods listed are great but assume through-hole. Worth adding L4   │   │
│  │ SMD-fab as a fifth?                                                 │   │
│  │                                                                     │   │
│  │     ↳ @kira · 5d ago                                                │   │
│  │       I'd vote yes, but it might be better as its own lesson        │   │
│  │       ("How to make an SMD resistor") since the process is so       │   │
│  │       different that calling it a "method" of this lesson is a      │   │
│  │       stretch. Thoughts?                                            │   │
│  │                                                                     │   │
│  │         ↳ @otto · 5d ago                                            │   │
│  │           Fair point. I'll open it as a separate lesson and link    │   │
│  │           between them.                                             │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│  │ # Step 3 of Level 2 has the wrong oven temperature                  │   │
│  │ @mei · 11d ago · resolved                                           │   │
│  │ ─────────────────────────────────────────────────────────────       │   │
│  │ 150 °C is too low for phenolic resin — it should be 175 °C.         │   │
│  │ Edit submitted.                                                     │   │
│  │     ↳ @kira · 11d ago                                               │   │
│  │       Approved and merged in r12.                                   │   │
│  └──────────────────────────────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────────────────┘
```

### Mockup F — Writing flow (5-step wizard)

```
┌──────────────────────────────────────────────────────────────────────────────┐
│  Write a lesson                                                              │
│  ─────────────────────────────────────────────────────────────────────────   │
│                                                                              │
│  Step 1 · Subject & level    [●━━━━○━━━━○━━━━○━━━━○]                        │
│                                                                              │
│  What subject is this lesson about?                                          │
│  [ Electronics > Components > Resistors                  ▾ ]                 │
│                                                                              │
│  How hard is it? (this affects which lessons readers must know first)        │
│  ( ● L1 Easy )  ( ○ L2 Medium )  ( ○ L3 Harder )  ( ○ L4 Expert )            │
│                                                                              │
│  ✓ L1 lessons don't need any other lessons first.                            │
│                                                                              │
│  Heads-up: there's a wish for this topic.                                    │
│  ┌────────────────────────────────────────────────────────────────────┐    │
│  │ 🌟  "How to solder a through-hole component" — 180/150 points      │    │
│  │     pledged. If your lesson fulfils this, you get a +36 bonus.     │    │
│  └────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
│                                              [ Cancel ]      [ Next → ]      │
└──────────────────────────────────────────────────────────────────────────────┘
```

---

## Part 8 — How the site holds up under stress

These are the failure modes I asked "what happens if X" for. The architecture above handles each one without changes.

| Failure | What happens |
|---|---|
| A jury member ghosts | Deadline expires → reselect jury, double its size. Auto-handled. |
| Spam waves of new submissions | Submission rate-limited per user. Reputation < 10 = 1 submission/week. |
| A bad-faith verifier passes garbage | 3 silent/unjustified votes → status revoked. Auditor review of pattern. |
| An accepted lesson is later found to be wrong | Anyone with ≥10 rep opens an ACCURACY dispute. UNDER_REVIEW → fresh jury. |
| Two niches argue about the same lesson | CROSS_NICHE_CONFLICT dispute → spin-off forks the lesson. |
| Sebastian or any Steward disappears | Stewards are 3–5 people; quorum is 2; replacement by RFC vote. |
| Cloud provider goes down | Daily Postgres snapshots to S3-compatible cold storage. ~$2/month for years of history. |
| Lawsuit / takedown demand | Append-only history means we can "redact" content without losing the audit trail. |
| 4chan-style coordinated brigading | RFC-defined cooldown: if a guide receives >20 down-votes in <1hr from new accounts, votes are quarantined for steward review. |
| Someone tries to forge a "Sebastian approved this" claim | Founder role has no special powers in the data model. There is nothing to forge. |

---

## Part 9 — What ships first (the path to the first commit)

Six modules, in this order:

```
Week 1–2 │  Module 1: Identity            (users, sessions, reputation rows)
         │  Goal: people can sign up and have a profile page
         │
Week 3–4 │  Module 2: Content             (subjects, guides, revisions, texts)
         │  Goal: read-only browsing works. Seed data: the resistor lesson.
         │
Week 5–7 │  Module 3: Authoring           (draft → submit → review flow)
         │  Goal: anyone can write a lesson and submit it.
         │
Week 8–9 │  Module 4: Reviewing           (jury selection, votes, justifications)
         │  Goal: 5 randomly-picked verifiers can vote with reasons.
         │
Week 10  │  Module 5: Talk & disputes     (per-guide talk pages, dispute FSM)
         │  Goal: editorial debate lives somewhere structured.
         │
Week 11  │  Module 6: RFCs                (numbered, public, immutable)
         │  Goal: governance changes happen openly.
```

This intentionally leaves out:
- Vendor links (Phase 2, after a published lesson exists)
- Translations (Phase 2)
- Mobile app (Phase 3)
- Search beyond Postgres full-text (Phase 2)
- AI features (never — explicit non-goal)

Each module ends with a *demoable* feature so you can show the community visible progress.

---

## Part 10 — Why this is the right architecture for B.L.U.E.

Three reasons.

1. **It directly encodes Sebastian's eight rules.** Every non-negotiable from the video maps onto a database constraint, a state-machine transition, or an RFC requirement. The rules can't be quietly violated by code changes because they live in the schema.

2. **Every part is borrowed from a system that works at scale.** Wikipedia for content. Stack Overflow for reputation gating. Python/Rust for governance. Git for forks. We are not inventing anything we don't have to invent.

3. **It survives Sebastian leaving.** The Steward role has no founder, the database has no admin overrides, the RFC process can amend itself, and the bus-factor of every module is at least 2 by design. The system genuinely does not need him to function — which is the stated goal.

The next step is opening **RFC-001** with this document attached, putting it through a 7-day comment period plus 10-day FCP, and — if accepted — starting Module 1.
