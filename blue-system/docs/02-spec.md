# 02 — System Spec

This document turns Sebastian's verbal description into concrete, implementable rules. Anything marked **[OPEN]** is a decision the project lead (you) needs to make.

---

## 1. Hierarchy

### 1.1 Subject trees
- A **Subject** is a top-level domain (e.g. *Electronics*, *Medicine*, *Carpentry*, *Mathematics*).
- Subjects can have **Sub-subjects** (e.g. *Electronics > Digital > Logic Gates*).
- Anyone can propose a new subject; it must be approved by Auditors (see §4) before guides can be filed under it.

### 1.2 Levels
- Each guide is assigned an integer **level** (1 = most basic).
- A Level N guide MUST declare its **prerequisites** — explicit links to specific Level 1…N−1 guides that cover everything the reader needs to start.
- **Hard rule:** if you can't write a Level N guide without referencing knowledge that doesn't exist at a lower level, you must first write (or get someone to write) that lower-level guide.

### 1.3 Canonical guides
- Only **one canonical guide** per (subject, topic) pair.
- To replace it, a challenger must either:
  - Submit edits to the existing guide, or
  - Submit a competing guide that wins a verifier-jury head-to-head vote.

### 1.4 Methods
- Inside a guide, alternate techniques live as **Method sub-articles**.
- Methods are independently votable and linkable.
- If a Method's usage stats exceed the main method by a threshold over time, the system flags it for **promotion to main method**. **[OPEN: what threshold?]**

---

## 2. Content submission flow

```
DRAFT → SUBMITTED → IN_REVIEW → (PUBLISHED | RETURNED)
                                       ↓
                                   AUTHOR EDITS → SUBMITTED again
```

### 2.1 Submission requirements
- Title, subject, level, prerequisites, body (Markdown), at least one method.
- Author must confirm CC BY-SA license.

### 2.2 Review
- System randomly selects an **odd number** of verifiers in the relevant subject + level (default: 5). **[OPEN: scale with content complexity?]**
- Timer set based on guide length: hours for short guides, weeks for long ones.
- Majority vote = published.
- Every vote requires written justification (min ~50 chars). Silent votes are rejected and may cost the verifier their status.

### 2.3 Post-publication
- Users can upvote/downvote.
- If downvotes exceed threshold within window → automatic return to author for revision. **[OPEN: threshold + window]**

---

## 3. Verifiers

### 3.1 Becoming a verifier
- Submit application for (subject, max-level) pair.
- Take a subject-specific exam authored by current top-level verifiers + Auditors.
- Pass → become verifier at that level.
- Higher levels require either:
  - Passing every lower-level exam in that subject, OR
  - Passing one harder synoptic exam (choose one model — **[OPEN]**).

### 3.2 Cross-subject verifiers
- Allowed. Each (subject, level) is a separate qualification.

### 3.3 Loss of status
- 3 silent/unjustified votes → status revoked.
- Pattern of bad-faith voting (per Auditor review) → revoked.

---

## 4. Auditors

A small, separately elected role responsible for things voting can't decide:
- Approving new subjects.
- Resolving disputes (see §5).
- Reviewing patterns of abuse.
- Auditors are NOT editors. They do not write or vote on content.

**[OPEN: How are Auditors selected?]** Suggestion: elected by all verifiers above level 3 in any subject, for fixed terms.

---

## 5. Disputes

A user in good standing can file a dispute of type:
- `HIERARCHY_PLACEMENT` — wrong level / wrong subject
- `CONTENT_ACCURACY` — facts disputed
- `VERIFIER_CONDUCT` — bad-faith voting
- `CROSS_NICHE_CONFLICT` — two niches disagree on a shared guide
- `SUBJECT_TAXONOMY` — about the subject tree itself

### 5.1 Resolution paths
- HIERARCHY_PLACEMENT → handled by Auditor + 2 senior verifiers in subject.
- CONTENT_ACCURACY → re-vote with a fresh jury, larger size (e.g. 9).
- VERIFIER_CONDUCT → Auditor review.
- CROSS_NICHE_CONFLICT → **spin-off**: the guide is forked into two niche-specific versions. (Sebastian's idea.)
- SUBJECT_TAXONOMY → Auditor decision, public reasoning required.

### 5.2 Anti-abuse
- Max N open disputes per user at once. **[OPEN: N=?]**
- Spam pattern → dispute privileges revoked by Auditor.

---

## 6. Monetization

### 6.1 Allowed
- **Topical product links** at the bottom of guides ("Materials for this build").
- Affiliate revenue or per-click deals with vendors.
- Donations.

### 6.2 Forbidden
- Display ads, popups, interstitials, autoplay video ads.
- Sponsored guides.
- Paywalled content.
- Selling user data. Ever.
- Allowing vendors any editorial input.

### 6.3 Vendor ranking
- Star ratings per vendor + per product.
- Higher-rated items appear first.
- Strict anti-review-bot measures (rate-limit, behavioral analysis, manual review of large swings).

---

## 7. Anti-abuse, general
- All accounts require verified email + phone (or trusted-network invite) before submitting content.
- Rate-limits on submissions and disputes by account age + reputation.
- All votes, edits, and dispute outcomes are publicly auditable.
