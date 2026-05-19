# Scaling B.L.U.E. and Getting People to Actually Contribute

> *Two real questions:*
> 1. *How does this go from "a working prototype" to something with tens of thousands of lessons?*
> 2. *Why would anyone bother writing or checking lessons, for free, forever?*

This document answers both. It's the document to read after `12-forum-proposal.md`.

---

## Part 1 — The growth model: small loops, in order

Every successful free-content site followed the same path: **don't try to be big. Be small and useful in one niche, then let the loops start.** Wikipedia started with one writer (Larry Sanger) seeding philosophy articles. Stack Overflow started with one tag (`c#`). Khan Academy started with one subject (algebra).

The mistake is launching with 50 empty subjects and a "please contribute" banner. Nobody contributes to empty.

### Stage 1 — One subject, four lessons (Month 0)

Pick a single Level 1 lesson and write it really well. The prototype shows it: **"How to make a resistor."** That's the seed.

Then write three more in the same subject — *transistor, NAND gate, ALU*. The full chain. So a visitor sees:
- A real Level 1 lesson that's actually useful on its own
- A Level 2 lesson that explicitly builds on it
- A Level 3 lesson that explicitly builds on that
- A Level 4 lesson at the top

This shows the **hierarchy idea is real, not just talked about.** It's tangible proof the concept works. That tangible proof is what makes people want to add lesson 5.

### Stage 2 — First 10 lessons (Months 1–3)

The job in this stage is **find five subject-experts** in your pilot subject (Electronics) and get them to each write *one lesson*. Not ten. One.

Where to find them:
- Subreddits like `r/AskElectronics`, `r/electronics`, `r/diyelectronics`
- The EEVblog forum
- Hackaday comment sections
- The /r/Electronics Discord
- University maker-spaces (email a few)

Approach: *"I'm building a free, community-checked encyclopedia of practical skills. We have a working pilot lesson on resistors — can you write the one on soldering / capacitors / your speciality?"*

10 lessons is enough for the site to feel *populated.* Below 10, it feels abandoned. Above 10, the social proof flips.

### Stage 3 — First 50 lessons (Months 3–9)

Now the **flywheel starts**. The pattern:

1. Lesson gets published → author gets reputation
2. Reputation unlocks the ability to verify other lessons
3. Verifying others' lessons earns reputation
4. New verifiers attract submissions (because submissions now get reviewed quickly)
5. Faster reviews attract more writers
6. More writers → more lessons
7. **Back to step 1**

This is the same loop Stack Overflow used to grow from 0 to its first 100,000 questions. The whole thing is reputation-driven, and the reputation is fully scoped per-subject so you can't game it across subjects.

By 50 lessons, the pilot subject has critical mass and a small but real community of regulars.

### Stage 4 — Adding the second subject (Months 9–15)

Don't add subject 2 until subject 1 has its own self-sustaining community. The signal is: *new writers are showing up without you personally inviting them*.

Then pick an adjacent subject. From Electronics, the natural next ones are:
- **3D printing** (overlaps with electronics for makers)
- **Programming** (Electronics → microcontrollers → code)
- **Cars & engines** (electronics + mechanical)

Each new subject seeds the same way: one founding writer, four lessons forming a chain, then recruit five subject-experts.

### Stage 5 — Network effects (Year 2+)

Past about 5 well-populated subjects with 50+ lessons each, the site **stops needing centralised recruitment**. New subjects appear because contributors want them. Wikipedia hit this point around 2003 and never looked back.

The mechanisms that make this happen:
- **Sub-subjects branching** off existing ones (Electronics → Components → Resistors → SMD-resistors → …)
- **Wishes** that highlight gaps — anyone seeing "this subject is empty, 240 points pledged for the first lesson" thinks *I could write that*
- **Cross-subject prerequisites** — a Cooking lesson needing the Thermometer lesson from the Tools subject, which the Cooking community then has to fill in

This is the **hierarchy-as-growth-engine** insight that makes Sebastian's design specifically good. Unlike a flat tag system, you literally can't write Level 3 stuff without someone first writing Level 2. That guarantees breadth.

### Stage 6 — Long-tail subjects (Year 3+)

By this point, lessons get written about specific things (*"how to whittle a wooden spoon", "how to brew a New England IPA", "how to lay a brick wall"*) because the platform is there for them. This is the **Wikipedia long tail** — most readers go to articles most editors have never heard of.

