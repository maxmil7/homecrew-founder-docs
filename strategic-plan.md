# HomeCrew — Strategic Plan

## Mission
Help homeowners find trusted contractors through their personal network, and help honest contractors build recurring business through word-of-mouth.

## Exit Target
- **Minimum:** $3M
- **Target:** $10M
- **Deadline:** End of 2027
- **Most likely path:** Strategic acquisition by Nextdoor, Angi, Thumbtack, Zillow, or a home services PE roll-up

## Resources
- 1 founding engineer (Milind) — full-time, AI-assisted development
- 2–3 family/friends — up to 10 hrs/week each for ground ops, outreach, onboarding support
- 2 PM friends at big tech — periodic feedback and product gut-checks
- $2,000/month budget for infra, tools, and growth

## Key Assumptions
- AI-assisted development compresses typical build timelines by 3–5x
- Hyperlocal launch strategy (one neighborhood, then expand)
- Monetization introduced after product-market fit is confirmed, not before
- Acquisition conversations begin proactively, not reactively
- The founding team's personal network is the first user base

---

## Timeline Overview

| Quarter | Phase | Primary Goal |
|---------|-------|-------------|
| Q2 2026 (Apr–Jun) | Build | Complete MVP, finalize designs, set up infrastructure |
| Q3 2026 (Jul–Sep) | Launch & Seed | Launch in home neighborhood, saturate first area, prove retention |
| Q4 2026 (Oct–Dec) | Expand & Monetize | Expand to 3–5 neighborhoods, introduce revenue, validate unit economics |
| Q1 2027 (Jan–Mar) | Scale | Hit 10+ neighborhoods, grow revenue, build acquisition narrative |
| Q2 2027 (Apr–Jun) | Accelerate | Aggressive growth, press/visibility, begin acquirer outreach |
| Q3 2027 (Jul–Sep) | Sell | Active acquisition process, negotiate and close |
| Q4 2027 (Oct–Dec) | Buffer | Fallback window if deal slips |

---

## Q2 2026 — BUILD (April – June)

### Objective
Ship a production-ready MVP for iOS (and optionally Android via React Native / Flutter). Complete all Figma screens. Stand up backend infrastructure.

### Key Decisions to Make This Quarter
- Native iOS vs. cross-platform (React Native / Flutter) — affects speed and reach
- Backend stack (Node + Postgres? Firebase/Supabase for speed?)
- Contact sync approach (privacy-first, clear permissions)
- Monetization model to design for (pro subscriptions, lead gen, or both)

### Milestone Metrics
- Fully functional app in TestFlight / internal testing by end of June
- All core flows working: onboarding, contact sync, vouch creation, Circle browsing, profile
- 10+ internal testers (family, friends, PM contacts) actively using it

### Weekly Sprints

**Week 1 (Apr 14–18):** Project setup and architecture decisions. Set up repo, CI/CD pipeline, choose tech stack. Define data model (users, pros, vouches, circles, neighborhoods). Set up backend infrastructure within budget. Decision: native vs cross-platform.

**Week 2 (Apr 21–25):** Auth and onboarding flow. Phone auth (OTP), name setup, contact sync permission screen. Backend: user creation, phone verification, session management.

**Week 3 (Apr 28–May 2):** Contact sync engine. Import phone contacts, match against existing users, build circle graph. Privacy controls and permission handling. This is the core infrastructure that everything else depends on.

**Week 4 (May 5–9):** Home feed and vouch card display. Home screen with vouch feed (review-centric cards). Pull vouches from user's circle (1st circle + mutual connections). Backend: vouch retrieval queries with circle filtering.

**Week 5 (May 12–16):** Vouch creation flow. Vouch for existing pro, vouch for new pro, star rating + review text + photo upload. Backend: vouch storage, image handling, pro profile creation.

**Week 6 (May 19–23):** Circle tab (pro-centric). All view with category filter chips showing pro cards (Option A from our discussion). Saved view with hearted pros. Heart/save functionality.

**Week 7 (May 26–30):** Ledger and profile. Ledger screen (maintenance log entries). Profile screen with edit profile, settings, saved pros. Basic push notification infrastructure.

**Week 8 (Jun 2–6):** Pro profile and search. Pro profile detail screen with all vouches, contact actions (call/text). Search with autocomplete, search results, no results state.

**Week 9 (Jun 9–13):** Polish and edge cases. Empty states for all screens (no vouches yet, no circle, no saved pros). Error handling, loading states, offline behavior. Onboarding tutorial or first-use guidance.

