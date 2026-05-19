# A Working Prototype & Concrete Plan for B.L.U.E.

> *To Sebastian and everyone on the [B.L.U.E. forum](https://github.com/SebReyWriter/TheBLUEForum/discussions)*

Hi Sebastian, and hi everyone.

I watched your video about the B.L.U.E. System, read your forum post, and spent some time building something concrete you can react to. Below is:

1. **A working prototype you can click around in** — a single HTML file you can open in any browser. No install, no signup, no server.
2. **Concrete solutions to all three of the open problems you listed** — hosting, collaborators/roles, and fundamental architecture.
3. **A specific path to get this actually built**, broken down into things small enough for individual volunteers to pick up.

I'm not asking to lead anything. I just wanted to give the conversation a concrete starting point — something specific to agree with, disagree with, or fork. That feels like the bottleneck right now.

---

## 1 · The prototype

📁 **File:** `prototype/index.html`

**How to use it:** open the file in any browser. That's it. Everything runs in your browser, persists to your browser's local storage, and can be reset from the footer at any time. No backend, no accounts, no privacy concerns.

### What's in it

- **A real lesson** — "How to make a resistor" — written out at all four difficulty levels (Beginner → School → Hobbyist → Specialist), with four methods (pencil trick → carbon-composition → wire-wound → thin-film deposition). This shows what the multi-level reading idea actually feels like.
- **Six other lessons** seeded with realistic content (BJT, NAND gate, ALU, soldering, mortise-and-tenon joint).
- **Working verifier jury system** — open the ALU lesson, click "Cast your vote", try voting without a 50-character justification (it won't let you). Try voting properly and the lesson auto-publishes when the jury majority agrees.
- **Working RFC system** for governance — 5 seeded RFCs covering license, stack, jury rules, lesson forks, and founding moderators. You can vote on the open ones.
- **Working wishes (bounties) system** — three seeded lesson-wishes you can pledge reputation to.
- **Working talk pages** — a Wikipedia-style threaded discussion attached to every lesson.
- **Working dispute docket** — three seeded disputes showing the open/under-review/resolved flow.
- **A Roles page** showing how the seven roles fit together and what jobs are open.
- **Search palette** (⌘K) that finds lessons, subjects, wishes, and RFCs.

### What it looks like

It deliberately looks like Wikipedia (Vector skin, serif headings, infobox on the right, talk-page tab, "in plain words" callouts) because Wikipedia is the trust standard for "free encyclopedia" — anyone landing on B.L.U.E. should immediately recognise what kind of site they're on.

Everything that's a real interaction works. Everything that's a stub (like the "Edit" tab) is clearly placeholder. There is nothing fake.

### What I'm not claiming

The prototype is not the final design. It's a sketch to make conversation easier. Disagree with anything — the colour scheme, the wording, the role names, the data model. The point is that there's now something specific to disagree with.

---

## 2 · Solutions to the three problems

### Problem 1 — Hosting

**It's not the expensive thing people fear.** A pilot like B.L.U.E. costs **$6–50 a month** to host until it's well past 1 million monthly page-views.

Suggested 4-stage plan:

| Stage | When | Cost | How it's paid |
|---|---|---|---|
| **1. Bootstrap** | Months 0–6 | ~$10/mo (Hetzner + Cloudflare free) | One trusted maintainer pays personally. Visible "hosted by X" in footer. |
| **2. Open Collective** | Months 6–18 | ~$150/mo | 30 patrons × $5/mo via [Open Collective](https://opencollective.com/). Fully transparent ledger. |
| **3. Fiscal sponsor** | Years 1–3 | Donation-funded | Apply to Open Source Collective or Software Freedom Conservancy. Tax-deductible donations via their 501(c)(3) status. No need to file our own paperwork yet. |
| **4. Own foundation** | Year 3+ if revenue >$250k/yr | Foundation-managed | Estonia, Switzerland, or Netherlands. Modelled on Wikimedia Foundation, smaller and leaner. Endowment policy: keep 18 months of runway. |

This is exactly how projects like Babel, Webpack, and most large open-source projects fund themselves. Wikipedia itself runs on $11 average donations.

**Hard rules baked into the constitution** (these are your non-negotiables from the video):
- No display advertising. Ever.
- No paywalls. Ever.
- Only topical "Materials & tools" affiliate links on relevant lessons.
- All financial records public, updated monthly.

### Problem 2 — Collaborators & Role Assignment

You said you needed "different people responsible for different parts." This has been solved by every long-running open-source project: Wikipedia, Debian, Django, Python, Apache. The pattern is consistent — **start small and informal, formalise as you grow.**

**Seven roles:**

| Role | Who | What they do | How you get it | How you lose it |
|---|---|---|---|---|
| Reader | Anyone | Reads lessons | Just visit | n/a |
| Writer | Anyone | Submits lessons | Sign up | Banned for repeated abuse |
| Verifier | Subject expert | Reviews & votes on submissions | Pass a subject-specific exam | 3 silent/unjustified votes |
| Maintainer | Trusted coder | Reviews & merges code changes | Nominated + ratified | Steward decision |
| Auditor | Senior verifier | Resolves disputes, approves new subjects | Elected by verifiers, 1-year term | Term ends or recall |
| Moderator | Founding mod team | Spam triage, community health | Nominated, ratified | Term ends |
| Steward | Elected | Holds keys to legal entity, last-resort arbiter | Elected from auditors+maintainers, 2-year term, 3–5 seats | Term ends or recall (high bar) |

**Key principles** that address your "system that doesn't need me to function" goal:
- All assets (domain, repos, bank account) are owned by the legal entity, not by an individual.
- Founder role has no special powers in the data model — there is nothing to forge.
- Every area of responsibility has at least 2 people (bus-factor ≥ 2).
- A documented succession plan baked into the constitution from day one.

**To get going, we need 6 volunteers** giving about 4 hrs/week each:
- Lead frontend developer
- Lead backend developer
- DevOps / hosting
- Verifier recruitment lead
- Community moderator
- Legal-curious volunteer (watches for IP issues)

That's 25 person-hours per week total. Wikipedia started smaller.

### Problem 3 — Fundamental Architecture

This is the one I spent the most time on, because it's the one that's actually been blocking everything else. **Without agreement on how the site is structured, there's nothing to start building.**

The solution comes from open-source projects that have had to make architecture decisions for decades: **Requests for Comment (RFCs).** Python uses PEPs. Rust uses RFCs. The IETF has used RFCs since 1969. They all work the same way:

1. Anyone can propose a change in writing.
2. The proposal is numbered, public, and immutable.
3. It goes through a 7-day discussion window.
4. Then a 10-day **Final Comment Period** (borrowed from Rust) — last chance to object.
5. Then it's accepted or rejected, with the rationale preserved forever — even rejected proposals.

You can see this working in the prototype's **Architecture (RFCs)** tab. Try voting on RFC-3 ("Jury size scales with level") to see the flow.

**Decision rules scale with impact:**

| Change type | Approval needed | Cooling-off |
|---|---|---|
| Bug fix | 1 maintainer | none |
| New feature | 2 maintainers | 48 hours |
| Behaviour change | RFC + 2 stewards | 1 week |
| Architecture change | RFC + ⅔ stewards | 2 weeks |
| Governance change | RFC + ⅔ stewards + ⅔ verifiers | 4 weeks |
| Constitutional change (mission, license, anti-ads) | RFC + ⅔ stewards + ⅔ verifiers | 60 days |

**The first six RFCs to write, in order:**

1. **RFC-001** — Adopt CC BY-SA 4.0 as the content license (everything else assumes this)
2. **RFC-002** — Use Postgres + Next.js as the technical stack
3. **RFC-003** — Verifier jury rules: size, vote rules, recall
4. **RFC-004** — Subject taxonomy and how new subjects get added
5. **RFC-005** — Constitution: mission, anti-ads rule, license-lock
6. **RFC-006** — Governance: how stewards are elected and replaced

After these six, the architecture is "frozen enough" to start building. Everything else builds on a stable base.

**The full architecture proposal** is in `docs/10-architecture-proposal.md` — it includes the database schema, the state machines for submissions/disputes/RFCs, the reputation rules, and ASCII mockups of every key screen. It's the document I'd nominate as RFC-001 itself.

---

## 3 · The 90-day path to a real, deployed site

If even three of the things below happen, B.L.U.E. is real.

### Weeks 1–2 — Get the conversation unblocked
- [ ] Sebastian (or someone) opens RFC-001 with the architecture proposal attached
- [ ] 7-day discussion window
- [ ] 10-day Final Comment Period
- [ ] Vote

### Weeks 3–4 — Find the first six people
- [ ] Post the open roles on the forum
- [ ] First volunteer takes the "Bootstrap host" job (~$10/mo personally)
- [ ] Set up the GitHub org, the Open Collective, and the Discord/Matrix

### Weeks 5–12 — Build the MVP in six modules
- [ ] **Module 1** — Identity (users, sessions, reputation rows)
- [ ] **Module 2** — Content (subjects, lessons, revisions, texts)
- [ ] **Module 3** — Authoring (draft → submit → review flow)
- [ ] **Module 4** — Reviewing (jury selection, votes, justifications)
- [ ] **Module 5** — Talk pages & disputes
- [ ] **Module 6** — RFCs (numbered, public, immutable)

Each module ends with a demoable feature on a public dev URL. The prototype already shows how each module should feel — it can be used as the design reference.

### Week 12+ — Seed the pilot subject
- [ ] Start with Electronics (your example in the video)
- [ ] Seed 15–20 Level-1 lessons (resistor, capacitor, inductor, soldering, multimeter use…)
- [ ] Recruit the first 10 verifiers from the forum
- [ ] Open to public reading

---

## 4 · What I think we should *not* do

Equally important. From watching the video twice:

- **Don't build an AI feature.** You explicitly said the value of B.L.U.E. is human-checked knowledge, and that AI hallucinates. Keep AI out of the lesson body. Verifiers may use AI privately to research, but the site itself does not generate content.
- **Don't take VC money.** A cap table forces exit pressure that breaks the mission. Donations and small grants only.
- **Don't start with medicine, law, or firearms.** Liability would kill the project before it had a chance. Phase 4 at the earliest.
- **Don't hide the project behind a single person.** This is your stated goal — let's protect it by structure, not just by intent.

---

## 5 · What I want to ask you (Sebastian and the forum)

1. **Is the prototype roughly what you imagined?** I tried to be faithful to all eight non-negotiables from the video, but you'd know best where I missed.
2. **Are the seven roles right?** Or should some merge / split / be renamed?
3. **Should we start with the RFC process before anything else?** I think yes — it lets every other decision get made openly. But I'd love a vote on the question itself.
4. **Who wants to take on one of the six volunteer roles?** Even informally for now.

---

## 6 · A note on what this is

I built this in a couple of weekends as a working sketch. It's not a pitch, not a takeover, not a startup. The forum has been short on concrete proposals to react to — so here's one. Disagree, fork, rewrite, use any piece of it. The goal is the same as yours: a free system that teaches anyone how to do anything, and that doesn't need any single person to keep it alive.

The prototype is at `prototype/index.html`. The full set of design and planning docs is in `docs/`. Everything is under CC BY-SA 4.0.

— *(your name here)*

---

## Appendix · The full docs in this repo

| File | What it covers |
|---|---|
| `docs/00-vision.md` | One-page mission + your eight non-negotiables |
| `docs/01-roadmap.md` | Phased plan: Foundation → Spec → MVP → Pilot → Open beta |
| `docs/02-spec.md` | Full system spec with open questions flagged |
| `docs/03-data-model.md` | Prisma-ish database schema sketch |
| `docs/04-legal-and-jurisdiction.md` | Threat model + jurisdiction options |
| `docs/05-pilot-subject.md` | Why to start with Electronics |
| `docs/06-outreach-to-sebastian.md` | Draft messages (this document evolved from) |
| `docs/07-ai-build-prompt.md` | Prompts to hand to AI coding tools to build the MVP |
| `docs/08-ux-research-and-application.md` | UX research synthesis |
| `docs/09-sebastian-problems.md` | Long-form treatment of all three problems |
| `docs/10-architecture-proposal.md` | The full architecture, ready to nominate as RFC-001 |
| `docs/11-navigation-at-scale.md` | How the sidebar stays clean as content grows to thousands of subjects |
| `docs/12-forum-proposal.md` | **This document** — for posting on the forum |
| `prototype/index.html` | **The working prototype** — open in any browser |