The portal pages (the Wikipedia-style ones built into the prototype) become the main entry points. Search becomes primary navigation. The sidebar tree is no longer the discovery mechanism — it's just a fallback.

---

## Part 2 — Why people will contribute

This is the harder question, and the one most projects get wrong. Most communities die because the founders assumed *"if we build it, they'll come"* — they don't.

Look at what motivates contributors on real platforms:

| Platform | What contributors get |
|---|---|
| **Wikipedia** | Status (edit count, watchlists, barnstars, the *feeling* of being an editor) |
| **Stack Overflow** | Reputation that's portable to job applications, gold badges with social weight |
| **GitHub** | A public portfolio that recruiters look at |
| **Reddit** | Karma + community identity + entertainment |
| **YouTube** | Money, fame, audience |
| **Khan Academy** | Curriculum used in real classrooms (mission-fit) |

For B.L.U.E. specifically, contributors will fall into **five archetypes**, each motivated by a different thing. Designing for all five is the difference between a project that grows and one that stalls at 30 lessons.

### Archetype 1 — The expert who wants to teach (intrinsic)

A retired electrical engineer who genuinely wants to share what he knows before he forgets it. Or a teacher who wishes a free version of her course existed.

**What motivates them:** mission. The act of writing the lesson *is* the reward.

**What we give them:**
- A platform that takes their work seriously (Wikipedia-style respect, verifier review)
- Permanent author credit on every lesson
- Translation tracking — *"your lesson now exists in 7 languages, read by people in 84 countries"*
- A serif Wikipedia look that telegraphs *this is a real encyclopedia*, not a blog farm

This archetype is the *founding* audience. The first 50 lessons come from them.

### Archetype 2 — The expert who wants recognition (status)

A working senior engineer who's good at what they do, wants industry visibility, and doesn't have time to maintain a blog.

**What motivates them:** reputation that's visible and portable.

**What we give them:**
- A **subject-and-level-scoped reputation score** — *"Electronics, L4 verifier, 4,217 reputation"* is a meaningful credential
- A **public profile page** with badges, authored lessons, verifier history
- An **exportable contributor identity** — a permanent URL like `blue.example/u/handle` that they can link from LinkedIn or their CV
- The Stack-Overflow-style **badge system** (`Founding verifier`, `Mentor`, `Polymath`, `10 approvals`) — visible signal of contribution

This archetype is what *makes* the verifier layer work. They join to be seen as experts, and the side effect is that they review everyone else's work for free.

### Archetype 3 — The hobbyist who wants to learn by teaching (growth)

Somebody who's been making cabinets at home for a year and wants to deepen their knowledge by writing it down.

**What motivates them:** *Feynman effect* — explaining something is how you really learn it. Plus a real audience of peers who'll point out your mistakes.

**What we give them:**
- The 4-level reading hierarchy — they can write at Level 1, learning what they don't know in the process
- **Verifier feedback** — every rejection comes with a written justification, which is free education
- **Mentor pairing** — senior verifiers volunteer to walk less-experienced writers through their first submission
- **The reading experience** — they read others' lessons too, and the per-method ratings tell them what's actually proven vs theoretical

This is the *biggest* archetype by count. Wikipedia's editor base is overwhelmingly people who learn by editing.

### Archetype 4 — The bounty-driven contributor (extrinsic)

Someone who, faced with a wish ("How to make a flip-flop — 240/300 reputation pledged"), thinks *"I could write that this weekend and pocket a 20% reputation bonus."*

**What motivates them:** an explicit, gameable goal.

**What we give them:**
- **The Wishes system** as it exists in the prototype — pledged reputation, visible progress bar, fulfilment bonus, `Bounty hunter` badge on profile
- **Auto-suggestion** — when someone starts a Write flow that matches an open wish, the prototype already tells them *"this could claim a bounty"*
- **A leaderboard** of recently-fulfilled wishes, so the social proof of "this works" is constant

This archetype is **what fills the gaps**. Without bounties, popular subjects get over-served and unpopular ones starve. With them, demand is visible.

### Archetype 5 — The lurker who eventually clicks (passive)

99% of visitors will never write a lesson. They read, they 👍, they share. That's fine — they're the *audience* that makes the work meaningful.