**Week 10 (Jun 16–20):** Internal testing round 1. Deploy to TestFlight. Get all 10+ internal testers on the app. Collect feedback aggressively — daily check-ins with testers. Bug fixes and UX improvements based on feedback.

**Week 11 (Jun 23–27):** Internal testing round 2 and fixes. Address all critical feedback from round 1. Performance optimization, crash fixes. Prepare App Store listing, screenshots, description.

**Week 12 (Jun 30):** App Store submission. Submit to App Store review. Prepare launch materials (simple landing page, social accounts). Finalize launch neighborhood strategy.

---

## Q3 2026 — LAUNCH & SEED (July – September)

### Objective
Launch publicly. Saturate your home neighborhood. Prove that the network effect works at micro scale. Establish retention benchmarks.

### Key Insight
Do not try to grow broadly. Go absurdly deep in one neighborhood. If you can get 50 households and 30 vouched pros in Willow Glen (or wherever you are), you have a working product. If you can't, you need to iterate before expanding.

### Initial Launch Playbook (density-first)

One consolidated reference for what "launch" actually requires. Everything targets **one neighborhood** — Willow Glen, or wherever the founder's own graph is densest. Breadth is the enemy; depth is the product.

**Definition of done.** Floor (working product — see Milestone Metrics below): **50 households, 30 pros with ≥1 review, week-4 retention > 25%, ≥3 unaided hires** (someone found and hired a pro through the app with zero founder intervention). Campaign headline target: **100 organic reviews** in the neighborhood. ("Vouch" is the internal name; the UI says *review*.)

**Pre-launch checklist** (detail in `IMPLEMENTATION.md`): App-Store-approved build · `safety@homecrew.app` monitored · ToS + Privacy live at stable URLs, ToS containing the no-objectionable-content clause the phone screens already point at · PostHog `_attempted/_succeeded/_failed` funnels firing · contact-sync exercised on a **physical device** (the activation hero — untestable in the simulator) · 5–10 TestFlight testers recruited *before* the build is ready · **you personally seed 5–10 reviews** for pros you've actually used, so the first real user never lands on an empty app.

**The 100-review local campaign.** The point is reviews created *organically*, not by you — track organic-vs-seeded from day one; only the organic count moves us toward the gate.
- *Channels:* door-to-door on the target streets · the neighborhood's Facebook / Nextdoor groups · a 5-minute slot at an HOA meeting · contractor outreach — visit local pros and get them to ask happy clients to add them.
- *The ask is 60 seconds:* "Add one plumber or electrician you'd actually recommend." Anything longer dies on a doorstep.
- *Team:* helper 1 runs neighborhood outreach · helper 2 sits with people through signup + first review + contact-sync · helper 3 works the contractor side (mirrors Team Deployment below).

**Mom Test discovery — run it *during* launch, not after.** Ten real conversations a week, phone or in person, never a survey.
- *Who:* homeowners who recently needed a pro · users who signed up but never reviewed · churned users · your power users.
- *Rules (from The Mom Test):* ask about **past behavior and money already spent** ("how did you find your last plumber? what did it cost you in time and stress?") — never "would you use this?" Compliments and hypotheticals are noise.
- *Signal that counts:* they searched for / paid for / asked a friend about a contractor in the last few months — and, the only retention signal that matters, they came back to the app unprompted.

**Retention without notifications.** v1 ships **no push, no digests, no badges** (`V1.md` Principle 9). Retention must come from the product being genuinely useful at the moment of need, the **ledger** giving a reason to return, and the **invite-a-neighbor** word-of-mouth loop — not re-engagement pings. Any tactic that assumes push is out of scope for v1.

**Go / no-go gate (end of seed window).** One question: *are people finding and hiring pros through the app without the founder's intervention?* **Yes →** systematize the playbook and repeat it in neighborhood 2 — never launch wide. **No →** fix the loop first; expanding a broken loop only multiplies the problem.

### Milestone Metrics
- 100+ registered users in primary neighborhood by end of July
- 50+ vouches created organically (not seeded by you) by end of August
- 30+ pros with at least 1 vouch
- Week-4 retention > 25% (users who return in week 4 after signup)
- At least 3 "magic moments" — users who found and hired a pro through the app

