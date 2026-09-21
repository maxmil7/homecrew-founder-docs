# Home Circle — Founding Partner Brief

*Last updated: Sept 1 2026*

---

# ⭐ START HERE — state as of Sept 1 2026

## Where the project actually is

**Auth is done. Roughly 15–20% of v1 is built. Design is well ahead of code.**

| Layer | Built | Missing |
|---|---|---|
| API | `auth.ts` (JWT, OTP, rate limit, allowlist, Sentry), `twilio.ts` | saves, reviews, ledger, search, profile, match hardening |
| DB | `users`, `pros`, `vouches` | `saves` migration, ledger, blocks, reports |
| Mobile | Welcome, PhoneEntry, OTP, QuickSetup + 17 components | everything after onboarding |
| Figma | `Components / V1` (`1055:2`) substantially complete | screens still iterating |

Implementation from here is mostly transcription and wiring, not design — that is why a 7-week estimate is credible.

## The live doc set

Everything else in `docs/` is reference or archive. These five are maintained:

| Doc | Role |
|---|---|
| **`v1-build-plan.md`** | **The active plan.** Sept 1 → submission Oct 23 2026 |
| `v1-spec.md` | Product scope, surfaces, visibility rules, empty states |
| `decision-log.md` | Every decision + rationale, append-only |
| `design-system.md` | Figma reference, components, tokens |
| `arch.md` | System design, saves schema, client write model |

Archived Sept 1 to `docs/archive/`: `v1-drift-fix-spec.md`, `v1-migration-map.md`, `v1-legacy-salvage-map.md`, `v1-alignment-reconciliation.md`, `sketch-inventory-map.md`, `screens.md`. Superseded: `v1-day-by-day-plan.md`, the V1 Sprint Plan in `sprint-tracker.md`.

## Two open blockers

1. **Save visibility — list-level vs per-save.** `v1-spec.md` (May 25) specifies per-save with three levels and the Figma `ProCard` has a per-pro control; the Aug schema work and the current copy are list-level with two. **This blocks the Phase 0 D1 migration.** See `v1-spec.md` §Save + Review Visibility Rules.
2. **CSAM hash-matching + NCMEC reporting.** Rekognition classifies nudity/violence but does not hash-match known CSAM or discharge 18 U.S.C. 2258A. Recommendation in `v1-build-plan.md` §7. Blocks Phase 3 close.

*(Image moderation itself is **not** open — Rekognition + Supabase Storage was decided May 12 2026.)*

## What happened Sept 1 2026

- Built the error primitives in Figma: `Toast tone`, `EmptyState tone=offline`, `Button state=loading`, `Button / disabled`, `Spinner`, `EmptyIcon / offline`
- Settled the full write model — queue scope, loading semantics, the three exits, timeouts
- Fixed a live bug: the Saved empty state was rendering "＋ Find a contractor"
- Rescaled all seven opacity tokens to percentage units (`-pct`)
- Re-based the plan from a calendar that expired Jun 30, archived six docs, corrected the brief's stack line (it claimed FastAPI + Clerk + Cloudflare R2 — none of which are used)

---

## What happened this session (May 16, 2026)

### Day 6 design queue — fully shipped

Heavy session. All five Day 6 Figma atoms built and placed in `Atoms / V1` on the `Components / V1` page, every fill bound to tokens from the `Tokens / V1` collection (54 color variables discovered + adopted). Atom inventory at end of session:

| Atom | Node | Variants | Tokens bound |
|---|---|---|---|
| Toast | `1198:47` | 4 (Success/Error/Info/Compact) | 10 |
| Input | `1211:17` | 5 state (Default/Focused/Error/Disabled/Filled) | 11 |
| FormField | `1215:68` | 10 (5 states × 2 required) | 12 |
| ProCard | `1227:90` | 3 tier (1stCircle/MutualFriend/Neighbor) | 11 |
| LocationInput | `1232:16` | 2 state (Idle/Loading) | 8 |

### Token system discovery + retrofit

`get_variable_defs` on the original Toast gallery returned `{}` — sleek.design's generated mockups used raw Slate/Plus-Jakarta hex everywhere. Audited the file's `Tokens / V1` collection (54 color tokens, `colors/primitives/*` raw + `colors/semantic/*` aliased). Toast atom was retrofitted token-by-token after Milind caught a raw-hex icon I'd missed. Subtle Figma API gotcha learned: `figma.variables.setBoundVariableForPaint()` resets paint `opacity` to 1 — must re-apply opacity after binding for fills like `border-subtle @ 6%` or the Undo button `white @ 12%`.

### Destructive red token alignment

Pre-session, two reds coexisted in the file: token `red-500 = #D14B47` (aliased by `action-destructive`) and raw `#C25E5E` (used by Vouch Delete Confirm Sheet `842:2` and Delete Account Confirm Sheet `785:12`, plus my initial Toast Error work). Milind chose to align everything to the token. Toast Error icon flipped from `#C25E5E` to `action-destructive` (slightly brighter, more saturated red); Input/FormField error states already bound correctly via Milind's draft work; destructive sheets retrofitted in a clean sweep (6 fills bound across the 2 sheets). Visual delta is minor but the system is now unified.

### Quick Setup screen — polished, composed from atoms, promoted to canonical

Milind drafted Quick Setup at `1168:2` inside the `Day 6 — For Review` working canvas. Polish pass found tokens 100% already bound (cleanest draft of the session). Only drifts: Name→Zip spacing 23→24 (`space-2xl`) and back chevron stroke from `forest-700` primitive to `text-brand` semantic.

Then refactored to compose entirely from atoms: Name field uses FormField `state=Focused, required=true`, Zip field uses FormField `state=Filled, required=true` (new variant created today). Required adding a `Filled` state to Input atom (5th variant — default border styling + filled text styling) and a `state=Filled` to FormField (10 variants total). Accepted layout shift: Name field grows from 89h → 121h (consistent reserved helper-text slot across all FormField variants).

Hero copy rewritten: `"What should we call you?"` → `"Tell us about you"`, subtitle to `"We use your name on vouches and your zip to find pros near you."` This removed the four-affordance redundancy on the Name field (hero + subtitle + label + placeholder all said "type your name"). With screen-level framing, both field labels earn their place.

Quick Setup then moved out of `Day 6 — For Review` and into `Canonical Screens / V1` at `(1701, 92)` — 4th canonical screen alongside Welcome / Phone Entry / OTP Entry. Reads left-to-right as the onboarding flow.

### Quick Setup architecture decisions locked

**Routing branch:** new users see Quick Setup; returning users skip entirely. After OTP verify, backend returns `{ jwt, userId, profileComplete: bool, missingFields?: [], ipZip?, ipCity? }`. Client routes by `profileComplete`. Phone-switch case (user reinstalls, signs in fresh) is the load-bearing reason — forcing returning users to re-enter name + zip is broken UX.

**IP-prefill via Cloudflare:** Quick Setup zip pre-fills from `request.cf.postalCode` returned in the OTP verify response. Helper text `"Looks like {city} — we'll show pros near you"` confirms IP-derived city. Covers 80%+ of users before they even see the screen. User can correct via typing OR via LocationInput's GPS icon (Day 8 wiring).

**Welcome-back morph** noted as a future polish: returning-user OTP success state could read `"Welcome back, [Name]"` with stored name retrieved via the same `/auth/otp/verify` response. ~15min Figma work; not blocking v1.

### LocationInput molecular + permission-on-intent canonical pattern

Built `LocationInput` (`1232:16`) as a separate molecular wrapping an Input atom instance + a GPS icon button on the right edge. Two state variants: `Idle` (icon at `text-quaternary`, awaiting tap) and `Loading` (icon at `action-primary`, geolocation in flight). Icons sourced from existing canonical icons in the file — `1069:3` pin-icon from LocationBadge for Idle, `1:437` location vector from Search header for Loading (per Milind's direction after my first attempt at custom-drawn ellipses didn't match the system).

**Permission-on-intent pattern locked as canonical** for both Contacts (already locked) AND Location. No native OS prompt fires on screen mount; prompt only fires when user explicitly taps the GPS icon. Same pattern that powers the Privacy Firewall / Soft Auth architecture. Cognitive friction disappears because the user initiated the ask.

Day 8 plan updated to add a shared `useGeolocation()` hook as the single primitive consumed by both LocationBadge GPS button AND LocationInput GPS icon — one source of truth for the permission/loading/reverse-geocode lifecycle. Avoids two parallel implementations.

### Input atom — extended to 5 states, plus name unification

