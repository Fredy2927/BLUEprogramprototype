# 01 — Roadmap

A realistic, phased path from "I watched a YouTube video" to "live website with users."

---

## Phase 0 — Foundation (weeks 0–2)
**Goal:** know what you're building and who's helping.

- [ ] Re-read Sebastian's video transcript; write your own one-page summary in your own words.
- [ ] Contact Sebastian (`docs/06-outreach-to-sebastian.md`). Ask permission to use the B.L.U.E. name and ask if he wants any involvement.
- [ ] Start a public GitHub org (e.g. `blue-system`) and mirror Sebastian's discussion forum link.
- [ ] Open a Discord or Matrix server for contributors.
- [ ] Decide on a working name (B.L.U.E., or a fork name if Sebastian prefers).

## Phase 1 — Spec lock (weeks 2–6)
**Goal:** turn the video's ideas into an unambiguous design doc.

- [ ] Finalize `docs/02-spec.md` (hierarchy rules, verifier rules, dispute rules).
- [ ] Finalize `docs/03-data-model.md` (entities + relationships).
- [ ] Get 3–5 outside reviewers (educators, wiki admins, lawyers if possible) to poke holes.
- [ ] Decide jurisdiction + entity type (see `docs/04-legal-and-jurisdiction.md`).
  - Likely candidates: Estonia (e-Residency), Switzerland (foundation), Iceland.
- [ ] Choose a license for content: **CC BY-SA 4.0** is the obvious starting point (same as Wikipedia).

## Phase 2 — MVP (months 2–5)
**Goal:** smallest possible thing that demonstrates the hierarchy + verifier system.

Minimum feature list:
- Account creation + email verification.
- Subject tree (e.g. `Electronics > Digital > Logic Gates`).
- Guide creation with required fields: title, level, prerequisites (links to lower-level guides), body, methods sub-articles.
- Submission → verifier jury (manual selection at first; randomization later).
- Verifier vote with **mandatory written justification**.
- Public guide page with up/down vote.
- Read-only browsing (no login required).

Tech stack suggestion (opinionated, cheap, boring on purpose):
- **Frontend:** Next.js + Tailwind (server-rendered for SEO).
- **Backend:** Postgres + Prisma. Auth via Lucia or NextAuth.
- **Hosting:** Railway / Fly.io to start; move to self-hosted on Hetzner once stable.
- **Search:** Postgres full-text initially, Meilisearch later.

## Phase 3 — Pilot subject (months 5–9)
**Goal:** prove the model works on ONE niche before going broad.

- [ ] Pick the pilot (see `docs/05-pilot-subject.md`).
- [ ] Recruit 20–50 domain enthusiasts as initial writers + verifiers.
- [ ] Seed ~30 Level 1 guides yourself to set the quality bar.
- [ ] Run for 3 months. Watch every dispute, every rejection, every confused new user.
- [ ] Iterate on the verifier flow based on what actually breaks.

## Phase 4 — Open beta (month 9+)
**Goal:** open to additional subjects, but slowly.

- [ ] Add a second subject. See what generalizes and what doesn't.
- [ ] Build the monetization layer (topical product links) only after content is solid.
- [ ] Publish transparency reports (votes, removed content, disputes).

## Phase 5 — Forever
- Stay free.
- Stay weird.
- Stay independent.