### Team Deployment
- Milind: Engineering, bug fixes, analytics, iteration
- Friend/family 1: Door-to-door and local Facebook/Nextdoor group outreach in target neighborhood
- Friend/family 2: Onboarding support — help people sign up, create first vouch, sync contacts
- Friend/family 3: Contractor outreach — visit local pros, explain the app, encourage them to ask clients for vouches

### Weekly Sprints

**Week 13 (Jul 6–10):** Launch week. App goes live on App Store. Personal outreach blitz — Milind + all helpers reach out to every neighbor, friend, and family member. Target: 30 signups in first week. Seed 5–10 vouches yourself for local pros you've personally used.

**Week 14 (Jul 13–17):** Onboarding optimization. Watch where users drop off in the signup flow. Fix friction points. Implement basic analytics (Mixpanel/Amplitude free tier or PostHog self-hosted). Target: 50 cumulative signups.

**Week 15 (Jul 20–24):** Content seeding and activation. Identify users who signed up but haven't vouched. Personal outreach: "Have you used a good plumber/electrician? Add them!" Run a small neighborhood event or ask to present at an HOA meeting. Target: 20+ organic vouches.

**Week 16 (Jul 27–31):** First retention check. Analyze: who's coming back and why? Who churned and why? Talk to 10 users directly — phone calls, not surveys. Iterate on the product based on what you learn.

**Week 17 (Aug 4–8):** Push notifications and re-engagement. "Your neighbor Elena just vouched for a great painter" style notifications. Weekly digest: "3 new pros were added to your Circle this week." These are critical for retention in a low-frequency category.

**Week 18 (Aug 11–15):** Referral mechanics. Add "Invite a Neighbor" flow — make it dead simple. Track referral chains. Identify your power users (most vouches, most invites) and talk to them. Target: 100+ cumulative users.

**Week 19 (Aug 18–22):** Pro-side experience. Do pros know they've been vouched for? Build a simple pro notification/claim flow. A pro who claims their profile and sees their vouches becomes an evangelist. Target: 10 pros who've claimed profiles.

**Week 20 (Aug 25–29):** Second neighborhood seeding. If primary neighborhood metrics look good, identify the next neighborhood. Don't launch yet — just do research. Which adjacent area has the most existing connections? Where do your current users' contacts live?

**Week 21 (Sep 1–5):** Product iteration sprint. Incorporate all learnings from 2 months of real usage. What feature do users ask for most? What's the #1 reason people don't vouch? Build the highest-impact improvement.

**Week 22 (Sep 8–12):** Analytics and story building. Set up a dashboard tracking key metrics: WAU, vouches/week, pros added/week, retention curves. Start documenting the growth story — you'll need this for acquirers later. Write a "what we've learned" internal memo.

**Week 23 (Sep 15–19):** Expansion prep. Prepare playbook for launching in a new neighborhood. What worked in neighborhood 1? What was wasted effort? Systematize the launch process so helpers can run it. Target: playbook document ready.

**Week 24 (Sep 22–26):** Quarter review and planning. Honest assessment: do we have product-market fit? Key question: are users finding and hiring pros through the app WITHOUT your personal intervention? If yes: proceed to Q4 expansion plan. If no: identify what needs to change and plan an iteration sprint before expanding.

---

## Q4 2026 — EXPAND & MONETIZE (October – December)

### Objective
Expand to 3–5 neighborhoods. Introduce monetization. Validate that the model works in places where you don't personally know everyone.

### Milestone Metrics
- 500+ registered users across 3–5 neighborhoods
- 200+ total vouches
- 100+ pros with at least 1 vouch
- First revenue (even $100/month matters — it proves willingness to pay)
- Retention holding steady as you expand (not declining)

### Pricing principle — SETTLED Sept 27 2026

**Charge only against value the app has already demonstrated, at no more than a tenth of it.** A pro who can see HomeCrew earned them $50 will pay $5; a homeowner who can see it saved them $50 will pay $5.

1. **Proof before paywall.** Both sides are free until the app can put a *checkable* number in front of them — "HomeCrew sent you 6 calls this month," "your friends paid $140 less for this than your quote," "$3,200 of cost basis on file." The ask arrives attached to that number, never to a feature list. Checkable beats estimated.
2. **The Logbook and Pay confirms are the monetization instrument.** Earnings and savings are only provable if the app knows a job happened and what it cost. A Pay confirm (Venmo deep link → "Paid Joe $120?") is the strong signal, and the same tap fills the homeowner's ledger. Payments are on the monetization critical path, not a side feature.