**What we give them:**
- **No-account-needed reading** — every lesson is fully accessible without signing up
- **One-click "💚 thank the author"** — a tiny reward to the writer that costs the reader nothing
- **The "I used this" button on methods** — passes a strong signal back to the system about what's actually being adopted, without the lurker having to type anything

The crucial design choice: **lurkers don't subsidise the system; they validate it**. Even passive readership is fuel because it's what makes contributors feel their work matters.

---

## Part 3 — The reputation economy, in detail

Reputation is the central incentive. Here's what earns it and what it unlocks. (Numbers below are tunable via RFC after launch.)

### How you earn reputation

| Action | Reputation | Notes |
|---|---|---|
| Submit a lesson | +1 | Pre-publication, just for submitting |
| Lesson is published | +10 to author, +3 each co-author | The main payoff |
| Lesson hits 100 helpful votes | +25 (one-time) | Quality bonus |
| Lesson hits 1,000 helpful votes | +100 (one-time) | Reach bonus |
| Lesson hits 10,000 helpful votes | +500 (one-time) | Hit-lesson bonus |
| Cast a verifier vote with ≥50-char justification | +2 | Encourages thoughtful voting |
| Vote on a lesson that passes (you voted yes) | +5 bonus | Rewards good judgment |
| Reader presses 💚 thanks | +1 to author + co-authors | Light-touch reward |
| Open a wish | 0 (free) | But costs 5 reputation to pledge |
| Pledge to someone else's wish | −5 (a real cost) | You're committing your own status |
| Fulfil a wish (publish a matching lesson) | +20% of pledged total | Big payoff, attracts bounty hunters |
| Open an RFC | 0 | Free to propose |
| RFC is accepted | +20 to author | Rewards institutional contribution |
| Open a dispute that is later resolved as valid | +5 | Rewards quality control |
| Open a dispute that is dismissed as spam | −10 | Strong disincentive against frivolous disputes |

### What reputation unlocks

| Reputation (in subject) | Capability |
|---|---|
| 0 | Read everything. Vote helpful on lessons. Use the "I used this" button. |
| 10 | Submit lessons. Comment on Talk pages. Open disputes. Rate methods. |
| 20 | Mark methods as "I used this" (anti-spam threshold). |
| 50 | Suggest edits to existing lessons (queued). |
| 200 | Pass exam for L1 verifier. Vote on L1 reviews. |
| 500 | Pass exam for L2 verifier. |
| 1000 | Pass for L3. Mentor newcomers (their pass earns you reputation too). |
| 2000 | Pass for L4. Eligible for Auditor election. |
| 5000 | Open subject-scoped RFCs (taxonomy changes, jury rules in that subject). |
| 10000 (across all subjects) | Eligible to be elected a Steward. |

This is borrowed almost directly from Stack Overflow's *privilege ladder*, which has run for 16 years on essentially this idea. The numbers are tuneable per-subject via RFC.

### Why this avoids the Stack Overflow trap

Stack Overflow's reputation has been criticised for **rewarding quantity over quality** and producing a "wall of grumpy moderators."

The protections built into B.L.U.E.:

- **Reputation is per-(subject, level)** — never global. A 5000-rep Electronics expert is a complete newcomer in Cooking.
- **All voting requires written justification** — verifiers can't snipe-reject without explaining why.
- **Recall mechanism** — verifiers with patterns of bad-faith voting lose their status (auditor decision). Auditors can be recalled by a 2/3 verifier vote.
- **Reputation can be lost** (frivolous disputes, malicious justifications) — it's not a one-way ratchet.
- **Mentor bonuses** — explicitly rewards senior verifiers for *bringing new people in*, not just sniping submissions.

---

## Part 4 — Non-reputation incentives that also matter

Reputation is the engine, but several smaller systems quietly do a lot of the work.

### Badges (free, social, addictive)

Cheap to award, massively motivating. Examples from the prototype:

- `New here` — automatic, low bar
- `Author` — published your first lesson
- `10 verifier votes` — cast 10 substantive reviews
- `Founding verifier` — among the first 50 verifiers in your subject
- `Mentor` — helped 3 mentees pass their verifier exam
- `Polymath` — verifier in 2+ subjects
- `Bounty hunter` — fulfilled a wish

Stack Overflow's gold badge system has been studied to death. Conclusion: people work *unreasonably hard* to chase them. They cost nothing to award.