Milind's draft `Input — error state variant` (`1166:2`) was already well-bound to tokens. Built the canonical Input atom with 4 states from his draft (Default, Focused, Error, Disabled), then added the 5th state (`Filled` = default border + filled-text styling) when Quick Setup needed a "field has value but isn't focused" rendering. Genericized default placeholder text on the atom from `"Phone number"` / `"(415) 555-2671"` to `"Placeholder"` / `"Sample input"` — the prior values read as prescriptive (designers might assume the atom was phone-specific).

Also clarified the label/placeholder pattern question Milind raised: we use **two patterns by context** — placeholder-as-label for sheet-context single-field surfaces (Auth Sheet phone input, OTP sheet), and fixed-top labels via FormField for multi-field forms (Quick Setup, future Edit Profile). Floating Material labels explicitly rejected — iOS-first MVP doesn't get value worth the implementation complexity, and they break the required-asterisk affordance.

### ProCard naming, structure, and adoption pass

Milind questioned my initial name `ProProfileCard` — the card surfaces a pro but isn't the Pro Profile page (that's `1:705`/`798:2`). Discussed `ProVouchCard` (rejected — card is pro-led not vouch-led, breaks when there's no vouch attribution) vs. `ProCard` (cleanest, scales to any context). Locked on **ProCard**.

Built the atom from the locked canonical pattern (`1:1430 → 1:1524` for 1stCircle, `1:1557` for MutualFriend, `1:1591` for Neighbor) rather than the alternate `ProProfileCard / simplest` 100h draft at `1168:27` — the rich 148h canonical is what every pro-discovery surface actually consumes; the 100h compact form has no home in v1.

**Adoption pass executed end-of-session:** all 10 inline pro cards across the 4 Home screens (`1:1430`, `284:2`, `1:1209`, `21:219`) replaced with ProCard atom instances. Text overrides applied for pro name / location / pill / rating / attribution. Image hashes preserved per source (`b2aa7bdd...` for Flow State Plumbing logo, `649732a0...` for Roots Gardening, `67929ff8...` for Bright Arc Electrical, plus voucher avatar hashes). Future ProCard changes now propagate to all 4 Home screens automatically. Minor known regression: mixed-font bold range on overridden attribution text (the bolded "Willow Glen" on 4 Neighbor cards) was flattened by Figma's `characters =` setter — 5-min fix later via `setRangeFontName` if we want.

### Other product threads landed

- **Input success state** asked + rejected for v1. Symmetry-with-error isn't sufficient justification; revisit when we have a real use case (username availability check, async email verification, real-time field validation). Lock the Input atom on 4 states (Default/Focused/Error/Disabled) for v1 + the Filled state added for Quick Setup.
- **Name field optional vs required** considered for Quick Setup. Went with required (option A). Revisit if conversion data shows abandonment at Quick Setup is meaningfully higher than baseline.
- **Derive name from phone contact card?** Considered. Technically possible on iOS (Contacts framework `unifiedMeContact`, ~60% coverage) and Android (`ContactsContract.Profile`, ~50% coverage) but requires Contacts permission — which we explicitly defer until post-onboarding via the Privacy Firewall pattern. Permission timing argument trumps the auto-fill convenience. Plus per Apr 25 decision, the stored name is functionally invisible to non-contacts anyway. Name capture stays manual on Quick Setup.
- **Day 6 — For Review canvas** (`1164:2`) now serves as an audit-reference archive — all the draft contents have either been atomized (Toast, Input, FormField, LocationInput) or promoted to canonical (Quick Setup). The `ProProfileCard / simplest` draft stays as an archived alternate form factor that we may revisit.

### Day-by-day plan updates

`docs/v1-day-by-day-plan.md` edited in three places:
- Day 6 Error UI primitives bullet list now includes LocationInput as a Figma-only deliverable
- Day 6 Quick Setup section now describes the FormField + LocationInput composition + IP-prefill mechanism + returning-user routing branch
- Day 8 renamed from `Google Places integration + LocationBadge` to `Google Places integration + LocationBadge + LocationInput GPS wiring`. Added the shared `useGeolocation()` hook as a morning deliverable; wire-up of LocationInput in Quick Setup zip field lives in the evening section alongside LocationBadge. Also added the previously-denied-permission error matrix row (iOS only prompts once per install — need "Open Settings" toast fallback).

### Doc state going forward

- **founding-partner-brief.md** — this session entry
- **decision-log.md** — 15 new decision rows appended for May 16
- **sprint-tracker.md** — Day 6 status updated to "design complete; remaining is code+config"
- **design-system.md** — **NOT updated.** Per the locked rule, that doc only gets updated when Milind explicitly says "this is final, write the canonical spec." The new atoms, the destructive-red token alignment, the LocationInput pattern, the Quick Setup architecture — all worthy of documentation, all queued for sign-off.

### Where we are end of session

Day 6 design queue is **fully complete**. Five atoms shipped + token-bound, one screen polished and promoted to canonical, 10 inline cards across 4 home screens adopted to the new ProCard atom, destructive sheets retrofitted to the unified red token, 4 canonical screens now in `Canonical Screens / V1` (Welcome → Phone Entry → OTP Entry → Quick Setup reading left-to-right as the onboarding flow). What's left on Day 6 is all code or config (Day 5 OTP retrofit, `analytics.captureFailure`, mid-OTP SecureStore resume, alert guardrails, Quick Setup signup wiring, ProCard code implementation, Definition-of-Done sweep).

Open thread: **design-system.md update** when Milind says go. Plus minor polish: re-bold the "Willow Glen" range on the 4 Neighbor cards where override flattened mixed-font styling.



## What happened this session (May 7, 2026)

### Phase 1 Home card scaling — COMPLETE across all 4 Home screens

Built MUTUAL FRIEND variant on `1:1430 → 1:1557` (Marcus Chen → Roots Gardening, light-green outlined badge, single bridge avatar, "Vouched by a friend of **Marcus C.**"). Built NEIGHBOR variant on `1:1430 → 1:1591` (anonymized → Bright Arc Electrical, NEIGHBOR outlined badge, no avatars, "Vouched by 2 neighbors in **Willow Glen**"). Sibling spacing tightened to 12px gaps (148h cards). Then batch-applied the 3-tier pattern across the other 3 Home screens:

- `284:2` Home Auth Fully Onboarded — full 3-tier set
- `1:1209` Home Auth No Contacts — 2 NEIGHBOR cards
- `21:219` Home Guest — 2 NEIGHBOR cards

Total: 10 cards across 4 Home screens, all using canonical pro-led pattern with consistent tier-degraded visual treatment (filled badge → outlined → outlined-tinted; 2 avatars → 1 avatar → 0 avatars; named voucher → bridge name → location anchor in bold).

Hit ancestor-clip issue during scaling: the `1:1431`-equivalent inner container on each screen was clipping the bottom card when card heights shrunk from 162h to 148h. Temporarily bumped multiple ancestors for screenshot verification — captured in task #33 for revert decision (Option A: revert to original sizes and accept that 3rd card scrolls into view per original design intent; Option B: keep extended sizes).

### Pill / location order swap on canonical card pattern

Side-by-side comparison via cloned screen `921:4` revealed: location text first, category pill second reads better than original pill-then-location-with-separator. Locked the swap across all 3 cards on `1:1430` and propagated to other Home screens. Reasoning: (1) mirrors title row's text-then-badge rhythm; (2) consistent with Circle All v2 (`554:355`); (3) pill becomes a clean visual punctuation rather than a heavy left anchor; (4) reading flows naturally as a phrase. "•" separator dropped — pill's distinct fill provides visual separation.

### Pro Profile claimed (`1:705`) retrofit — DONE

Cross-surface consistency fix (May 3 followup item). Applied 2-card Call/Text + inline bookmark pattern matching Pro Profile Unclaimed (`798:2`):
- Save card `1:758`: hidden (preserved invisible, restorable)
- Call `1:742` + Text `1:750`: widened 120w → 187w, internals recentered (icon at x=83.5, label at x=78.5/79, lock badge at x=167)
- Text repositioned to x=195 (8px gap from Call)
- Bookmark Button cloned from `798:2 → 838:2` placed at body-(370, 254) on `1:706`
- All other content (avatar + verified badge, pro name, pills, rating, location, voucher row, About, Vouches) preserved untouched

Both pro profile variants (claimed + unclaimed) now use identical canonical action-row pattern. Task #2 marked complete.

### Major product discussion: Save / Vouch decoupling

Long discussion that landed a meaningful product expansion. Captured fully in task #34 for Phase 1.7 / Phase 2 implementation. Key decisions:

**Unified add-flow with optional rating/review.** Rating + review become OPTIONAL in the existing Vouch flow (`1:2411` + `1:2465`). User fills both → public Vouch (tier badge + "Vouched by Name"). User skips → private Save (same tier badge + "Saved by Name"). Single flow, two outcomes. Solves multiple user pain points: "I want to remember this pro without publicly endorsing them," "I want friends to discover my list without per-pro vouching," "lower-friction save creation."

**Tier badge semantics decoupled from endorsement.** Two-axis trust signal: badge = network proximity ("how close"), attribution = endorsement strength ("how strong"). Same 1ST CIRCLE badge can carry "Vouched by Sarah J." or "Saved by Marcus C." — different strengths, same proximity. Sort weight within tier: vouches outrank saves (Vouched multi > Vouched single > Saved multi > Saved single).

**Vouches always Neighborhood (public review).** Matches Yelp/Google/Angi convention. No visibility toggle on vouches — keeps the UI clean. Users who want "private review to my circle" express that via Save (without rating/review).

**Save visibility: per-pro selector at save time, Circle default hardcoded, no list-level state.** Simpler than initially proposed. Single source of truth per save record. 3-option selector (Private / Circle / Neighborhood) in add-flow. No list-level toggle (redundant with per-pro state). No user-level default preference setting (YAGNI for MVP — if defaults need to change at scale, change the hardcoded default in a backend deploy). v1.5 may revisit if behavioral data demands.

**Save → Vouch graduation:** explicit visibility-shift messaging. At-time-of-add: submit button label changes based on review-filled state ("Save to your list" / "Save & Vouch" with subtitles communicating visibility). Later graduation: explicit prompt "Vouches are public reviews — visible to neighborhood. Continue?"

**Three entry points** for "Add a Pro": heart on existing pro card (existing); FAB action sheet renamed "Vouch for a Pro" → "Add a Pro" (`1:1942`); Saved tab header "+ Add" button (post-#27 restructure).

**Profile surface:** merge Profile → My Vouches (`746:2`) into unified "My Pros" sub-page with filter chip (All / Vouched / Saved). Becomes canonical "Sarah's pro list" friends can browse.

### Open thread — bookmark/save prominence

Late in session, Milind questioned whether the bookmark icon on Pro Profile is prominent enough. Currently 44×44 white circle with subtle bookmark glyph — could be missed. Captured as task #35 to revisit later (Milind asked to park for evening). Decision after looking at prominence variants side-by-side.

### Where we are at end of session

Phase 1 Home card work is complete (canonical pattern locked + scaled to all 4 Home screens + Pro Profile claimed retrofit). The major outstanding architectural items are: #27 4-tab restructure (mechanical bottom-nav rename + Circle screens deprecation), #34 Save/Vouch decoupling (Phase 1.7 product expansion), and the smaller polish items (#15 pagination dots, #17 exit routing doc, #29 Vouch CTA on Pro Profile, #30 user-pro relationship indicators, #31 Profile cleanup). Plus task #33 revert (ancestor frame heights) needs decision.



## What happened this session (May 6, 2026)

### Major product re-architecture: 4-tab Home / Saved / Ledger / Profile

The biggest shift this session. The original 4-tab structure had a Circle tab with All + Saved sub-tabs. Audit revealed Home and Circle/All were 80% the same surface — both surfaced pros vouched by your network, only sort + card structure differed. Two tabs for "different sorts of the same data" is hard to defend. After deep competitive analysis (Yelp, Angi, Thumbtack, Houzz, Pinterest, Spotify, Zillow, Airbnb), restructured into:

- **Home** — unified pro-discovery (search + sortable directory + filter chips, pro-led cards)
- **Saved** — own top-nav tab, single-purpose retrieval surface for saved pros (heart icon)
- **Ledger** — unchanged (Pillar 2 home maintenance, top-nav presence signals strategic priority)
- **Profile** — unchanged

Differentiation between Home and Saved is content-source, not card-structure: Home = pros vouched by your network (broad, sortable); Saved = pros YOU've curated (narrow). Captured as task #27. Affects ~12-15 currently-designed Circle screens (deprecated as standalone, card patterns repurposed onto Home/Saved/Search Results).

### Two debated sub-tab proposals on Saved — both killed

(1) **Friends sub-tab** showing connected contacts on the app — would dilute Saved's high-frequency retrieval job for unproven Path-B people-first hypothesis; at MVP network density (100-500 users in South Bay), Friends view would be near-empty creating bad first impression.

(2) **Vouched sub-tab** showing pros user has vouched for — frequency mismatch (saving = high-frequency intent, vouching = low-frequency action history); My Vouches already has appropriate placement at Profile sub-page (`746:2`); permanent UI chrome on Saved would tax 95% of users for 5% who care.

Both rejected. Where vouched-status SHOULD show: "You vouched for this" indicator on Pro Profile pages (task #30) + checkmark on Saved cards if user also vouched. Friends/People hypothesis revisited post-launch when network density justifies and behavior data shows demand.

### Naming decisions: keep app name "Home Circle", tab name = "Saved" not "Circle"

App name retained — Circle concept lives in tier badges (1ST CIRCLE / MUTUAL FRIEND / NEIGHBOR) and attribution patterns ("a friend of Marcus C.", "a neighbor in Willow Glen"), which are stronger anchors than a tab label. Two-pillar reading reinforces the name: "Home" = maintenance + recording (Pillar 2), "Circle" = trust network (Pillar 1).

For the Saved tab specifically, considered renaming to "Circle" but went with "Saved" — competitive analysis shows Pinterest, Spotify, Zillow, Houzz, Airbnb all use simple/conventional language for saved-collection tabs. For MVP acquisition with new users, "Saved" is unambiguous and pictographically obvious paired with the heart icon. Brand "Circle" stays alive in app name where it does branding work without UI tax.

### Home leads with pros (Search Results card pattern adopted)

Per product framing — "find pros used by your friends" — Home should lead with pros, not vouchers. Adopted Search Results card pattern (`1:526 → 1:569`): pro avatar + name + tier badge + meta + attribution. Same pattern shared across Home + Saved + Search Results so visual language is consistent across pro-discovery surfaces. Drops the italic quote that voucher-led Home cards had — vestigial social-feed flavor that doesn't fit pro-discovery intent. Default sort: trust-first per Apr 24 sort lock. NO section headers on Home for MVP (badges + sort order convey grouping; networks too sparse for sections to earn their keep).

### MVP scope cuts to sharpen the value prop

Three concrete cuts:

1. **"Popular in Neighborhood" section** on Home — cut. Dilutes trust-through-network value prop with generic popularity ranking (every contractor app has this); at MVP scale popularity signal is thin. Add post-launch if trust-tier feed engagement is low.
2. **Push and email notifications** — cut entirely. Sparse network at launch = empty notifications = bad signal; notification fatigue happens fast pre-value; 2-4 days engineering each that doesn't move core value prop. Eliminates Push Notification Priming screen (was task #5, now deleted). Add post-launch v1.1 when network density supports meaningful events.
3. **Two Profile sub-pages** — Saved Pros (`746:60`, already deferred) and Notification Settings (`746:176`) — both formally killed. Profile shrinks 8 rows → 6 (Edit Profile, My Vouches, Connected Contacts, Privacy, Help & Support, Log Out). Captured as task #31.

Net: ~2-4 days engineering + 2-3 hours design saved, sharper value prop.

### A4 Google Places integration: two-scenario framing locked

**Scenario 1 (new pro):** Vouch Add New Pro (`1:2465`) restructured with Google Places search as primary input; manual entry only as fallback link AFTER Places returns zero results. For user's VERY FIRST vouch ever, manual fallback is hidden entirely — they MUST select from Places (forces canonical data on first user-generated entry, prevents downstream pro-claim mismatches).

**Scenario 2 (existing pro):** Vouch Search Autocomplete (`1:2719`) returns HC pros first, Places results second labeled as new-pro candidates. Tap HC pro → `1:2411` with pro pre-filled. Tap Places result → new pro flow with Places data pre-filled. Dedup at search time via phone number as the dedup key.

Optimization: "Vouch for [Pro Name]" CTA on Pro Profile pages (`1:705` + `798:2`) per task #29 — saves search step when user is already viewing the pro.

### Two-pillar value prop preserved through restructure

Made explicit that Home Circle's value is two-fold: (Pillar 1) trust-curated pro discovery — the launch differentiator; (Pillar 2) home maintenance + recording — the longer-term direction. The 4-tab structure preserves both: Home + Saved carry Pillar 1; Ledger is Pillar 2's dedicated surface. Cross-pillar integration already exists in designs (Log Maintenance tags a pro from your circle; Post-Vouch Prompt offers to log work to Ledger; A4 Google Places makes new-pro entry low-friction for both flows). MVP onboarding copy should mention both pillars upfront ("Find pros your friends trust. Keep a record of your home.") to anchor the dual-pillar story. Pillar 2 stays minimal at MVP (no reminders, warranty tracking, multi-property — all post-launch).

### Canonical pro-led Home card built (`1:1430 → 1:1524`)

Multiple iterations today on the canonical 1ST CIRCLE reference card:

- **Iteration 1:** Yelp-inspired voucher-led design with social header + divider + meta line. Looked cohesive but wrong direction — voucher-led contradicted "lead with pros" decision.
- **Iteration 2:** Reverted to inline pro-led structure cloning Search Results pattern. Cleaner.
- **Iteration 3:** Added Pro Profile (`1:716`)-derived enrichments — location with separator dot inline with category pill, voucher avatar stack right-aligned next to attribution.
- **Iteration 4 (locked):** Swapped rating ↔ tier badge positions. Tier badge moved to title row (paired with pro name as "this pro your circle backs"); rating moved to its own row with full 5-star expanded display (more emotional weight than compact ★ 5.0). Card height 148h.

Final structure: 60×60 pro logo + pro name + tier badge (title row) → PLUMBER pill · Willow Glen (meta) → ★★★★★ 5.0 (stars) → Vouched by + voucher avatars (vouch row).

Three iteration tweaks queued in task #32: bump star size, bump "Vouched by" text size, revisit avatar gap. Then scale to MUTUAL FRIEND (`1:1430 → 1:1557`) and NEIGHBOR (`1:1430 → 1:1591`) on the same screen, then batch-apply across the other 3 Home screens.

**Continued iteration (May 6 evening):** All three #32 tweaks landed — vouched-by text bumped 11px → 14px, vector star icons cloned from `1:1576` (Milind unified the 5th star size since the cloned 4.8 pattern had a smaller half-star inappropriate for 5.0), avatar gap resolved via Option A (single Sarah avatar inline with attribution).

After Option A, explored a cluster pattern (3 overlapping avatars on left + text following) for richer aggregate social proof. Tightened cluster overlap from 8px to 12px. Then built side-by-side comparison on cloned screen `921:4` to evaluate cluster vs the original Pro Profile-derived pattern (text left + 2-avatar stack right-aligned).

**V2 (text left + 2-avatar stack right) won** — less crowded than the cluster, text-led reading, faces as supporting visual on the right, intentional middle space reads as breathing room not gap. Added Bold weight to "Sarah J." substring within the attribution text (DM Sans Bold for "Sarah J.", DM Sans Medium for "Vouched by..." and "+ 3 others") so the named voucher pops as the visual anchor — matches Pro Profile `1:716` attribution treatment.

**Canonical 1ST CIRCLE card LOCKED** on `1:1430 → 1:1524` May 6 evening. Cloned screen `921:4` preserved in file as comparison artifact. Card height 148h. Ready to scale to MUTUAL FRIEND + NEIGHBOR variants tomorrow.

### Tier-attribution audit extended

The May 3 A6 audit found MUTUAL FRIEND attribution issues on Home cards. May 6 mapping revealed the same problem on NEIGHBOR cards: Home Pre-Contacts cards (`1:1209` + `21:219`) all use named-contact voucher format (Sarah Jenkins / David Miller) with NEIGHBOR badge — contradicts tier semantics where NEIGHBOR voucher should be anonymous. Folded into A1 (#11) since the Home redesign touches all the same cards.

### Tasks restructured to match

Heavy task list changes today: `#5` deleted (Push Priming killed), `#11` rescoped to "Search Results pattern (pro-led)", `#12 + #18 + #19` description-updated to reflect restructure dependencies, `#27` umbrella created for the 4-tab restructure, `#28` created (Yelp revert) and immediately marked done since the canonical build replaced it in place, `#29` created (Vouch CTA on Pro Profile), `#30` created (user-pro relationship indicators), `#31` created (Profile cleanup — remove Saved Pros + Notification Settings rows), `#32` created (canonical card iteration tweaks). Total active queue ~30 items across Phase 1 (Rahul UI sweep), Phase 1.5 (restructure), Phase 2 (net-new screens), Phase 3 (overlay refactor), Phase 4 (new overlay screens), Phase 5 (QA + docs), Phase 6 (prototyping).

### Process improvements

- **Nested node referencing convention** reinforced after a slip — always cite via parent frame ID (`1:1430 → 1:1524`), never bare nested ID alone. Already in `.claude/skills/chad/SKILL.md` from May 3.
- **Doc persistence pass** at session end — decision-log appended with 11 May 6 entries, founding-partner-brief updated (this section), sprint-tracker pending update.



## What happened this session (May 3, 2026)

### Vouch Delete Confirm Sheet built (`842:2`)

Cloned `785:12` Delete Account Confirm with vouch-scoped copy. Title: "Delete this vouch?" Subtitle: "Mario's Plumbing will lose your recommendation, and your circle won't see it anymore." (names both stakeholders — pro and circle — so the consequences are visible before tap.) Primary "Delete vouch" / Cancel button stack inherited as-is from `785:12`. Lives at x=2500, y=10100 in the destructive-sheets row. Wires from Vouch Detail (mine) `793:2` Delete button.

### MUTUAL FRIEND attribution audit completed (Rahul A6)

Swept all 8 MUTUAL FRIEND badge instances across the file. Found 4 cards with correct "a friend of [Name]" attribution (Circle Saved, Circle All, My Vouches, Saved Pros) and 4 issues. **Three fixed inline** today: (1) Search Results card `1:526 → 1:569` showed "Vouched by David M. + 1 other" (1ST CIRCLE pattern misapplied to MUTUAL FRIEND tier) — corrected attribution to "Vouched by a friend of David M." Required hiding "+ 1 other" and cascading container resizes to fit the longer string in the constrained row layout (card right margin tightened from 14w to 4w but stays positive). (2) Circle All card `554:321 → 554:355` had two duplicate MUTUAL FRIEND badges (`554:510` + `554:521`) at identical positions — `554:521` hidden + renamed for traceability. (3) Search Results MUTUAL FRIEND badge pill `1:526 → 1:584` was 84w but text was 83w with 6.5px left padding — text overflowed pill right edge by 5.5px (pre-existing bug, not from today's changes). Pill widened to 95w with text recentered for symmetric 6px padding both sides. **Two folded into A1**: Home cards `1:1430 → 1:1557` and `284:2 → 284:130` show the bridge person AS the voucher (with photo + name), contradicting MUTUAL FRIEND tier semantics. Resolved as part of the A1 Home card rebalance pass — see decision below.

### PM Rahul's feedback fully ironed out

A ~3-hour transcript of Rahul's product/design feedback triaged into 9 implements, 4 killed, and 5 post-MVP deferrals. Key locked decisions captured in `decision-log.md`:

- **A1 Home card hybrid rebalance** (typography only, no flip). Keep voucher-led card structure but elevate inline contractor name + add category pill so pro+job is scannable. Circle untouched.
- **A2.5 Circle category chips:** add explicit "All" chip default-active, single-select. At MVP scale data is sparse — defaulting to a category filter would land users on near-empty screens.
- **A3 Drop name + neighborhood from onboarding** — phone-only signup. Friends see you by their own contact name; non-contacts never see your name at all (mutual-friend = "a friend of [Bridge]", neighbor = "a neighbor in [city]"). So a name-less HC user is fully attributable across the social graph regardless of typed input. Two screens (`1:1083`, `1:1125`) get deprecated.
- **A4 Google Places integration** in Vouch Add New Pro is v1 scope (not v1.1). Solves both vouch-creation friction and pro-duplication problem (phone becomes dedup key). Worth the 1-2 day backend lift now to avoid cleanup later.
- **B10 Example cards** with EXAMPLE pill in `656:2` Pre-Contacts Cold Start only. Skip on guest cold-start (competes with Sign Up CTA), authenticated cold-start (depressing), and empty Saved variants (action lives elsewhere).
- **Home MUTUAL FRIEND attribution** (folded into A1): keep bridge person's avatar (his face IS the social proof), change name slot to "[Bridge]'s friend" possessive form (fits the slot, reads naturally). Asymmetric with Circle's "a friend of Marcus C." attribution but each phrasing fits its grammatical context.

### Circle stays pro-led — Saved tab is the structural anchor

Rahul proposed flipping both tabs (Home → pro-led, Circle → voucher-led). Pro-led Home gets implemented as A1. Flipping Circle is incompatible with the Saved tab — saved pros are inherently a pro-curated list, there's no coherent way to make Saved voucher-centric without breaking what saving IS. Two tabs in a segmented control with two card structures and two content types isn't a tab switcher anymore. Circle stays pro-led with All/Saved structurally aligned. Home/Circle differentiation is now content-source (broader pool, recency-sorted vs trust-tier strict), not card-structure.

### 24-item remaining design punch list with 6-phase sequencing

Audit complete. The stale "Remaining screens — Not started" row in `design-system.md` (line 188) is now deleted. Actual remaining work captured as a Design Wrap-up Punch List in `sprint-tracker.md`, broken into 6 sequenced phases:

1. **Rahul's UI sweep** (~3-4 hrs) — A1, A2.5, A5, A6 (done), A7, A8
2. **Independent screen work** (~2-3 hrs) — Pro Profile retrofit, Push Priming, B10, A3, A4 Figma updates
3. **Overlay refactor** (~3-4 hrs) — restructure ~12 sheet-style overlays into standalone components following the `823:2` / `785:308` pattern
4. **New overlay screens** (~1 hr) — Claim Phone Mismatch, Download My Data, both built directly in new component pattern
5. **QA + docs** (~2 hrs) — illustration sweep, post-MVP decision log, design-system final cleanup
6. **Prototyping** (~5-7 hrs) — smoke test → Pro claim → Search → Profile/settings → Guest → Onboarded user flow

Total ~15-20 hrs design work + 1-2 days A4 backend engineering as a parallel track.

### Prototyping plan — programmatic, 4 transition presets, 5 flows

Decided to drive Figma prototyping programmatically via `setReactionsAsync` once all screens are in their final pattern. Four reusable transition presets defined (`nav-push`, `sheet-morph`, `sheet-present`, `instant`) so all flows tag connections by preset name for consistency. Five flows specified: Pro claim (~12 reactions, smallest, warm-up template), Search (~8 reactions), Profile/settings (~15 reactions, repetitive — script wins), Guest (~25 reactions, branchy), Onboarded user (~35 reactions, biggest). Smoke test (one trivial connection) precedes the real work to verify the MCP allows `setReactionsAsync` calls.

### Process improvements

- **Top-level frame names** now have node IDs appended in parens (e.g. "Search Results (1:526)") — bulk rename of all 63 frames executed today. Search-by-ID now works in Figma's layer panel.
- **Nested node referencing convention** codified in `.claude/skills/chad/SKILL.md`: always cite via parent frame ID (e.g. `1:526 → 1:592`), never bare nested ID alone. Page reference omitted since file is single-page.
- **Doc persistence pass** done at session end: decision-log appended with 10 May 3 entries, sprint-tracker restructured with Sprint 0 expansion + Design Wrap-up Punch List, founding-partner-brief updated (this section), `design-system.md` stale row deleted.



## What happened this session (April 27, 2026)

### Out-of-Market success morph built (`712:2`)

Picked up the open thread on `668:2` (Out of Market guest screen): what is
the next state after the user enters their email and taps Notify Me?

**Decision: morph-in-place success state on the same screen** — same pattern
as `707:2` (auth success). The user feels anchored to the surface they just
acted on; no fullscreen interstitial, no toast, no separate confirmation
screen. Hardened the principle: **a confirmation state confirms the outcome
(the waitlist spot), not the input (the email).** Email is not rendered back.

**`712:2` built as a sibling node** so the two-state morph (form → success)
can be reviewed side-by-side, mirroring the `1:859`→`1:932`→`707:2` sheet
morph sequence.

What changes between `668:2` and `712:2`:
- House illustration unchanged (still scene-setting for the same place)
- Title morph: "Coming to Sacramento soon" → **"You're on the list"**
- Subtitle morph: "Drop your email and we'll let you know..." → **"We'll
  email you the moment Home Circle launches in Sacramento."** (city pulled
  from same `request.cf.city` — personalization thread survives the morph)
- Email input hidden; Notify Me button hidden
- **64×64 dark-green checkmark badge** drops in at body-(183,494) where the
  email input used to sit. Same badge spec as `707:2` so success-state
  visual language is consistent across auth + waitlist surfaces.
- **Tertiary share link** below at body-y=590: "Know a neighbor in
  Sacramento? Tell them →" — opens iOS share sheet with prefilled message.
  Turns every out-of-market signup into a potential viral seed at ~10 min
  integration cost. Optional but cheap.

**Terminal state by design**: no auto-dismiss, no Continue button, no return
path. Out-of-market guests have no Home Circle to enter yet — `712:2` *is*
their Home Circle until we launch in their city. Server checks email-on-list
status on repeat visits and renders this state directly.

**Build spec captured (edge cases)**:
- Invalid email format → inline red border on `668:2`, no morph
- Already on waitlist → same success state (don't reveal new vs repeat)
- Network failure → toast + input stays enabled, no partial morph



## Where we are

We're in **Q2 2026 — Build phase**. Design work has extended ~3 weeks past the original Sprint 0 close. As of May 3, the design audit is complete and the remaining work is fully scoped: 24 items across Rahul's-feedback implements, 4 net-new screens, an overlay refactor, prototype wiring, and final docs. Engineering sprints (2+) shift accordingly when design wraps. Tech stack (corrected Sept 1 2026 — the earlier FastAPI/Clerk/R2 line was never accurate): **React Native + Expo** (mobile), **Bun + Hono + Drizzle** (API), **Postgres + PostGIS** via Docker locally and Supabase in prod, **phone-only auth** (Twilio Verify + HS256 JWT, no Clerk), **Supabase Storage** for images (no Cloudflare R2).

## Current sprint

**Design Wrap-up Punch List (active May 3-onwards):** 24-item remaining design work organized into 6 phases — see the full mapping in `docs/sprint-tracker.md`. Phase 1 (Rahul's UI sweep) is in progress with A6 already done. Phases 2–6 sequenced for ~15-20 hours of design work + 1-2 days of A4 backend engineering as a parallel track.

**Engineering sprints (2+) deferred** until design wrap-up completes. Originally Sprint 2 (Apr 27-May 1) was Auth & Onboarding engineering; that now slips ~2-3 weeks pending design completion.

**Full Q2 sprint-by-sprint breakdown + Design Wrap-up Punch List:** See `docs/sprint-tracker.md`.

## What happened this session (April 26, 2026)

### Sign-up sheet redesigned: inline phone input + morph-in-place to OTP

Building on yesterday's phone-only decision, today we collapsed the entire phone-auth
flow onto a single sheet surface.

**Inline phone input on the sheet (`1:859` + `583:1357`):**
With OAuth gone, "Continue with Phone" was a tap that led to a fullscreen phone-entry
screen — pure indirection. Replaced the button with the input itself. The sheet now
carries:
- 366×56 white pill input with `+1` prefix (hardcoded, US-only for MVP, no country
  picker), 1×24 vertical divider, and "Phone number" placeholder. Same input
  language as the email-capture pill on `668:2`, so we have one consistent inline
  pattern across guest entry points.
- "Send Code" CTA below the input — same dark-green pill treatment as everywhere else.
- "Already have an account? Sign in" row removed. Sign-up and sign-in unified — submit
  looks up the number on the backend and routes accordingly. The user doesn't need to
  declare which path they're on.

Sheet shrank from 462h (post-OAuth-collapse) to 386h. Bottom anchored at y=932 as
always; top moved to y=546. `583:1357` got the same treatment so the in-context
Saved-tap clone stays in sync.

**OTP step happens via in-place sheet morph (`1:932` repurposed):**
After Send Code, the sheet doesn't transition — it morphs. Phone input swaps for
6 OTP boxes (50×56 white pills, gap 13.2px, first box has a centered cursor).
Send Code swaps for "Didn't get a code? Resend code (0:28)" centered. Title becomes
"Verify your number," subtitle becomes "Code sent to +1 (408) •••-••82." Sheet shape,
footer, and background-blur stay constant — the user feels anchored to the same
surface throughout.

`1:932` was previously a fullscreen OTP screen. Repurposed: hid the original fullscreen
contents (preserved as invisible nodes), cloned the `1:859` sheet shell into it, swapped
phone input for OTP boxes and Send Code for the resend row. The artifact stays useful
in a new role.

No standalone Verify button — auto-submits on the 6th digit. The "From Messages: 842910"
auto-fill chip from the old design is dropped from the in-sheet mock; iOS provides that
above the keyboard regardless of UI.

**Old fullscreen `1:1007` deprecated:**
Renamed to `[DEPRECATED] Phone Auth Number — replaced by sheet 1:859`. Added an amber
DEPRECATED banner overlay so anyone navigating to it sees the redirect. Kept in the
file (not deleted) — historical references resolve, recoverable if we ever pivot back
to multi-step auth.

### Wrong-number escape hatch + chrome cleanup on setup profile

Talked through the OTP → setup-profile transition. Two related decisions landed:

**Edit link on the OTP sheet (`1:932`):** the subtitle now reads `Code sent to +1
(408) •••-••82  ·  Edit` with "Edit" as a tappable dark-green link. Tapping morphs
the sheet back to phone-input state — bidirectional sheet morph, not a back-stack
pop. This is the meaningful escape for the most common error case (mistyped number)
and it sits at the step where the user can actually see the wrong number.

**No back button + no 3-dot indicator on setup profile (`1:1083` + `1:1125`):**
once the user is past OTP, the account exists; a "back" tap has nowhere coherent to
go (returning to OTP forces re-verify, returning to phone contradicts the verify
just completed). Removed. The Edit on OTP is the only safety net we need — placed
at the right step instead of being scattered across post-auth screens.

The 3-dot progress indicator went too. With phone + OTP collapsed onto the sheet,
the post-auth flow is 2 fullscreen steps; three dots overstate it, two dots feel
paltry, and at this length the user isn't lost. Setup profile screens now read as
focused single-task surfaces with no header chrome competing for attention.

Future build spec captured in decision log: **OTP → setup-profile transition** uses
a ~700ms micro-success state on the sheet (boxes flip dark green, title morphs to
"You're in!" with a checkmark) before dismissing. Hides the network latency naturally
and acknowledges the milestone without burning a tap on a separate "Account Created"
interstitial.

### Success micro-state visualized (`707:2`)

Followed up by building the success state as a static Figma frame — clone of
`1:932`, then iterated based on a simple question: do we need to show the
entered code back to the user?

**First pass** carried over the 6 OTP boxes filled dark green with white
digits "842910" — the entered code shown back as positive confirmation. Read
as data display competing with the celebratory beat. Cut.

**Final composition** is a single centered hero stack:
- 64×64 dark-green checkmark badge (white tick, SVG-imported because raw
  `vectorPaths` for open-stroke paths was unreliable). Enlarged from the
  original 44×44 since it now carries the visual weight alone.
- Title "You're in!" Inter Semi Bold 24px centered.
- Subtitle "Welcome to Home Circle" DM Sans Medium 16px @60% centered below.
- OTP boxes hidden; resend row hidden (both preserved as invisible nodes,
  restorable).

Sheet shape, footer, and blurred background carried over unchanged. The morph
sequence is now reviewable end to end as three nodes on the same surface:
`1:859` (phone input) → `1:932` (OTP entry) → `707:2` (success). This is the
visual that holds for ~700ms in production while network calls complete, then
the sheet dismisses into setup profile.

Hardened the principle behind the cut: **a confirmation state confirms the
outcome, not the input.** The code was a means; the account is the end. Show
the end.

### MVP auth: phone-only — sign-up sheet collapsed (`1:859` + `583:1357`)

*From session April 25, 2026*

Locked decision: MVP ships with **Continue with Phone as the only auth path**.
Apple and Google OAuth dropped for v1.

Applied to the universal sheet (`1:859`) and its in-context Saved-tap clone
(`583:1357`):
- Apple and Google buttons hidden (preserved as invisible nodes — easy to
  restore for v2 without rebuilding the layout).
- Phone button moved to y=0 of the OAuth container (was the third in the
  stack at y=144).
- OAuth container shrunk from 202h to 58h (just the phone button).
- Divider, sign-in row, and ToS/Privacy footer all shifted up 144px to close
  the gap.
- Inner content container shrunk from 584h to 440h.
- Sheet shrunk from 606h to 462h, top moved from y=326 to y=470 (bottom
  anchored at y=932 — sheet still emerges from screen bottom, just taller-
  feeling because there's less to fit).

Continue with Phone is now visually dominant on the sheet — no competing OAuth
options to dilute attention. The pre-existing title-by-context map (which
varies the heading per entry-point) still works unchanged; only the auth
options below changed.

Subtitle copy ("Join your neighbors on Home Circle.") kept for now. Open
question still on the table: phone-only makes the heading + subtitle bear
more weight on building trust before asking for a number, so a future swap
to a trust-framed subtitle ("We'll text a code. Your number stays private.")
is worth considering once we see early conversion data.

### Guest Out of Market (`668:2`) — built

Closed the geofence-fallback corner of the guest experience. Triggered when
an anonymous visitor's IP resolves outside the South Bay launch market
(decision-log Apr 25 spec'd this as "generic 'in your area' copy + email
capture"). Now built and visualized.

Cloned from `663:2`, then re-shaped to fit a different ask:
- Title: "Coming to Sacramento soon" (city name pulled from
  `request.cf.city`; mockup uses Sacramento as the canonical NorCal-out-of-
  South-Bay case — most early geofence traffic will be neighbors, not random
  distant cities).
- Subtitle: "Drop your email and we'll let you know the moment we launch in
  your area." Purely action-oriented since the title already sets the scene.
- Email input field added between subtitle and CTA: 366×56 white pill, subtle
  dark-green border @15%, "Email address" placeholder. Sized to match the CTA
  pill exactly so the two read as siblings — input then action.
- CTA: "Notify Me" (waitlist framing, not sign-up — they don't get an account
  here, they get a launch ping).

Why a different screen instead of stretching `663:2` to cover both: the value
exchange is fundamentally different. In-market = "be the first to vouch"
(contribution invitation); out-of-market = "we'll let you know" (waitlist
promise). One screen trying to do both would muddle the ask. Better to read
the IP and switch the screen.

Two state-specific guest patterns preserved across both guest cold-start
variants: Saved keeps the lock (guests still can't save), FAB stays hidden
(can't vouch in either scenario, and especially not when we're not live).

The full guest experience matrix is now mapped:
- In-market, vouches present → `583:672`
- In-market, no vouches → `663:2`
- Out-of-market → `668:2`

### Guest Cold Start All (`663:2`) — built

Closed the last open corner of the Cold Start matrix. Coexists with `583:672`
(populated Guest All) — same user state, populated-vs-empty split. Triggered
when an anonymous visitor's IP resolves into the launch market but no neighbor
vouches have been seeded yet.

Built by cloning `583:672`, then stripping the 3 NEIGHBOR cards, the inline
Sign Up Nudge banner, the NEIGHBORHOOD FEED label, and the filter chips strip.
Same Cold Start treatment as `1:3137` / `656:2`: header 181→128, body shifted
to y=151. Hero stack cloned from `656:2`: house illustration, title, subtitle,
CTA wrapper. CTA text re-flowed (cloned "Connect Contacts" was wider than the
new "Sign Up" so the textAutoResize fix and recenter were applied again).

Copy locked to:
- Title: "Join Home Circle" — matches the existing 615:2 nudge brand title;
  Guest screens use this consistently so the brand recognition compounds across
  states.
- Subtitle: "Be the one your neighbors thank later. Vouch for a pro you trust."
  — parallels `1:3137`'s "Be the one your friends thank later" but swaps
  "friends" → "neighbors" because guests have no contacts and the social graph
  defaults to geographic.
- CTA: "Sign Up" (chosen over email capture / dual CTA — sign-up is the
  conversion goal across every guest state).

Two state-specific guest patterns preserved: **Saved tab keeps the lock**
(communicates the gate before tap, no surprise sign-up wall); **FAB hidden**
(guests can't vouch, no relationships exist).

Out-of-market geofence (different scenario, see Apr 25 IP geolocation decision)
gets generic "in your area" copy plus email capture — separate variant, not
designed yet. The guest-cold-start matrix is now: in-market = `663:2` (built);
out-of-market = TBD.

Three Circle states × Cold Start coverage now complete:
- Guest → `583:672` populated / `663:2` cold start
- Pre-Contacts → `590:2` populated / `656:2` cold start
- Authenticated → `554:321` populated / `1:3137` cold start

### Pre-Contacts Cold Start All (`656:2`) — built

Filled the gap in the Circle state matrix: a user who's signed up but hasn't
synced contacts yet AND whose market has zero neighbor vouches. Coexists with
`590:2` (Pre-Contacts All with neighbor vouches present) — the populated-vs-empty
split for the same user state.

Built by cloning `590:2`, then stripping everything that doesn't apply when
there's nothing to feed: 3 NEIGHBOR-tier cards, the "Find Your Circle" nudge
(replaced by a hero CTA below), the NEIGHBORHOOD FEED label, and the filter
chips strip. Same Cold Start treatment as `1:3137` — header shrunk 181→128
to remove the chips slot, body shifted to y=151 to close the void.

Hero stack cloned from `1:3137`: house illustration, title, subtitle, CTA
wrapper. Copy locked to:
- Title: "Build your circle"
- Subtitle: "Connect your contacts to see vouches from people you trust."
- CTA: "Connect Contacts"

**Value-first vs seeding-first CTA decision**: chose "Connect Contacts" over
"Vouch for a Pro" (the CTA on `1:3137` Cold Start). The user just signed up
and gets immediate personal value from sync — they see the network unlock —
whereas vouching is a contribution ask before the app has demonstrated worth
to them. Vouching remains accessible via the FAB. On `1:3137` the user is
already past sync, so vouching IS the next ask.

Fixed a clone artifact: the cloned CTA text node had `textAutoResize=NONE`
which wrapped "Connect Contacts" inside its 122px constraint. Reset to
`WIDTH_AND_HEIGHT` and recentered the text within the 366×56 button.

### Empty Saved states (`636:2` Pre-Contacts, `643:2` Authenticated) — built

Saved tab needed an empty state for users who haven't hearted anything yet.
Built two variants from the same shell:

- **Pre-Contacts Empty Saved** (`636:2`): cloned `606:2`, stripped cards, kept
  the "Find your circle" nudge at top (it's still the primary action — connect
  contacts to discover pros worth saving). Empty state sits below the nudge.
- **Authenticated Empty Saved** (`643:2`): cloned `636:2`, removed the nudge,
  centered the empty state in the full available area.

Both use the same composition: 80×80 light-green icon holder containing a
36×36 dark-green heart vector + title "No saved pros yet" + subtitle "Tap the
heart on a pro to save them here." No CTA — the action is "tap a heart on a
pro," which lives one tap away in the adjacent All tab. Pushing a "Browse
Pros" button would be patronizing.

Both variants apply the same patterns from Cold Start: filter chips hidden (no
saved pros = nothing to filter), header sized to 128, body shifted to y=151
to close the void above the content. FAB preserved on both — vouching is a
separate action from saving and remains accessible even with empty Saved.

Hardened design principle across Circle: **an empty state is the populated
state with content removed, not with empty affordances retained.** That rule
killed the secondary CTA on Cold Start, the chips on Cold Start, and the
chips on both empty Saved variants.

### Cold Start All (`1:3137`) — IA mismatch fixed, copy tightened

The "Fully Onboarded - All - No reviews" empty state had two real problems:

1. **4-tab segmented control** (All / 1st Circle / Mutual / Neighbor) broke the
   locked 2-tab All/Saved IA used everywhere else in Circle. A user landing here
   would see a different navigation pattern than every other Circle screen.
2. **Secondary CTA "Browse neighborhood vouches"** contradicted the empty-state
   premise — if there are no vouches anywhere in your reachable graph, there's
   nothing to browse.

Fixes applied:
- **Header swap**: 4-tab control deleted; canonical 2-tab All/Saved + filter
  chips header cloned from Pre-Contacts All (`590:113`) into `1:3138` at (0,0).
  Body shifted from y=102 to y=181 to clear the new header.
- **Secondary CTA hidden**: `1:3149` set to invisible. Single primary CTA
  "Vouch for a Pro" carries the action.
- **Subtitle tightened** to 13 words: "Be the one your friends thank later.
  Vouch for a pro you trust." Original was longer and softer; this version puts
  the call-to-action in the second sentence so the friend-thanks-later framing
  earns the vouch ask.
- **Standard refinement pass**: cream BG to canonical `#F8F6F0`, primary CTA
  fill to canonical dark green `#1A3B2B` with dual 3% drop shadows, CTA wrapper
  shrunk from 366×104 to 366×56 to remove slack from the hidden secondary,
  title kept at Instrument Sans Bold 24px (per design system — DM Serif Display
  was wrong; Instrument Sans is the canonical heading face).

Filter chips also dropped (revised after first review). I'd rationalized
keeping them as "continuity with populated state" but the same logic that
killed the secondary CTA kills them too — chips imply "filter the feed by
category," but with zero vouches there's no feed to filter. An empty state
is the populated state with content removed, not with empty affordances
retained. Header shrunk from 181 to 128 to remove the chips strip; body left
at y=181 so the illustration stays vertically balanced (shifting it up would
have made the screen feel top-heavy). NEIGHBORHOOD FEED label intentionally
absent (no feed yet to label). When the first vouch lands, chips and feed
label both reappear — clean transition, no IA shift.

### Circle Guest All (`583:672`) shipped — three-state Circle now complete

Guest state had two issues: voucher copy named contacts (Elena R., Marcus C.) the
guest doesn't have, and there was no sign-up CTA on the screen — wasted
conversion real estate.

Three changes applied:
- **All voucher copy switched to NEIGHBOR pattern**: Card 1 → "a neighbor in
  Willow Glen", Card 2 → "a neighbor in Los Gatos", Card 3 → "a neighbor in
  Campbell". "+ N others" hidden on all 3 (per locked NEIGHBOR-tier rule —
  highest-tier-wins means a NEIGHBOR-badged card has only neighbor vouches, no
  other tier to roll into "+ N others").
- **FAB hidden**: guests can't vouch (no account, no relationships), so the
  "+" was functionally useless. Saved tab keeps the lock icon to signal the
  gate before tap.
- **Sign-up nudge banner added**: cloned the Pre-Contacts banner shape so the
  Guest and Pre-Contacts screens share the same DNA. New copy — title "Join
  Home Circle", subtitle "Sign up to save pros, vouch for the ones you trust,
  and unlock your circle.", CTA "Sign Up". Notebook-with-hearts icon retained.
  NEIGHBORHOOD FEED section header included (matches Pre-Contacts All — both
  are discovery feeds).

The three Circle states now form a clean progression: **Guest** ("Join Home
Circle" nudge) → **Pre-Contacts** ("Find your circle" nudge) → **Authenticated**
(no nudge, real circle feed). Same shell, different verb at each gate.

### Sign-up sheet (`1:859`) confirmed as universal guest gate

Existing sign-up sheet works as-is — bottom sheet over blurred content, OAuth
options (Apple / Google / Phone), Sign in fallback, ToS/Privacy footer. No
new design needed. Title varies by context (Saved tap → "Sign up to save pros",
heart tap → "Sign up to save [Pro Name]", Contact tap → "Sign up to contact
[Pro Name]", etc.) — single component, `context` prop maps to title.

Fixed one inconsistency: subtitle was "Join your **neighbours**..." (British
spelling). Swapped to American "neighbors" to match NEIGHBOR badge + voucher
copy used everywhere else.

Locked Saved tab on Guest All triggers this sheet; no separate Guest Saved
screen needed (would be 100% sign-up wall).

### Guest location: IP geolocation, geofenced to launch market

Decision logged. Use Cloudflare Workers' free `request.cf.city` to resolve guest
location at city granularity. Voucher copy stays anchored to the pro's city
(neighbor lives near the pro), but the feed itself is filtered/sorted by guest's
IP-resolved city. Outside South Bay → fall back to generic "in your area" copy
+ email-capture prompt. VPN/data-center IPs default to generic. Replaced once
the user signs up + sets a neighborhood in onboarding.

### Circle Pre-Contacts Saved v2 (`606:2`) shipped

Old Pre-Contacts Saved (`583:885`) had broken data: all 3 cards carried NEIGHBOR
badges but two of them displayed "Vouched by Elena R. + 4 others" / "a friend of
Marcus C. + 2 others" — names that come from the user's contacts, which a
pre-contacts user doesn't have. Inconsistent and misleading.

Cloned `590:2` (Pre-Contacts All) and applied three changes to make the Saved tab:
- Segmented control flipped: All inactive (no fill, text @40%), Saved active
  (white fill + dual shadow, text #1A3B2B)
- Dropped the "NEIGHBORHOOD FEED" section header — that label belongs on the
  discovery feed (All tab), not on the user's own saved collection. The natural
  ~58px gap between nudge and first card reads as a clean section break without
  the label
- All 3 hearts switched to filled saved-state (#1A3B2B, opacity 1, strokes cleared)

Card positions kept identical to `590:2` (y=219/384/549) so the two tabs share
exactly the same vertical structure — only segmented state and heart fill differ.
Same nudge banner, same notebook icon, same 3 NEIGHBOR-tier cards with
"a neighbor in [city]" attribution. Old `583:885` removed.

### Circle Pre-Contacts v2 (`590:2`) shipped

Old pre-contacts Circle (`1:3191`) had a 3-tab control (Neighborhood / My Circle /
Saved) and vouch-centric cards — inconsistent with authenticated Circle's IA
(2-tab All/Saved + filter chips, pro-centric cards). Users would experience an
IA shift the moment they connected contacts, which is bad.

Cloned Circle All v2 (`554:321`) and modified to make Pre-Contacts:
- All 3 cards demoted to NEIGHBOR outlined tier (no 1st circle / mutual friend
  pros exist pre-contacts)
- Voucher attribution → "a neighbor in [city]" pattern across all 3
- Prepended a "Find your circle" nudge banner: 390×153 white card, 80×80 light
  green icon holder with the notebook-with-hearts illustration (cloned from
  prior nudge banner `583:535`), title + subtitle + "Connect Contacts" pill CTA.
- Added a "NEIGHBORHOOD FEED" section header between the nudge and the cards
  (DM Sans Black 12px, #1A1A1A @20%, 1.2 letterSpacing, UPPER) so users
  understand the cards aren't from their own circle yet.
- Cards positioned at y=219 / 384 / 549, body container 430×697.
- Old `1:3191` removed.

Net effect: pre-contacts Circle is structurally identical to authenticated
Circle, with the only differences being content (only NEIGHBOR pros) and the
nudge banner up top. Connecting contacts simply makes the screen richer — the
shell stays the same.

### Circle Saved v2 (`554:158`) shipped

Mirrored the canonical Circle All v2 (`554:321`) pattern onto the Saved tab. Old
redesigned Saved (`499:462`) marked Superseded.

Structural changes applied to all 3 cards:
- Card height 136/138 → 140; avatar (17,17) → (16,16); content height 102 → 108
- Added city row (Willow Glen / Los Gatos / Campbell) at (0,34), DM Sans Medium 13px @40%
- Moved category pills inline to x=86/71/72 (after city)
- Vouched-by row y=51.5 → y=58; badge row y=80 → y=87
- Heart tap target 22×22 → 44×44 (HIG min) at (243,-11) with heart vector at (11,11)

Tier corrections (data + visual hierarchy):
- Flow State Plumbing → 1ST CIRCLE filled green (unchanged)
- Roots Gardening: voucher "Friend of Marcus C." → "a friend of Marcus C." (lowercase article)
- Pristine Paint Co: 1ST CIRCLE filled → NEIGHBOR outlined; voucher "Sarah J. + 1 other" → "a neighbor in Campbell" (no count, hidden + N others)

Header/nav fixes:
- Segmented control buttons 40 → 44px tall (HIG); container 48 → 52
- Filter chip row y=123.5 → 127.5 to maintain spacing
- Nav labels Title Case → UPPERCASE; DM Sans Black 9px / 0.9 letterSpacing; active Circle = #1A3B2B; inactive at 30%

The Saved screen now differs from All in only two intentional places: segmented
control has Saved active, and hearts render filled green (saved state).

## Previous session (April 24, 2026)

### Sort & attribution logic locked

Major product discussion that landed clear, defensible patterns:

- **Circle tab (All) sort:** trust tier first (1st circle → mutual friend → neighbor),
  recency within tier. Multi-tier pros use highest-tier-wins. Soft decay 18 months.
- **Home tab sort:** pure recency, all three tiers included (avoids empty cold-start feeds).
  Tier inclusion is a backend config — flip a flag if neighbor vouches dominate the feed
  at scale. Soft decay 6 months.
- **Search:** global, returns pros (not vouches), trust-first sort. Existing search screens
  (`1:419`, `1:526`, `1:648`) already designed for this.

### Vouch attribution patterns — locked with privacy reasoning

The patterns vary by tier because the data we have access to varies:

| Tier | 1 voucher | N vouchers |
|------|-----------|------------|
| 1ST CIRCLE | "Vouched by Elena R." | "Vouched by Elena R. + N others" |
| MUTUAL FRIEND | "Vouched by a friend of Sarah J." | "Vouched by N friends of Sarah J. + M others" |
| NEIGHBOR | "Vouched by a neighbor in Willow Glen" | "Vouched by N neighbors in Willow Glen" |

**Privacy reasoning:**
- We only have voucher names for 1st-circle (because they're in your contacts).
- For mutual friend tier, the voucher's name is never shown — we name the bridge person
  (in your circle) who connects you to the FoF voucher.
- The "+ N others" represents all additional vouches across any tier (honest counting).
- ToS covers the disclosure (voucher consent + bridge consent). No explicit per-user
  toggle needed for MVP.
- Bridge selection: when multiple bridges, name the one with most friends vouching
  for that pro. Tiebreaker: most recent.

### Tier badge visual hierarchy locked

- 1ST CIRCLE = filled dark green (#1A3B2B) with white text + white icon
- MUTUAL FRIEND = light green fill (#E3ECE6) with dark green text + green icon
- NEIGHBOR = outlined (transparent fill, #1A3B2B border at 25% opacity, text at 60%)

Three distinct treatments make trust hierarchy readable at a glance without parsing labels.

### Circle All v2 (`554:321`) shipped

Replaces the earlier `554:2` version (now marked Superseded). Key features:

- Compact pro card layout (140px tall)
- Row layout: pro name + rating → city + category pill (inline) → vouched-by → tier badge + heart
- All three tiers visually demonstrated (Flow State Plumbing 1ST CIRCLE, Roots Gardening
  MUTUAL FRIEND, Pristine Paint Co NEIGHBOR)
- Card padding 16px (8pt grid aligned), 8/8/9 vertical rhythm
- Heart Button frame 44×44 (HIG min tap target) with 22×22 visible heart vector centered
- Segmented control buttons 44px tall (HIG compliant)
- Standard refinement pass applied (cream BG, dual 3% shadows, fonts, FAB green-tinted
  shadow, etc.)

### UX/accessibility audit + refinement

Pushed the screen against standard mobile UX guidelines (iOS HIG, Material Design,
8pt baseline grid). Fixed: 2px gap inside cards (now 8px), heart tap target 22→44, segmented
buttons 40→44, card padding 17→16. Confirmed: ≥44pt touch targets, 8pt baseline grid,
consistent vertical rhythm, HIG-compliant.

## Other Q2 work in flight (from prior sessions)

- 35+ Figma screens already refined and Done
- 18-month "Leap 1" roadmap printed as 11×17 wall poster
- Architecture documented (`docs/arch.md`): RN+Expo mobile, FastAPI+Postgres+PostGIS,
  R2 storage, Clerk auth
- Strategic plan complete (`docs/strategic-plan.md`)

## Key decisions in play

- **Pre-launch data seeding:** $1K Starbucks gift card campaign at South Bay farmers markets / hardware stores. Timing relative to app launch still TBD.
- **Engineering sprint replanning:** with design extending ~3 weeks past original close, the engineering schedule needs explicit replanning. Sprint 2 onwards (Auth & Onboarding through App Store submission) all shift. Should happen once Phase 6 prototyping is done so we have realistic remaining-screen complexity to plan against.

## Open questions

1. Tech stack details — which auth provider, which infra, etc. (Resolved per `docs/arch.md` + V1 decisions in `docs/decision-log.md`. OTP: Twilio Verify. Image hosting: Supabase Storage with AWS Rekognition async moderation, locked May 12.)
2. ~~When do we start the gift card campaign relative to app development?~~ — **Resolved May 14, 2026.** Planning + materials Mon Jun 9 → Fri Jun 13 (Week 5 of build). Field execution: 4 Saturdays Jun 14, 21, 28, Jul 5 (overlapping Week 6 of build through pre-launch). Admin seeding pipeline shipped Day 22 morning supplemental (Jun 9). Tracked as tasks #89 / #90 / #91 + new "Gift Card Seed Campaign" section in `sprint-tracker.md`. Total budget ~$1,100, total time ~30h spread over 5 weeks.
3. Do we need a landing page / waitlist before the app launches?
4. Should bridge attribution ("a friend of Elena R.") get a per-user privacy toggle in v2, or is ToS-only consent acceptable long-term?

## File index

| Doc | Path | What it contains |
|-----|------|-----------------|
| This brief | `docs/founding-partner-brief.md` | Current state, session summaries, open questions |
| Design system | `docs/design-system.md` | Figma reference, refinement checklist, screen status |
| Strategic plan | `docs/strategic-plan.md` | Quarter-by-quarter plan, weekly sprints, risk register |
| Decision log | `docs/decision-log.md` | All significant decisions with rationale |
| Sprint tracker | `docs/sprint-tracker.md` | Current sprint, backlog, priorities |
| Architecture | `docs/arch.md` | Mermaid architecture diagram + tech stack |
| Roadmap poster | `home-circle-roadmap-poster.pdf` | 11×17 Leap 1 wall poster (Apr 2026 → Sep 2027) |