**Never for sale, on either side:** ranking or placement; a "verified" or trust badge (license verification is a user-safety feature — free and universal); access to the friend graph.

**What pros pay for:** the earned relationship — claimed profile, replying, a calendar, getting paid through the app, and for recurring trades HomeCrew as their accounts-receivable. The best pros are booked and don't need leads; every one of them hates chasing payment.

**What homeowners pay for (Plus):** the private side only — the household's file: receipts and cost basis, warranty and maintenance reminders, "is this quote fair?", recurring-service roster with auto-pay and vacation hold, household sharing. Recommendations stay free forever. Extends to cars and care providers when non-home categories ship.

**Pricing shape when the time comes:** one plan per side, priced boringly ($3–5/mo homeowner, low tens for pros), year priced to make tax time the buying moment. Sequence: Venmo deep link + confirm first, Stripe Connect when recurring relationships have density, never before.

> Supersedes the earlier "Monetization Options to Test" (pro subscriptions with badge + search priority, per-lead pricing, featured placement). Each sold the thing the free side depends on.

### Weekly Sprints

**Week 25 (Oct 1–3):** Launch neighborhood 2. Use the playbook from Q3. Deploy a helper to this neighborhood. Target: 20 signups in first week.

**Week 26 (Oct 6–10):** Launch neighborhood 3. Parallel launch while neighborhood 2 is still growing. Start seeing if the playbook works without Milind's direct involvement. Target: 20 signups in neighborhood 3.

**Week 27 (Oct 13–17):** Monetization v1 build. Implement pro subscription flow — claim profile, payment (Stripe), subscriber benefits. Don't over-build. Minimum viable monetization. Target: payment infrastructure live.

**Week 28 (Oct 20–24):** Pro outreach for subscriptions. Personally visit or call the top 10 most-vouched pros. Pitch the subscription. Offer a free trial month. Target: 5 paying pro subscribers. Every conversation is also product research.

**Week 29 (Oct 27–31):** Neighborhoods 4 and 5. Continue expansion using the playbook. By now the helpers should be able to run launches semi-independently. Track: are new neighborhoods reaching the same density benchmarks as #1?

**Week 30 (Nov 3–7):** Growth mechanics iteration. What's the #1 source of new signups? Double down on it. If it's invites, improve the invite flow. If it's local Facebook groups, systematize posting. If it's word-of-mouth, figure out how to accelerate it.

**Week 31 (Nov 10–14):** Retention deep-dive. Compare retention across neighborhoods. Is neighborhood 1 (most mature) showing long-term retention? Are users who joined via invite retaining better than organic? Build a cohort analysis dashboard.

**Week 32 (Nov 17–21):** Revenue optimization. How are pro subscriptions performing? Churn? Satisfaction? Talk to every paying pro. Iterate on the subscription value prop. Target: $500/month MRR by end of November.

**Week 33 (Nov 24–28):** Thanksgiving — light week. Use downtime for strategic thinking. Write the "Home Circle growth story" document. Outline: problem, solution, traction, unit economics, vision for scale. This becomes the foundation of the acquisition pitch.

**Week 34 (Dec 1–5):** Press and visibility prep. Write a blog post or Medium article about the trust problem in home services. Set up a basic PR strategy — local press in your metro is easiest and most relevant. Target: 1 piece of local press coverage.

**Week 35 (Dec 8–12):** End-of-year metrics push. Final push to hit Q4 targets. Clean up the analytics dashboard. Prepare a one-page metrics summary: users, vouches, pros, revenue, retention, growth rate.

**Week 36 (Dec 15–19):** Year-end review and 2027 planning. Comprehensive review of everything learned since April. Decision point: are we on track for a $3M+ exit? What needs to change? Plan Q1 2027 in detail based on actual data, not projections.

**Week 37 (Dec 22–31):** Holiday break / light maintenance. Keep the app running. Respond to user issues. Rest and recharge — the next two quarters are the sprint.

---

## Q1 2027 — SCALE (January – March)

### Objective
Expand to 10+ neighborhoods (ideally across 2 metros). Grow revenue to $5K+/month MRR. Build the acquisition narrative and start getting on acquirers' radar.