### Public attribution everywhere

Every lesson page lists the author by name with a clickable profile link. Every revision is signed. Every verifier vote shows the verifier's name and reasoning. This is the **encyclopedic equivalent of a byline** — and it's worth real money to anyone building a career.

### Translations as growth multiplier

Per the CC BY-SA 4.0 licence, anyone can translate any lesson. The author's profile shows *"your work is now available in 7 languages, read in 84 countries this month"*. That's a powerful psychological reward — and a real one for educators who want global reach.

### Real-world citation

Lessons should be **citeable** like Wikipedia articles. *"As described in [B.L.U.E.: How to make a resistor (v4.2)]"*. Every lesson page should have a "Cite this page" link that produces APA / Chicago / BibTeX. This makes B.L.U.E. show up in real published work — books, papers, course materials — which is a permanent status reward for contributors.

### The "I used this" button (Sebastian's own idea)

Probably the single best feature for incentivising writers. It tells you, the writer, *that 1,247 real people built the thing using your instructions*. Nothing in any other contribution system does this. It's worth pushing hard on.

---

## Part 5 — Anti-patterns we must avoid

Things that have killed every comparable project. The constitution should hard-code prohibitions against these.

| Anti-pattern | Why it kills projects | What we do instead |
|---|---|---|
| **Display advertising** | Turns the site into the product and readers into product | Topical "Materials & tools" links only, fully disclosed |
| **Algorithmic feed** | Optimises for engagement (bad content wins) over usefulness | Curated portal pages + hierarchy navigation |
| **Premium tier / paywalls** | Splits the community, breaks the "free encyclopedia" promise | The licence-lock RFC prevents this from passing |
| **AI-generated lesson body** | Destroys trust, hallucinates, undermines verifier work | Hard-coded ban. Verifiers may use AI privately to research, but the lesson body is human-written |
| **Founder-as-bottleneck** | Project dies when founder leaves (most common cause of death) | All assets owned by the legal entity, not by Sebastian or anyone; bus-factor ≥ 2 in every role |
| **Engagement metrics** (DAU, time-on-site) | Goodhart's law — you optimise for the metric and lose the mission | Track "lessons written, lessons published, helpful votes per published lesson" instead |
| **Real-time notifications** | Trains contributors to be addicted, not productive | Daily digest emails only; opt-in |
| **Anonymous voting on disputes/RFCs** | Lets bad actors swing decisions invisibly | All voting public, all votes attached to a written justification |

---

## Part 6 — Concrete year-1 milestone plan

So you can tell people *"we're at X% of the way to a real thing"*.

| Quarter | Target | Defines success |
|---|---|---|
| **Q1** | 4 seed lessons (the Electronics ladder: resistor → transistor → NAND → ALU). 5 founding verifiers in Electronics. RFC-001 through RFC-006 accepted. Hosting on a free tier. | The site exists, has 4 real lessons, and the governance process has produced its first 6 accepted decisions. |
| **Q2** | 20 lessons in Electronics. 15 verifiers. First 5 wishes opened. First 3 disputes resolved. First non-Electronics lesson appears (in any subject). | Self-sustaining contributor flow in 1 subject. |
| **Q3** | 50 lessons across 2–3 subjects. 30+ verifiers. 5 fulfilled wishes. First translation goes up. Open Collective active with 10+ patrons covering hosting. | Crossed the "feels populated" threshold. Hosting is now community-funded. |
| **Q4** | 100 lessons across 3–5 subjects. 75 verifiers. First Steward election held. First fiscal-sponsor application submitted (Open Source Collective). | The project has its own legal/financial infrastructure and elected leadership. **No longer needs Sebastian or any single person to keep running.** |

At Q4, the system Sebastian wanted exists — *"a system that does not need me to function"*.

---

## TL;DR — the two-line version

**To scale:** start with one subject, four lessons that explicitly chain into each other. Recruit five experts. Don't add subject 2 until subject 1 is self-sustaining.

**To incentivise:** combine permanent author credit (intrinsic), subject-scoped reputation that's portable (status), Stack-Overflow-style badges (cheap and addictive), wishes with reputation bounties (extrinsic), and the "I used this" button that closes the feedback loop between writer and reader (Sebastian's own idea, possibly the strongest single feature).

The rest is just keeping the anti-patterns out.
