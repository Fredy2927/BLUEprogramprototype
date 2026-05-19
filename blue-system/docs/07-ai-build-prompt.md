# 07 — AI Build Prompt for the B.L.U.E. UX

Copy-paste the prompt below into an AI builder (v0.dev, Lovable, Bolt.new, Cursor, Claude, etc.).
Three versions are included:

- **Prompt A** — short, for visual-first tools (v0 / Lovable / Bolt).
- **Prompt B** — long, for full-stack code generation (Cursor / Claude / ChatGPT).
- **Prompt C** — iteration prompts you send after the first generation.

---

## How to use these prompts well

1. **Start with Prompt A** if you want to see screens fast. Refine visuals first, then add logic.
2. **Use Prompt B** when you're ready to generate a real Next.js codebase.
3. **Never** paste both at once. AI builders do better with focused asks.
4. Always attach the files: `docs/00-vision.md`, `docs/02-spec.md`, `docs/03-data-model.md`, and `prototype/index.html` for visual reference.
5. After the first build, use **Prompt C** to iterate — one feature per round.

---

## PROMPT A — Visual-first (v0.dev / Lovable / Bolt.new)

> Build the UX for **B.L.U.E.** (Broad Learning Universal Education) — a free, community-built "how to do anything" website. Think Wikipedia, but every article teaches a *skill*, not a fact, and articles are organized into a strict learning hierarchy (Level 1 = basics → Level N = expert), where higher-level guides must list the lower-level guides you need to read first.
>
> **Tone & feel:** serious, library-of-Alexandria, low-distraction. Reading-first. Dark theme by default with a light toggle. No marketing fluff. No social-network vibes. Inspired by Wikipedia, MDN, Are.na, and Stripe Docs. Use a clean serif for guide body text and a clean sans-serif for UI.
>
> **Build these screens:**
>
> 1. **Home** — search bar front-and-center, featured subjects as cards, "guides currently in review" feed, "recently published" feed, tiny stats strip (# guides, # verifiers, # subjects).
> 2. **Subject browser** — left rail with the subject tree (collapsible, e.g. Electronics → Digital → Logic Gates). Main area lists guides in that subject as cards, grouped by Level (1, 2, 3…). Each card shows: level chip, title, status chip (Published / In Review / Returned), ▲ ▼ counts, author handle, last updated.
> 3. **Guide page** — the heart of the site:
>    - Breadcrumb (Subject › Sub-subject › Guide)
>    - Title with prominent **Level N** chip
>    - **Prerequisites box** at top: clickable links to the specific lower-level guides required. (If Level 1: "No prerequisites.")
>    - Body in Markdown.
>    - **Methods tabs** below the body: the "main" method is the default tab; other methods are alternate techniques contributed by the community. Each method has its own body and its own ▲ ▼.
>    - Right rail: status chip, ▲ ▼ counters, "Suggest an edit" button, "View revision history", "Open a dispute" link.
>    - At the bottom: a **"Materials & tools for this build"** section listing topical product links from vendors (no banner ads, no popups, ever). Each item shows a star rating; higher-rated items rank higher.
> 4. **Submit a guide** — multi-step form: (a) Subject + Level + Prerequisites picker (validates that every prerequisite is at a lower level), (b) Title + Body (Markdown editor with live preview), (c) Methods (at least one required; mark one as "main"), (d) License confirmation (CC BY-SA 4.0), (e) Review & submit.
> 5. **Verifier jury page** — for a logged-in verifier reviewing a submitted guide. Shows the guide on the left, a vote panel on the right with three buttons (Approve / Reject / Abstain) and a **mandatory written justification field** (min 50 chars; submit is disabled until filled). Shows other verifiers' votes (anonymized handles + their justifications) and a countdown timer.
> 6. **Become a verifier** — pick subject + max level, then take a subject-specific exam (placeholder UI for now).
> 7. **Dispute** — open / browse / view disputes. Types: HIERARCHY_PLACEMENT, CONTENT_ACCURACY, VERIFIER_CONDUCT, CROSS_NICHE_CONFLICT, SUBJECT_TAXONOMY. Each dispute shows discussion thread + resolution status.
> 8. **User profile** — public handle, reputation, verifier roles (e.g. "Electronics L3, Woodworking L1"), authored guides, recent votes (with justifications).
>
> **Reusable components:**
> - `<LevelChip level={1..N}>` — colored chip; intensity scales with level
> - `<StatusChip status="PUBLISHED|IN_REVIEW|RETURNED|DRAFT">`
> - `<GuideCard>` — title, level, status, votes, author, updated
> - `<PrereqList>` — clickable list of prerequisite guides
> - `<MethodTabs>` — tabbed method selector with vote counts
> - `<VerifierVotePanel>` — vote buttons + mandatory justification textarea
> - `<SubjectTree>` — recursive collapsible tree of subjects
> - `<VoteCounter>` — ▲ ▼ pair with optimistic UI
>
> **Non-negotiables (these are the design's soul):**
> - Free. No paywalls, no "premium" anywhere in the UI.
> - No display ads, popups, autoplay video, or interstitials. The only commercial element is the topical "Materials & tools" section at the bottom of relevant guides.
> - No social feed, no follow buttons, no notifications dopamine loop.
> - Every verifier vote must show a written justification — never just a thumbs up/down.
> - Hierarchy is sacred: the Prerequisites box on every Level 2+ guide is a primary UI element, not a footnote.
>
> Use seed data inspired by Sebastian Rey's video: **Electronics** as the pilot subject, with guides like "Build a BJT" (L1), "Build a NAND gate from BJTs" (L2, prereq: BJT), "Build a 4-bit ALU from NAND gates" (L3, prereq: NAND).

---

## PROMPT B — Full-stack code generation (Cursor / Claude / ChatGPT)

> You are building the MVP for **B.L.U.E.** (Broad Learning Universal Education), a free, community-edited website where guides teach you how to *do* things, organized into a strict learning hierarchy.
>
> **Stack:**
> - Next.js 15 (App Router) + TypeScript
> - Tailwind CSS + shadcn/ui
> - Postgres + Prisma
> - Auth: NextAuth (email magic link) — stub for now, real provider later
> - Markdown rendering: react-markdown + remark-gfm
> - Deployment-ready for Railway or Fly.io
>
> **Data model (Prisma):**
> Use exactly this schema as the starting point — do not invent extra tables:
>
> ```prisma
> // [PASTE THE CONTENT OF docs/03-data-model.md HERE]
> ```
>
> **Core rules to enforce in code (not just UI):**
>
> 1. **Hierarchy validation:** when creating a Guide with `level = N`, every prerequisite ID must reference a Guide whose `level < N` and whose `status = PUBLISHED`. Reject otherwise.
> 2. **One canonical guide per (subjectId, slug).** Enforce by unique constraint AND server-side check.
> 3. **Verifier eligibility:** a verifier can only vote on guides where they hold a `VerifierRole` with matching `subjectId` and `maxLevel >= guide.level`.
> 4. **Mandatory justification:** the Vote API rejects any vote whose `justification.length < 50`.
> 5. **Odd-sized juries:** when opening a Review, select an odd number of verifiers at random (default 5). If the eligible pool is even, drop the lowest-reputation eligible verifier to enforce odd.
> 6. **Review timer:** each Review has a `deadline`. When `now() > deadline`, the Review auto-closes; majority of cast votes decides, ties → REJECTED.
> 7. **Auto-return on user backlash:** after publication, if a guide's downvotes within 7 days exceed `max(20, 0.4 * total_votes)`, set status back to RETURNED and notify the author.
> 8. **Disputes:** users can open at most 5 OPEN disputes at a time. Auditors (role flag) resolve them. CROSS_NICHE_CONFLICT disputes use a "spin-off" action that duplicates the guide so each niche maintains its own variant.
> 9. **Content license:** every guide submission requires `licenseAccepted = true` for CC BY-SA 4.0; store the timestamp.
> 10. **No display ads, ever, in code or in config.** The only monetization surface is a `VendorLink` model (which you should add) attached to a Guide, rendered as a ranked list at the bottom of guide pages.
>
> **API routes to scaffold (all under `/api`):**
> - `POST /guides` (create draft)
> - `POST /guides/:id/submit` (move DRAFT → SUBMITTED, opens Review, selects jury)
> - `POST /reviews/:id/votes` (vote with justification)
> - `GET /guides/:id` (public)
> - `GET /subjects` (tree)
> - `POST /disputes`
> - `POST /verifier-applications`
>
> **Pages to build (App Router):**
> - `/` — home
> - `/s/[subjectSlug]` — subject browser
> - `/g/[subjectSlug]/[guideSlug]` — guide page
> - `/write` — submit a guide
> - `/review/[reviewId]` — verifier jury page
> - `/become-a-verifier`
> - `/disputes`, `/disputes/[id]`
> - `/u/[handle]` — user profile
>
> **Design system:**
> - Dark theme default, light toggle. Tailwind + CSS variables for theme.
> - Serif for guide body (`font-serif`, e.g. Source Serif Pro). Sans for UI (Inter).
> - Reusable shadcn-style components: `LevelChip`, `StatusChip`, `GuideCard`, `PrereqList`, `MethodTabs`, `VerifierVotePanel`, `SubjectTree`, `VoteCounter`.
> - Use the existing visual reference at `prototype/index.html` as the look-and-feel anchor.
>
> **Seed data:**
> Seed Postgres with:
> - 1 Subject `Electronics` with children `Digital`, `Analog`, `Power supplies`. `Digital` has children `Logic gates`, `CPU design`.
> - 3 Guides: L1 "Build a BJT", L2 "Build a NAND gate from BJTs" (prereq BJT), L3 "Build a 4-bit ALU from NAND gates" (prereq NAND).
> - 5 fake users: `@kira @otto @ravi @mei @sven` with VerifierRoles in Electronics at varying levels.
> - 1 in-progress Review on the L3 ALU guide with 2 votes cast, 3 pending.
>
> **Deliverables:**
> 1. Full Next.js project tree.
> 2. `prisma/schema.prisma` matching the model above + `VendorLink`.
> 3. `prisma/seed.ts` with the seed data.
> 4. All 8 pages above, at minimum read-only working with the seed data.
> 5. Working POST endpoints for guide creation and vote casting, with all the rules above enforced server-side.
> 6. A `README.md` with setup instructions (`pnpm install`, `pnpm db:push`, `pnpm db:seed`, `pnpm dev`).
> 7. Unit tests for the rules: hierarchy validation, jury sizing, mandatory justification length, max open disputes.
>
> **Things to explicitly not do:**
> - Don't add display ads, banners, popups, or any tracking script.
> - Don't add a follow/friend/notifications social layer.
> - Don't add a payment/subscription system.
> - Don't add an AI-generated content feature for guides (verifiers may use AI privately, but the site itself doesn't generate guide content).
> - Don't gate any content behind login except writing/voting.

---

## PROMPT C — Iteration prompts (after first build)

Use these one at a time, in order:

> **C1 — Polish the guide page:** Make the guide page feel like reading a great textbook. Improve typography (line height, measure, drop caps optional for L1 guides), add a sticky right-rail table of contents generated from the Markdown headings, and make the Prerequisites box visually unmissable but not gaudy. Keep it dark-theme native.

> **C2 — Make the verifier vote panel a delight:** the justification textarea should grow as you type, show a character counter, refuse submission below 50 chars with a friendly inline message ("Tell the author what specifically needs to change — silent votes hurt the system"), and after submission animate the vote into the jury list on the left. Add a "see other verifiers' justifications" toggle that's collapsed by default so reviewers form their own opinion first.

> **C3 — Subject tree and search:** make the left-rail subject tree keyboard-navigable (arrow keys, enter to open), add a Cmd-K command palette that searches subjects + guides + methods, and show a small "L1 · L2 · L3" coverage histogram next to each subject so users can spot subjects with hierarchy gaps.

> **C4 — Methods promotion UI:** on a guide page, if a non-main method has accumulated more usage / better ratings than the main method, show an inline banner: "This method is now more popular than the main one. Promote?" with a button that opens a verifier vote for promotion. Build the API endpoint for that too.

> **C5 — Disputes UX:** make the disputes page feel like a court docket, not a comment section. Each dispute card shows: type chip, status (Open/Under Review/Resolved/Dismissed), who opened it, days open, # of arguments, and the resolution if any. Filter chips at top. No upvotes on disputes — this isn't Reddit.

> **C6 — Onboarding for first-time visitors:** add a one-screen "What is B.L.U.E.?" explainer that shows ONCE on first visit and is reachable from the footer afterwards. No modal nag, no email capture, no "sign up to see more." Just a short page that explains the hierarchy, the verifier system, and that it's free forever.

---

## Tips for prompting AI builders effectively

- **Show, don't only tell.** Attach `prototype/index.html` as a visual reference whenever possible.
- **Prefer "don't do X" lists.** AI builders love to add sign-up modals, email captures, gradient hero sections, and AI chatbots. Pre-empt them.
- **One screen at a time after the first pass.** Big-bang refactors degrade quality.
- **Re-paste the non-negotiables every 3–4 prompts.** Long sessions drift.
- **Always specify the seed data.** Empty UIs are uninspired UIs.