### Milestone Metrics
- 2,000+ registered users across 10+ neighborhoods
- $5,000+/month MRR
- 50+ paying pro subscribers
- Coverage in at least 2 metros (proves the model isn't local-only)
- Network effect measurable: users in denser neighborhoods retain 2x+ vs sparse ones

### Key Activities Beyond Product
- Attend 1–2 industry events (home services, proptech, marketplace conferences)
- Publish thought leadership on trust-based marketplaces
- Get introduced to potential acquirers through your PM friends' networks (not to pitch yet — just to be known)
- Talk to a startup M&A advisor or broker (initial conversation, understand the process)

### Weekly Sprints

**Week 38 (Jan 5–9):** Multi-metro expansion planning. Pick metro #2. Criteria: you have at least a few contacts there, it's a homeowner-heavy suburb, not too far (you may need to visit). Research local Facebook groups, HOAs, neighborhood associations.

**Week 39 (Jan 12–16):** Scale the launch playbook. Can you launch a neighborhood without a physical helper present? Test a remote launch: local Facebook groups, targeted invites, digital-only outreach. If this works, expansion accelerates dramatically.

**Week 40 (Jan 19–23):** Monetization model 2. Based on Q4 learnings, introduce the second revenue stream (likely lead gen or featured placement). A/B test pricing. Target: 2 active revenue streams.

**Week 41 (Jan 26–30):** Metro 2 launch. Launch first 2 neighborhoods in the second metro simultaneously. Apply all playbook learnings. Track: does the model work somewhere you don't live?

**Week 42 (Feb 2–6):** Scalable acquisition channels. Organic growth is great but slow. Experiment with low-cost paid channels: targeted Facebook/Instagram ads ($200–300/month test), Google Ads for "[city] contractor recommendations" ($200–300/month test). Track CAC rigorously.

**Week 43 (Feb 9–13):** Pro-side growth. Happy pros are your best growth channel. Build a "share your profile" feature for pros — they text it to clients who then join to vouch. This inverts the growth model: pros bring users, not just users bringing pros.

**Week 44 (Feb 16–20):** Acquisition narrative document. Write the pitch deck (even as a markdown doc for now). Cover: market size ($500B+ home services), problem (trust deficit), solution (social proof network), traction (metrics), vision (nationwide trust layer), team. Get feedback from your PM friends.

**Week 45 (Feb 23–27):** Advisor conversations. Talk to 2–3 startup M&A advisors or brokers. Understand: what do acquirers in this space actually look for? What metrics matter most? What's a realistic valuation range for your traction? These conversations are free and invaluable.

**Week 46 (Mar 3–7):** Neighborhood density optimization. Not all neighborhoods are equal. Which ones reached critical mass fastest? Why? Focus expansion on neighborhoods that match the profile of your best-performing ones.

**Week 47 (Mar 10–14):** Platform hardening. At this scale, reliability matters. Performance audit, crash rate reduction, API optimization. An acquirer's technical team will do due diligence — the app needs to be solid.

**Week 48 (Mar 17–21):** Revenue push. Target: $5K MRR by end of March. If behind, diagnose why. Is it awareness (pros don't know about subscriptions)? Value (the subscription isn't worth it)? Pricing (too high/low)? Fix the bottleneck.

**Week 49 (Mar 24–28):** Q1 review and acquisition prep. Update the metrics deck. Finalize target acquirer list (5–10 companies). Identify warm intros to relevant people at each. Plan Q2 outreach strategy.

---

## Q2 2027 — ACCELERATE (April – June)

### Objective
Aggressive growth push. Get press coverage. Begin acquirer outreach through warm introductions. Create urgency and competitive dynamics.

### Milestone Metrics
- 5,000+ registered users across 20+ neighborhoods in 3+ metros
- $10,000+/month MRR
- 100+ paying pro subscribers
- At least 2 pieces of press coverage (local or trade)
- Active conversations with 3+ potential acquirers

### Weekly Sprints

**Weeks 50–53 (Apr):** Growth sprint. Launch 5+ new neighborhoods. Paid acquisition experiments scaled based on Q1 learnings. PR push: pitch to TechCrunch, local business press, home services trade publications. Target: 1,000 new users in April alone.

**Weeks 54–57 (May):** Acquirer outreach begins. Warm introductions to corp dev or product leads at target companies. Not "we're for sale" — instead "we've built something interesting in trust-based home services, would love to share what we're learning." Coffee chats, not pitch meetings. Let them see the metrics and draw their own conclusions.

**Weeks 58–61 (Jun):** Create competitive dynamics. Ideally 2–3 acquirers are now aware of you and interested. Share (selectively) that you're talking to others. Competitive interest is what drives price up. If no interest yet, reassess: is the product/traction not compelling enough, or is the outreach not reaching the right people?

---

## Q3 2027 — SELL (July – September)

### Objective
Convert acquirer interest into a signed deal. Navigate due diligence. Close.

### Key Activities
- Engage an M&A advisor if you haven't already (they typically take 3–5% but earn it through negotiation leverage and process management)
- Run a structured process: set a timeline, share a data room, let multiple parties bid
- Due diligence prep: clean financials, clear IP ownership, documented architecture, user metrics
- Legal: engage a startup attorney for the transaction (budget $20–50K, paid from proceeds)

### Realistic Timeline
- July: Letter of intent (LOI) from preferred acquirer
- August: Due diligence (4–6 weeks)
- September: Definitive agreement signed, deal closed

---

## Q4 2027 — BUFFER

### If the deal hasn't closed by October
- Don't panic. Most acquisitions take longer than expected.
- Continue growing the business — more traction only helps.
- If no serious acquirer interest exists by October, consider: (a) alternative buyers you haven't approached, (b) revenue-based financing or a small raise to extend runway, (c) whether the business is generating enough revenue to be valuable as an ongoing concern.

---

## Risk Register

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|------------|
| Can't achieve neighborhood density | Medium | Critical | Focus all energy on ONE neighborhood first. Don't expand until it works. Iterate on the activation playbook. |
| Low-frequency usage kills retention | High | High | Push notifications for circle activity, weekly digests, seasonal reminders ("winter is coming — is your furnace serviced?"). Make the app useful between contractor searches. |
| Pros don't pay for subscriptions | Medium | High | Test multiple monetization models. Lead gen may work better than subscriptions. Talk to pros early and often. |
| No acquirer interest at target price | Medium | Critical | Start relationships early (Q1 2027). Build press/visibility. Have multiple potential buyers. Be willing to accept $3M floor. |
| App Store rejection delays launch | Low | Medium | Follow Apple guidelines strictly. Plan for 1–2 rejection cycles in the timeline. |
| A competitor copies the model | Low | Medium | Network effects are the moat. First mover in a neighborhood wins. Execution speed matters more than the idea. |
| Burnout (solo founder, intense timeline) | High | High | Protect weekends. Lean on helpers for non-engineering work. The Q4 2027 buffer exists partly for this reason. |

---

## Decision Log

Track key decisions and their rationale here as the project progresses.

| Date | Decision | Rationale | Revisit By |
|------|----------|-----------|------------|
| Apr 2026 | Circle tab: All + Saved (both pro-centric) | Consistent mental model, category filters inline on All | — |
| Apr 2026 | Home feed: review/vouch-centric | Social discovery surface, different purpose than Circle | — |
| Apr 2026 | Target exit: $3M min, $10M target, by EOY 2027 | Personal obligations, realistic for strategic acq | Q4 2026 |
| | Tech stack: TBD | | Week 1 |
| | Monetization model: TBD | | Q4 2026 |
| | Metro 2 location: TBD | | Q1 2027 |

---

## Weekly Sprint Template

Each week follows this structure:

**Monday:** Review last week's metrics. Set the week's goals (1–2 primary, 1–2 secondary). Identify blockers.

**Tuesday–Thursday:** Heads-down execution. Engineering, outreach, or whatever the sprint calls for.

**Friday:** Ship what's ready. Update metrics dashboard. Brief retrospective: what worked, what didn't, what's next. Update this plan if priorities have shifted.

**Ongoing (daily):** Respond to user feedback within 24 hours. Monitor crash rates and errors. Check community channels (if applicable).

---

*Last updated: April 11, 2026*
*Next review: End of Q2 2026*

---

## Working notes

Folded in from the standalone `feedback.md` and `todo.md` when these docs left
the product repo — two files of a handful of lines each are easier to keep
current as sections of the plan they inform than as roots of their own.

### Feedback

1% improvement everyday

|Name|Feedback|
|-|-|
|Cade|Store private phone number of contractors|
|Rahul|MVP can include only contact sync, Seprate profile for contractos, Vercel, Supabase, Cloudflare pages, Progressive webapps, Claude context.md, claude.md, good docs for right result, Have a sprint plan, JIRA stories|
|Mom, Marcus|Can we search for other pros not related to home|

### Todo

- Telemetry
- Generate and refine all screens

**UX**
- Polishing logo
- Revisiting all images to make them consistent
- Consistent typography
- Micro animations if applicable
