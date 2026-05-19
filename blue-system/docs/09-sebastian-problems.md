# 09 — Solutions to Sebastian's Three Open Problems

> *"There are certain problems that need to be solved first. 1. Hosting. 2. Collaborators / Role Assignment. 3. Fundamental Architecture."*
> — Sebastian Rey, on the [B.L.U.E. forum](https://github.com/SebReyWriter/TheBLUEForum/discussions)

This document analyses each problem, looks at how comparable projects have solved it, and proposes a concrete path forward.

---

## Problem 1 · Hosting

> *"So far, I don't have a way to fund the server space for the site… until then, all code, ideas, and discussions are purely theoretical."*

Sebastian later noted that hosting has been *"effectively solved (for the time being)"*, but this is the most fragile of the three. We need a permanent answer.

### How comparable projects handle hosting

| Project | Reach | Hosting cost (annual) | Model |
|---|---|---|---|
| **Wikipedia** | 17 billion page-views / month | ~$3 million for servers (of $34M total) | Donations, grants, endowment |
| **MDN Web Docs** | ~1 billion page-views / month | Hosted by Mozilla Foundation | Folded into Mozilla budget |
| **arXiv** | ~250 million downloads / year | ~$2 million | Cornell + member institutions + Simons Foundation |
| **Internet Archive** | 75 PB stored | ~$10 million | Donations + small philanthropic grants |

Encyclopedic content is genuinely cheap to host. Wikipedia in compressed plain text is under 25 GB — the same size as a few HD movies. The expensive bits are search, image storage, and global edge caching.

### What hosting B.L.U.E. actually costs

For the first 12–18 months (pilot phase, single subject, < 1 million page-views/month):

| Need | Service | Monthly cost |
|---|---|---|
| App server (Next.js / Node) | Railway, Fly.io, or Hetzner | $5–30 |
| Postgres database | Neon (free tier → $20) | $0–20 |
| Static asset CDN | Cloudflare (free tier) | $0 |
| Search | Postgres FTS at first; Meilisearch later | $0 |
| Email (verification, notifications) | Resend (free tier) | $0 |
| Domain | Namecheap / Porkbun | $1 |
| Monitoring | Better Stack / UptimeRobot | $0 |
| **Total** | | **$6–50 / month** |

Phase-2 (multiple subjects, ~10M page-views/month) jumps to about $150–400/month. Wikipedia-scale doesn't happen until you have millions of contributors, and at that point donations easily cover it.

### Proposed solution

A four-stage plan that scales from "$6/month run by one person" to "self-sustaining non-profit":

#### Stage 1 — Bootstrap (months 0–6): one person, one credit card
- Domain + Hetzner VPS + Cloudflare free tier: **~$10/month**
- A single trusted maintainer covers it personally. No tax-deductibility, but no overhead either.
- Visible "Hosted by [Name], personally" notice in the footer.

#### Stage 2 — Patreon / Open Collective (months 6–18): small recurring donations
- Open a [Open Collective](https://opencollective.com/) — designed exactly for this. Transparent ledger of every cent in and out.
- Target: 30 patrons giving $5/month = $150/month. Covers Stage-2 needs comfortably.
- Why Open Collective over Patreon: tax-deductible via fiscal host (Open Source Collective is the standard fiscal host for code projects), full transparency, no platform lock-in.

#### Stage 3 — Fiscal sponsorship (years 1–3): no foundation needed yet
- Apply to **Open Source Collective** (≈$0 setup, 10% admin fee) or **Software Freedom Conservancy**.
- They handle: tax-deductibility (US 501(c)(3) status), legal liability, banking, accounting.
- B.L.U.E. continues to operate as a project under their umbrella; we don't have to file paperwork or pay lawyers.
- This is how [Babel](https://babeljs.io/), [Webpack](https://webpack.js.org/), and many other large open-source projects operate.

#### Stage 4 — Own foundation (year 3+): only when annual income > $250k
- At this point it makes financial sense to create a dedicated non-profit (likely in Estonia, Switzerland, or the Netherlands per `docs/04-legal-and-jurisdiction.md`).
- Modelled on the Wikimedia Foundation but smaller and leaner.
- Endowment policy from day one: keep 18 months of runway in reserve before spending growth income.

### Funding sources, in order of priority
1. **Reader donations** (Wikipedia's banner-ad-of-conscience approach, but lighter — once per year, not on every page).
2. **Affiliate revenue** from the topical "Materials & tools" sections of lessons (already in the spec).
3. **Grants** — Mozilla Open Source Support, Sloan Foundation, NLNet, Mozilla MOSS. Several actively fund open educational projects.
4. **Institutional sponsorships** — universities, libraries, makerspaces.
5. **Wikimedia Enterprise–style API** for commercial users (only at Stage 4+).

### Hard rule (Sebastian's vision-lock)
- **No display advertising. Ever.** Hard-coded into the constitution. Breaking this triggers automatic asset transfer to a successor foundation.
- **No paywalls. Ever.** Same rule.
- All financial records public, updated monthly.

---

## Problem 2 · Collaborators / Role Assignment

> *"I'm a writer, not a developer. Which means I'll need a lot of help… ideally, different people should be responsible for different parts of the build."*

This is the textbook "founder transition" problem in open-source projects. There is a literature on this — we don't have to guess.

### How comparable projects solved it

| Project | Founder model | Transition trigger | Final structure |
|---|---|---|---|
| **Wikipedia** | Jimmy Wales as benevolent founder | Foundation created within 2 years | Wikimedia Foundation board + community elected committees |
| **Debian** | Ian Murdock | 1997, four years in | Annually-elected Project Leader + Technical Committee |
| **Django** | Adrian Holovaty + Jacob Kaplan-Moss | 2014 (10 years in) | Core team → 2020 → Technical Advisory Board |
| **Python** | Guido van Rossum ("BDFL") | 2018, after 28 years | Elected 5-person Steering Council |
| **Apache HTTPD** | Brian Behlendorf | 1999 | Apache Software Foundation, project management committees |

The pattern is consistent: **start with a small trusted group; formalise roles only when the project outgrows informal trust.**

### Proposed role structure for B.L.U.E.

A six-tier structure derived from the GitHub Open Source Guide and the Wikimedia model, simplified for a project at our scale:

```
                           ┌──────────────┐
                           │  Stewards    │  small body that arbitrates,
                           │  (3–5 elected)│  resolves disputes, holds keys
                           └──────┬───────┘
                                  │
                  ┌───────────────┼───────────────┐
                  │               │               │
          ┌───────▼──────┐ ┌──────▼─────┐ ┌──────▼──────┐
          │  Maintainers │ │  Auditors  │ │ Moderators  │
          │  (technical) │ │ (editorial) │ │ (community) │
          └───────┬──────┘ └──────┬─────┘ └──────┬──────┘
                  │               │               │
          ┌───────▼──────────────▼───────────────▼──────┐
          │              Verifiers (per subject)          │
          │              Writers (anyone)                 │
          │              Readers (anyone)                 │
          └──────────────────────────────────────────────┘
```

### Role definitions (all written in plain English)

| Role | What they do | How you get the role | How you lose it |
|---|---|---|---|
| **Reader** | Reads lessons. | Just visit the site. | n/a |
| **Writer** | Submits lessons. | Sign up. | Banned for repeated spam / abuse. |
| **Verifier** | Reviews + votes on submissions in their subject. | Pass a subject-specific exam. | 3 unjustified votes, or pattern of bad-faith review (auditor decision). |
| **Maintainer** | Reviews & merges code changes. Different maintainers own different parts (frontend, backend, search, etc.). | Nominated by an existing maintainer + ratified by stewards. | Steward decision after warning. |
| **Auditor** | Resolves editorial disputes, approves new subjects, oversees the verifier pool. | Elected by all verifiers above level 3, 1-year term. | Term ends, or recall vote. |
| **Moderator** | Spam triage, dispute routing, keeps community discussion healthy. | Nominated, ratified by stewards. Limited term. | Same. |
| **Steward** | Holds the keys to legal entity, domain, finances. Last-resort arbitration. | Elected from auditors + maintainers, 2-year term, 3–5 seats. | Term ends, or recall (high bar). |

### Sebastian's specific concerns, addressed

> *"I'll need people with experience, who I trust to carry out functions while I'm not there."*

**Solution:** A formal `MAINTAINERS.md` file at the root of the repo lists every active maintainer, what part they own, and their backup. No single point of failure. Maintainership can be "honorary emeritus" if someone steps back — recognition without ongoing burden.

> *"Ideally, different people should be responsible for different parts of the build."*

**Solution:** Codify *areas of ownership* (Frontend, Backend, Search, Mobile, Translations, etc.) and require **at least two maintainers per area** before that area is considered "covered." A single-maintainer area is a known fragility flag and shows up on the project's health dashboard.

> *"My end goal is to help build a system which does not need me to function."*

**Solution:** This is called **succession planning** in non-profit governance. We bake it into the constitution from day one:

1. Sebastian (or whoever holds the founder role) is **explicitly not** automatically a steward.
2. The first cohort of 3 stewards is appointed by Sebastian, but their first task is to draft an open election process for cohort 2.
3. All assets (domain, repos, bank account, social handles) are owned by the legal entity, never by an individual.
4. A documented **bus-factor exercise** done every 6 months: "If person X disappeared tomorrow, what breaks?" If any single person scores > 30%, that area is a recruitment priority.

### Pragmatic first-step recruitment

We need *six volunteers* to start:

| Role | Time / week | What they do |
|---|---|---|
| **Lead frontend developer** | 5–8 hrs | Owns the React/Next.js app |
| **Lead backend developer** | 5–8 hrs | Owns the API, database, auth |
| **DevOps / SRE** | 2–4 hrs | Deployment, monitoring, backups |
| **Community / outreach** | 4–6 hrs | Forum, Discord, recruiting writers |
| **Editorial lead** | 4–6 hrs | Style guide, pilot subject expansion |
| **Legal-curious volunteer** | 2 hrs | Watches for IP issues, drafts policies |

Total: about 25 person-hours per week to keep the project moving. That's six people contributing one evening each. Wikipedia started smaller.

---

## Problem 3 · Fundamental Architecture

> *"If no one is able to agree on how the site should be structured, then it will be impossible to move forward."*

This is exactly the problem Python solved with **PEPs** (Python Enhancement Proposals), Rust solved with **RFCs**, and the IETF has solved with **RFCs** since 1969. The solution has a name: a structured, public proposal process.

### The proposed process: B.L.U.E. RFCs

Any proposal that affects how the project works — architecture, policy, tooling, governance — is written up as a Request for Comment (RFC). The format:

```
RFC-NNN: Short descriptive title

  Status:    DRAFT | PROPOSED | ACCEPTED | REJECTED | SUPERSEDED
  Author:    @handle
  Created:   YYYY-MM-DD
  Decided:   YYYY-MM-DD (when applicable)

  Summary
  -------
  One paragraph. Plain language.

  Motivation
  ----------
  What problem does this solve? What happens if we do nothing?

  Detailed design
  ---------------
  Specifics. Diagrams. API shapes. Schema. Concrete enough to implement.

  Alternatives considered
  -----------------------
  Other approaches we looked at and why we rejected them.

  Drawbacks
  ---------
  Honest list of what this trades away.

  Open questions
  --------------
  Things we don't yet have answers for.
```

### Decision rules (the part most projects get wrong)

| Type of change | Required approval | Cooling-off period |
|---|---|---|
| **Bug fix / documentation** | 1 maintainer review | None |
| **New feature** | 2 maintainer reviews | 48 hours for comments |
| **Behaviour change to existing feature** | RFC + 2 stewards' approval | 1 week |
| **Architecture change** | RFC + supermajority (⅔) of stewards | 2 weeks |
| **Governance change** | RFC + supermajority of stewards + supermajority of verifiers | 4 weeks |
| **Constitutional change** (mission, license, anti-ads) | RFC + ⅔ stewards + ⅔ verifiers + 60-day window | 60 days |

### Why this is better than just "having debates"

1. **The decision is preserved forever.** Future maintainers can read why something was chosen, not just what was chosen. Wikipedia's archives of village-pump debates are gold for this reason.
2. **Authority is bounded by the process.** No one — including the founder — can change architecture by fiat. The rules are the same for everyone.
3. **The community can disagree without it killing the project.** Disagreements happen on the RFC; once decided, work continues. Compare to projects without this, where every architectural decision becomes a personal fight.
4. **It scales.** The same process works whether the project has 5 contributors or 5,000.

### The first six RFCs to write

To get the project unblocked, we need agreement on these six things, in this order:

| # | RFC topic | Why it must come first |
|---|---|---|
| **001** | Content license (CC BY-SA 4.0) | Everything else assumes this |
| **002** | Technical stack (Next.js + Postgres + Prisma) | Required before any code |
| **003** | Verifier jury rules (size, vote rules, recall) | Core editorial system |
| **004** | Subject taxonomy & how new subjects are added | Affects every lesson |
| **005** | Constitution: mission, anti-ads rule, license-lock | The lines that cannot be crossed |
| **006** | Governance: how stewards are elected and replaced | How the project survives leadership change |

After these six, the architecture is "frozen enough" to start building. New RFCs continue to be opened, but each one builds on a stable base.

### Conflict resolution: what to do when an RFC stalls

If an RFC has been open for 30 days without consensus:
1. The stewards may call a **structured discussion**: each side writes a single 500-word summary of their position. No back-and-forth.
2. If still deadlocked, the stewards may **table the RFC indefinitely** with a note explaining why. It can be re-opened later when circumstances change.
3. As a final resort, the stewards may **decide by majority**, with each one publishing a written rationale.

The community can override a steward decision via a 2/3 verifier recall vote — but this is the nuclear option.

---

## Summary: a one-page plan

```
HOSTING                                COLLABORATORS                          ARCHITECTURE
══════════════                         ══════════════                         ══════════════
                                                                              
Stage 1: One maintainer pays           Six volunteers (Frontend, Backend,     RFC process (modelled on
~$10/mo on Hetzner.                    DevOps, Community, Editorial,           Python PEPs, Rust RFCs).
                                       Legal-curious). 25 hrs/week total.     
Stage 2: Open Collective.                                                     First six RFCs decide license,
30 patrons × $5/mo.                    Six-tier role structure                stack, jury rules, taxonomy,
                                       (Reader → Writer → Verifier →          constitution, governance.
Stage 3: Fiscal sponsor                Maintainer/Auditor/Moderator →         
(Open Source Collective).              Steward).                              Decision rules scale with
Tax-deductible donations.                                                     impact. Constitutional
                                       Succession planning baked into         changes require 60-day
Stage 4: Own foundation                constitution. Sebastian's role is      window + supermajority.
once revenue > $250k/yr.               replaceable from day one.              
                                                                              "Disagreements happen on
Hard rules: no ads, ever.              All assets owned by the legal          the RFC; once decided,
No paywalls, ever.                     entity, not by individuals.            work continues."
All books public.                      
```

## What this means for Sebastian personally

If we adopt this plan, Sebastian can:

1. **Step in and out at will.** His role is honorary, not load-bearing.
2. **Veto only constitutional changes** during a 6–12 month founding period, then become a regular community member.
3. **Be credited forever** — every lesson page links back to the [original video](https://www.youtube.com/watch?v=qcRKmm3B25c), and the "About" page tells the founding story.
4. **Disappear safely.** If he loses interest, gets hit by a bus, or decides to focus on other things, the project survives.

This is the goal he stated in the video: *"a system that does not need me to function."* The plan above is how you actually achieve that.
