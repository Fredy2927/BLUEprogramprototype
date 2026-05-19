# 04 — Legal & Jurisdiction (first-pass thoughts, NOT legal advice)

Sebastian was right that the legal layer matters as much as the technical layer. Some opening considerations.

## Threat model
1. **Copyright/IP claims** — a guide on "how to build an iPhone 17" attracts Apple lawyers fast.
2. **Regulated-knowledge claims** — medicine, law, firearms, drugs. "You can't teach that."
3. **Defamation/section-230-style claims** — for content users post.
4. **State pressure** — if the site becomes politically inconvenient anywhere.
5. **Funding pressure** — payment processors deplatforming you.

## Mitigations to evaluate

### Entity & jurisdiction
- A **non-profit foundation** structure makes mission lock-in legally meaningful.
- Candidate jurisdictions:
  - **Switzerland** (foundation law is strong, neutral, mature; expensive).
  - **Estonia** (e-Residency makes setup cheap, EU rules apply).
  - **Iceland** (strong free-speech tradition, IMMI-inspired).
  - **Netherlands** (Stichting; used by many open-source orgs).
- US-based 501(c)(3) is *possible* but exposes you to the most litigation.

### Content licensing
- Adopt **CC BY-SA 4.0** for all contributed content. Mirrors Wikipedia. Makes it forkable and resilient.
- Contributor License Agreement should grant the foundation enough rights to enforce, defend, and re-license downstream if needed.

### Hosting
- Don't host on AWS/GCP/Azure exclusively — single point of pressure.
- Have at least one fallback (e.g. Hetzner in Germany, OVH in France).
- Mirror static content via IPFS for censorship resistance.

### Disclaimers
- Every guide carries clear "for educational purposes; you are responsible for legal compliance in your jurisdiction" framing.
- Regulated-topic guides get an extra interstitial.

### Payment processing
- Have at least two processors lined up (Stripe + a backup like Mollie or a crypto on-ramp).
- Donations through a separately-organized fiscal sponsor reduces single-point risk.

## First concrete legal steps
1. Talk to a lawyer in your candidate jurisdiction. Budget €1k–€3k for an initial structuring memo.
2. Register the trademark for the name (or a fork name) in EU + US.
3. Draft the Contributor License Agreement.
4. Draft Terms of Service + Privacy Policy that match the non-negotiables in `00-vision.md`.

## Things you should NOT do early
- Don't take VC money. The cap-table will force exit pressure that breaks the mission.
- Don't host in a single country.
- Don't onboard verifiers in regulated fields (medicine, law) until liability framing is sorted.
