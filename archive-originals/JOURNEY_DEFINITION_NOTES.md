# Home Circle V1 — Journey Definition

**Status:** All decisions resolved. Ready to sketch.

**Goal:** Screen-flow diagrams capturing all V1 user journeys. Granularity A — one journey per action, state variants annotated inline.

---

## Table of Contents
1. [Product Principles](#product-principles)
2. [Resolved Decisions](#resolved-decisions)
3. [Final Journey List](#final-journey-list)
4. [Implementation Plan Integration](#implementation-plan-integration)
5. [Sketch State Backlog](#sketch-state-backlog)
6. [Next Steps](#next-steps)

---

## Product Principles

These are the load-bearing decisions that everything else hangs off of.

### 1. The community IS the catalog
**Pros only exist in the system because someone vouched for or saved them.** No admin-seeded pro database. No pros without social signal.

- Search empty states are common and meaningful in early days; become primary acquisition levers.
- The Add a Pro composer is the *only* way pros enter the system.
- Cold-start strategy = seed vouches, not pros. Reinforces Refer/Invite (J9) as critical.

### 2. Save and Vouch are one record
**One `saves` table** with optional `rating` and `review_text` columns. A "vouch" is just a save where `rating IS NOT NULL`. Same row, just enriched.

- "Graduate save → vouch" is an UPDATE, not a new row.
- The UI uses "Save" + "Review" as user-facing verbs. **The word "Vouch" does not appear in V1 UI** (internal/PM term only).
- Single source of truth on the save record itself.

### 3. Heart tap is frictionless
- Heart on any pro card/profile → instant save, default visibility = **Circle**, no composer.
- Toast on save: **"Saved to Circle (visible to friends) — Change"** — discloses visibility at the moment of save with an inline action to demote.
- Tap heart again → unsave (toggle).

### 4. Per-pro visibility, managed on Saved tab
- Visibility lives on each save record. Three values: **Private / Circle / Neighborhood**.
- Default at heart-tap = Circle.
- Per-pro toggle on the Saved tab lets users adjust visibility post-save.
- **VisibilityChip is ALWAYS interactive** — even on rows that have reviews. Save visibility is user-controlled regardless of review presence.

### 4a. Save visibility and review publicness are INDEPENDENT axes
- `save.visibility` ∈ {Private, Circle, Neighborhood} — user-controlled, mutable any time via the chip.
- `has_review` (with rating + review_text) — the existence of a review is implicitly public on the pro's profile + search ranking.
- **Adding a review does NOT mutate `save.visibility`.** A user can have a Private save AND a public review for the same pro — the review surfaces publicly (review-publicness is a different axis), the save stays Private (no friend-feed broadcast).
- When both exist on the same record, the review (public) takes precedence in any other-user-facing display; the Private save is just the user's internal label.
- Backend never auto-upgrades save visibility on review creation.

### 4b. Two-step Add-a-Pro flow (progressive disclosure)
- Composer is split into two screens, not one dynamic-submit-label organism.
- **Step 1 — `AddProComposer`:** pro search/select (or "Add new pro" inline form), visibility selector (always visible), static "Save Contractor" CTA. No rating/review fields.
- **Step 2 — `ReviewSheet`:** auto-shown bottom sheet after Step 1's save success. "Have you worked with them?" → optional rating + review + photos. Submit attaches the review to the save record (visibility unchanged). Easy skip.
- ReviewSheet is reusable in 3 places: post-save intercept (Step 2), Pro Profile "Add a review" CTA (auto-saves the pro first if not saved), Saved tab → row tap → "Add/Edit review".
- **The word "Vouch" never appears in V1 UI strings.** Internal/PM/strategic term only (banked as a post-launch brand lever). See Vocabulary principle below.
- See `D17_REVISED.md` for the full restructured day.

### 5. Three trust tiers, no generic fallback
1. **1st Circle** — direct friend vouch/save
2. **Mutual Friend** — friend-of-bridge vouch. Bridge must be in viewer's contacts (this is the *condition for the tier to apply*, not just a naming policy — if bridge isn't in viewer's contacts, the result falls to Neighborhood tier instead). Bridge is always named to viewer; voucher is always anonymous.
3. **Neighborhood** — geographic; also the fallback

**Locked card copy (vocabulary: "Reviewed by" not "Vouched by" — RESOLVED Jun 27):**
- 1st Circle: "Reviewed by **Sarah J.**" / "Saved by **Sarah J.**"
- Mutual Friend: "Reviewed by **Marcus C.**'s friend" (bridge name bolded; reviewer anonymous; possessive form locked Jul 3; no conditional fallback within the tier)
  - **Multiple bridges → most-recent single named** for V1. If three of viewer's contacts have separate friends who all reviewed, pick the bridge from the most recent review. Aggregate copy ("...of Marcus, Jenny, +1") is V1.5 polish.
- Neighborhood: "Reviewed by **N neighbors in Willow Glen**" (count + neighborhood)

If a pro has zero of all three tiers: **the pro doesn't exist in the system.**

### 6. Cards self-describe — no SAVED vs VOUCHED badge
A pro card shows:
- Tier badge (1st Circle / Mutual Friend / Neighborhood)
- Attribution: "Saved by Sarah" OR "Sarah ★★★★★ 'fixed our sink fast'"

Presence of the review line IS the vouch indicator. No separate badge taxonomy.

### 7. Mixed sort within tier
Within each tier section: **Vouched-multi > Vouched-single > Saved-multi > Saved-single.** Vouches are stronger signal but saves still count.

### 8. Welcome screen is one-time
- Shown on first install only (and reinstall, since localStorage is wiped).
- Subsequent cold opens land on Home (Guest).
- Welcome has split-button "Browse Pros in [city]" — left side commits to detected city, right side opens picker. City chosen here becomes the persisted default.

### 9. Sign-in is a separate full-page flow
- NOT the Onboarding Sheet. Different mechanism for returning users.
- Three post-OTP branches:
  - **Recognized + Onboarded** → "Welcome back" → restored
  - **Recognized + partial** → "Welcome back — let's finish setting up" → Quick Setup
  - **Not recognized** → "Looks like you're new here" → fresh Quick Setup
- Server retains contact hashes → reinstall restores user to Fully Onboarded immediately.

### 10. No mid-flow state preservation (V1)
- Composers and sheets do not persist mid-state. App backgrounding = state lost.
- Affects: AddProComposer (Step 1), ReviewSheet (Step 2), AddLedgerEntry, EditProfile, Refer ShareSheet.
- **The OTP resume from secure storage (D5) is the ONLY exception** — required because the user has already committed to verification.
- Rationale: V1 simplicity. Engineering cost of partial-save persistence is meaningful; user expectation is low for in-app forms.
- Sketches should NOT imply save-draft affordances (no "Resume where you left off?" prompts, no auto-save indicators).

### 11. No in-app notification system (V1)
- No push notifications. No in-app toast notifications triggered by other users' actions ("Sarah joined via your invite", "Marcus saved a new pro", "New vouch on a pro you saved", etc.).
- No badge counts on tab icons. No "unread" indicators anywhere.
- Out-of-V1 scope per the Plan; this principle exists so sketches don't accidentally include nudges that imply event-driven surfacing.
- The only "notifications" in V1 are inline toasts triggered by the user's own actions ("Saved to Circle", "Couldn't send code", etc.).

### 12. Auto-complete asymmetry (post-auth pending actions)
When a Guest triggers auth by tapping a locked action, the action resumes after onboarding — but **only if it's silent and reversible.**
- **Auto-complete:** Save (quiet, reversible, tap heart again to undo) → fires automatically post-auth, lands on PP-3 + disclosure toast.
- **Never auto-complete:** Call / Text (loud, irreversible — a real contractor sees a missed call/text). The locked button **unlocks** to PP-2; the user taps Call themselves to dial.
- **The deeper rule:** explicit tap-to-Call right now → dial (authed user tapping Call = standard `tel:` behavior). Dial as a *side effect of completing another flow* → don't; reveal instead. The lock promised "tapping unlocks," so reveal-not-dial fulfills that contract; auto-dialing breaks it.
- No 6th "revealed" Pro Profile variant — the post-auth screen just renders PP-2.

---

## Resolved Decisions

### Welcome screen
- One-time only (first install + reinstall).
- Split-button city picker. Left = commit, right = override.
- Sign in link below primary CTA.
- **Pro Claim NOT on Welcome.**
- **Referral link** has its own Welcome variant (friend pre-attached + their vouches pre-populated).

### Location handling
- Welcome split-button → persisted default.
- Search screen has dropdown to override per-search; new pick becomes new default.
- Onboarded users can set location in Profile (overrides last-searched).

### Tab bar
**Home / Saved / Ledger / Profile** (4 tabs). No separate Search tab.

### Home tab
- Search bar at top.
- Category chips (Plumber / Electrician / Landscaping / ...).
- N1 banner (Guest only): "These pros are trusted by your neighbors. Sign up to see who your friends recommend →"
- **Activity feed** (renamed from "Recent Vouches"): mixed saves + vouches. Suggested name: **"Recent activity from your circle"** (Onboarded) or **"Recent activity in your neighborhood"** (Guest/Auth).
- Trending section DESCOPED for V1.
- FAB bottom-right.

### Search
- Dedicated screen, not inline.
- Two entry paths from Home:
  - Tap search bar → Search screen (keyboard + location dropdown + category chips)
  - Tap category chip → straight to Search Results (skipping Search screen)
- Search Results sections:
  - **FROM YOUR CIRCLE** (1st Circle + Mutual Friend, sorted by 1st Circle first)
  - **NEIGHBORHOOD PROS** (renamed from "Verified Neighborhood Pros")
- Empty state: "No one in your area has reviewed a [category] yet. Know a great [category]? **Add them →**"
- **Edit query from Results:** tap the breadcrumb pill ("Plumber · Willow Glen") on Search Results → navigates back to Search screen with both fields pre-filled and editable. No inline-edit state on Results (deferred to post-V1 if usage data justifies).
- **Zero-state (one screen, both entry paths):** typed-bar (Path 1) and category-chip (Path 2) route to the SAME empty. Add-a-Pro CTA always present; "Clear filters / broaden" shown **only when a filter is active** (genuine category gap has nothing to clear). Three distinct empties kept separate: (1) this = zero in the live market for a category; (2) J28 cascade = zero in circle but neighborhood has results → fall to Neighborhood tier; (3) market gating = zero market entirely → Coming soon. Bias users toward canonical category chips over free-text to kill most typo-empties (no fuzzy matcher in V1).

### Save / Add a Pro / Review (progressive disclosure two-step)

**Three entry points, three composer-flow variants:**

- **Heart tap** (on Pro card OR Pro Profile heart): instant save, default visibility = Circle, toast "Saved to Circle — Change". No composer. Tap again → unsave.
- **FAB → "Add a Contractor"**: full two-step flow.
  - **Step 1 — AddProComposer:** Pro search/select (or "Add new pro" inline form for new entries). When a pro is selected from autocomplete, render a **rich preview card** (photo + full address + phone + category + "Not the right pro? Search again" affordance) to confirm identity before save. Visibility shown as **subtle single-line row** ("Visible to: **Circle** · your friends ▾") that taps to open the picker bottom-sheet. Static "Save Contractor" CTA.
  - **Step 2 — ReviewSheet (auto-shown after save):** "Have you worked with them?" + optional rating + review + photos. "Add review" or "Not yet" skip. Submitting a review never changes `save.visibility`; the review's publicness is independent.
- **Pro Profile "Add a review" CTA**: opens ReviewSheet directly. If pro not yet saved by this user → auto-save with default Circle visibility first (silent), then open ReviewSheet. Single-intent tap for the high-intent reviewer.

**Post-save routing — depends on entry context:**

| Entry | After save (skip review) |
|---|---|
| Heart-tap on Pro card / Pro Profile | Stay on current screen |
| PP "Write a review" → ReviewSheet | Stay on Pro Profile |
| **FAB → Add a Contractor from Home** | **Back to Home** + new pro appears in feed |
| **FAB → Add a Contractor from Saved** | **Back to Saved** + new row appears |
| FAB → Add a Contractor from Ledger | Back to Ledger |
| FAB → Add a Contractor from Profile | Pro Profile (Profile isn't a meaningful return context) |

Rationale: FAB is a "do this thing in the middle of what I was doing" affordance. Going to Pro Profile after FAB-add disrupts context.

**Google Places photo for the preview card:** used for preview-only confirmation; NOT persisted with the save record. Falls back to category-icon placeholder if URL expires before submit. Keeps cost discipline from Google Places integration spec.

**Saved tab edit paths:**
- Tap row → "Edit details" → opens AddProComposer pre-filled (for changing pro name/phone/category/visibility).
- Tap row → "Add/Edit review" → opens ReviewSheet only (pre-fills existing review if any).

**Data model:** One `saves` table with nullable `rating` + `review_text` + `visibility` columns. `has_review = (rating IS NOT NULL)` is the vouch discriminator. Save visibility and review publicness are independent (see Principles 4a + 4b above).

**UI string discipline (Vocabulary principle — RESOLVED Jun 27):** The word "Vouch" never appears in V1 UI. The user's verb and the card's attribution must close the same loop:
- **Verb (what you do):** "Add a review" / "Write a review" / "★ rate".
- **Attribution (what you read):** "Reviewed by Elena R." (review) · "Saved by Marcus C." (save, no review). A matched pair — both plain verbs, obviously the same family.
- **Why "Reviewed" not "Vouched":** (1) closes the verb→attribution loop (you Add a review → card says Reviewed); (2) rating-agnostic — you can't "vouch" a 2-star review, but "Reviewed by" renders at any rating (correctness, not style); (3) the moat is the trust graph (proximity badge + "from your circle"), not the noun — "Reviewed by Elena R. · 1ST CIRCLE" says everything "Vouched by" said.
- **Two-axis model intact:** tier badge = network proximity; Reviewed-by / Saved-by = endorsement strength.
- **Kill the ✓✓ glyph** — unexplained chrome that reads as a separate "verified" signal. Just "Reviewed by Elena R. +4".
- **"Vouch" stays internal** (data model `has_review` discriminator, analytics events `vouch_*`, strategy docs). Warming a label up later is cheap; un-confusing users at onboarding isn't.

**ReviewSheet submit-enable logic:** Submit ("Add review") is enabled only when a rating is set. Text alone is not enough — ratings are the universal sortable trust signal. When submit is disabled because the user typed text but didn't tap a star, show helper text under the stars: **"Tap a star to add your review."** Sketch the disabled / unrated state explicitly so the footgun is designed for.

**FAB menu:** Add a Contractor / Log Maintenance / Refer Friends (3 actions). *(User-facing label "Add a Contractor" resolved Jul 3 — matches "Find contractors" / "Save Contractor". Internal component stays `AddProComposer`; "pro" kept as compact object shorthand in dense labels like card subtitles.)*

### Saved tab
- List of user's saves.
- Each row: pro card + visibility chip (Private / Circle / Neighborhood).
- Tap chip → opens visibility picker bottom-sheet (3 radio options + Cancel + Save).
- **VisibilityChip is ALWAYS interactive** — including on rows with reviews. Per the axes-independence model, save visibility is independently user-controlled even when a review is attached. (Earlier draft had the chip greyed for reviewed rows; corrected.)
- Tap row → submenu: "Edit details" (opens AddProComposer pre-filled) OR "Add/Edit review" (opens ReviewSheet only).
- Sort: most recent first by default.
- Empty state: "Keep the pros you find in one place, ready when you need them." (Saving is primarily personal; recommending is a side effect — don't lead with it.)

### Pro Profile structure

**Layout (v2, locked May 26):**

| # | Section | Notes |
|---|---|---|
| 1 | **Top nav** | `‹ Back` (left) + `♡ Save` (right). Heart is the only save affordance — frees primary row for the two highest-intent CTAs. |
| 2 | **Hero photo** | Full-bleed single image. Tap → fullscreen gallery (post-V1; V1 shows first photo only). Category-icon fallback when no photo. |
| 3 | **Title block** | Pro name (display). Subtitle: `Plumber · Willow Glen, San Jose`. Inline social-proof line: **`★ 4.8 · 12 reviewed · 8 also saved`**. The aggregate rating + counts are computed server-side per pro. |
| 4 | **Tier teaser** | Strongest viewer-specific signal (see Tier teaser resolution below). One line, one tier. |
| 5 | **Primary CTAs** | Two big buttons: `Text` (outlined) + `Call` (filled, dark). Locked variants show lock icon over the same buttons. |
| 6 | **Body** | About-only for V1 (Bio, ~500 chars, pro-editable). Services chips + Service Area list deferred to V1.5. |
| 7 | **Reviews header** | `Neighborhood Reviews` (left) + `✎ Write a review` (right, underlined text link). |
| 8 | **Review list** | Reviewer avatar + name + rating inline + relative date + review text + tier label. Thin bottom-border between rows (no boxes). User's own review surfaces first when present. |

**Aggregate rating computation (NEW for V1):** Pro-level rating = `AVG(rating) FROM saves WHERE pro_id = X AND rating IS NOT NULL`. Counts: `vouched` = `COUNT(rating IS NOT NULL)`; `also_saved` = `COUNT(rating IS NULL AND visibility != Private)` viewer-scoped. Computed in the resolver, cached on the pro record. Plan amendment: D11/D14 Pro Profile spec + D17 backend.

**Removed from prior draft:**
- 3-photo thumbnail strip → single full-bleed hero.
- 4-cell stacked action row (Call/Text/Save/Review) → 2-button primary row + heart in nav + tertiary review link.
- Subordinated "Also saved by" section → inline `· 8 also saved` in title block.
- (Licensed & Insured dropped for V1; returns in V1.5 as verified badge.)

#### Tier teaser resolution (server-side, per (pro, viewer))
Teaser surfaces the **strongest** social signal the viewer has access to. Resolution order:

| Priority | Condition | Teaser copy |
|---|---|---|
| 1 | Viewer's own contact has reviewed pro | "**Sarah J.** reviewed" + `1ST CIRCLE` badge |
| 2 | Friend-of-bridge reviewed; bridge is in viewer's contacts | "**Marcus C.**'s friend reviewed" + `MUTUAL FRIEND` badge |
| 3 | Pro has neighborhood reviews (no circle overlap) | "**N neighbors in Willow Glen** reviewed" + `NEIGHBORHOOD` badge |
| 4 | No reviews, only saves | *Teaser hidden* — the `also saved` line in the title block carries the signal. |
| 5 | No vouches AND no non-Private saves | Pro doesn't exist in the catalog — by Principle 1 (community-as-catalog), this case never reaches Pro Profile. |

**Layout (Jul 3):** badge-led — "`[BADGE]` [subject] reviewed". Badge leads so the tier (proximity) is the scannable headline; copy is subject-led across all tiers. The mutual copy ("Marcus C.'s friend reviewed") is subject-led for consistency with the other tiers, and differs from the *card* phrasing ("Reviewed by Marcus C.'s friend", Principle 5) by design — the card carries its badge in the title row so its attribution can be verb-led.

**Same-tier count (1st Circle only):** the teaser pluralizes when >1 of the viewer's contacts reviewed — "Sarah J. reviewed" (one) vs "Sarah J. + 2 friends reviewed" (many). Mutual stays single in V1 (most-recent bridge); Neighborhood is already a count. Cross-tier mixing (e.g. "Sarah J. + 5 neighbors") is intentionally NOT done — it breaks the one-tier badge, and total volume is carried by the Pro Profile title block ("★ 4.8 · 12 reviewed"). Component: `TierTeaser` = `tier` × `count` (one/many; many only on 1stCircle).

**Reuse:** Same resolver function powers Activity Feed cards (D10/D13), Search Results section assignment (D20), and Pro Profile teaser (D11/D14). One function, three surfaces. Spec'd as `resolveTierForPro(pro_id, viewer_id) → { tier, copy }`.

**Viewer-specific:** the same pro shows different teasers to different viewers. Computed per request; not a static pro property.

#### ⚠ BACKEND CRITICAL PATH — `resolveTierForPro` + aggregate rating (build first, test hardest)
This is the literal heart of the product and the riskiest single piece of backend. Not scope creep — a build-sequencing flag.
- **`resolveTierForPro(pro_id, viewer_id)`** powers three surfaces (feed cards, search-section assignment, Pro Profile teaser). One function, three consumers — get it right once.
- **Viewer-scoped, can't be fully cached per-pro:** the tier AND the "N also saved" / "N reviewed" counts depend on the viewer's contact graph, so they're computed per (pro, viewer), not cached as a static pro property. This is the performance-risk hotspot at any real list length.
- **Aggregate rating** (`AVG(rating)` + counts on the saves table) is the one genuinely cache-able-per-pro number; everything relationship-scoped is not.
- **Build and load-test this before any screen polish.** If the resolver is slow or wrong, every surface in the app is slow or wrong.

#### Pro Profile viewer-state matrix (5 distinct sketches)

Pro Profile renders differently based on user state × viewer's relationship to the pro:

| # | User state | Relationship to pro | Top-nav heart | Primary CTAs | Reviews CTA | Notes |
|---|---|---|---|---|---|---|
| **PP-1** | Guest | Any | Locked (`🔒 Save`) | Both locked | Locked | Tapping any locked control triggers OnboardingSheet with appropriate trigger. Tier teaser always Neighborhood (Guest has no circle). |
| **PP-2** | Auth / Onboarded | Not saved | Empty `♡ Save` | Text + Call | `✎ Write a review` | Tapping `Write a review` auto-saves first (silent, Circle default), then opens ReviewSheet. |
| **PP-3** | Auth / Onboarded | Saved, no review | Filled `♥ Saved` | Text + Call | `✎ Write a review` | Tap heart again → unsave (revert to PP-2 state). |
| **PP-4** | Auth / Onboarded | Saved + reviewed | Filled `♥ Saved` | Text + Call | `✎ Edit review` | User's own review row surfaces first in list with `⋯` ownership menu. |
| **PP-5** | Pro (own listing) | This IS the viewer's claimed pro | `↗ Share` (replaces heart) | Hidden (no Call/Text — pro doesn't call themselves) | Hidden (can't review self) | No "Edit Pro Profile" button — **inline pencils** on Photos + About only (tap → focused editor). Name/phone/reviews have no pencil. Phone shown for reference, not a tap target. |

Each is its own sketch.

### Friend Profile (NEW screen)
### Friend Profile / reviewer profile (RESOLVED Jun 27 — universal tappable, visibility-scoped)

**Model:** Any reviewer's name is tappable — friend OR neighbor. One profile screen, one server-side **visibility-scoped query** that returns only what's visible to *this viewer*. The friend/stranger distinction dissolves into the visibility model: friends show more (reviews + Circle-visible saves + Neighborhood saves), strangers show less (reviews + Neighborhood saves only; Circle/Private stay hidden). Same screen, same query, different *amounts* of data. (Replaces the earlier "friend-only tappable" rule.)

**Privacy invariant — default-Circle is load-bearing, not a preference.** The entire privacy story rests on strangers seeing little by default. If the heart-tap save default ever drifts from Circle to Neighborhood, the dossier/aggregation risk reappears at full strength. **Mark default=Circle as a privacy invariant in code comments + decision log; never change it casually.**

**Header copy — only ever surface what's visible; never "N of M":**
| Viewer relationship | State | Copy |
|---|---|---|
| Friend | `visible > 0` | "Sarah shares **5 pros** with you" |
| Friend | `visible == 0` | "Nothing shared with you yet" (hidden + truly-empty collapse — indistinguishable by design) |
| Stranger (tapped off a Pro Profile review) | `visible > 0` | **Neutral count framing: "12 neighborhood reviews"** — lead with their visible footprint, NOT a "shared with you" framing that highlights a zero |
| Stranger | `visible == 0` | neutral, e.g. "No public activity" — avoid the cold "nothing shared with you" for someone you don't know |

Per-category filtering scopes the same logic.

**Not deep-linkable in V1.** In-app navigation only. Deep-link targets in V1 are Pro Profile (`/pro/:slug`) and Referral Welcome (`/invite/:code`).

**Heart-tap on a Pro card inside a profile** behaves identically to anywhere else (instant save, Circle default + toast).

#### Profile body layout — one flat marked list (RESOLVED Jun 27)
The two-axis model surfaces on the person: a reviewed pro (stars + opinion) is a stronger endorsement than a merely-saved one, and the reader must be able to weight them differently.

- **Single flat list, reviewed-first.** Reviewed pros sorted to the top (stronger signal), saved-only below. Each row self-labels — no tabs, no "Reviewed (3) / Saved (8)" sections, no category chips. That sectioning/filtering chrome is the J31 trim deferred to v1.1; at launch density (a couple reviews, a handful of saves) a flat marked list is plenty. Chips earn their place only once lists get long.
- **Row treatment by type** (same Reviewed-by / Saved-by distinction as cards, rendered on a person):
  - Reviewed row → stars + review snippet
  - Saved row → plain "Saved" marker, no stars
- **Dedup to one row per pro.** If they both reviewed AND saved the same pro, show it once as the review — the stronger signal absorbs the weaker. Never two rows for one plumber.
- **Header count = visible reviews + visible saves combined** (e.g. "shares 7 pros with you" / stranger neutral "3 reviews · 4 saved in Willow Glen"). Same visibility-scoped query: strangers see reviews + Neighborhood saves; friends also see Circle saves. No new exposure, no special-casing.

#### Reviewer attribution across surfaces (RESOLVED Jun 27)
The same named review renders differently by surface, because each surface has a different job:

- **Feed / Search cards → aggregate for neighbor tier:** "12 neighbors reviewed." Never an individual stranger's name on a card — a stranger's name carries no proximity trust to you, AND a bare name risks **false-friend confusion** (you have a friend named Sarah; the card says "Sarah J." → you assume it's her). Names surface on cards ONLY for friend/mutual tier, where the name IS the proximity signal ("Reviewed by [your friend] +3").
- **Pro Profile review list → named, tappable, with tier marker ALWAYS attached:** "Sarah J. · 1st Circle ★★★★★ '…'" vs "Marcus C. · neighbor ★★★★☆ '…'". **Name + tier always travel together** — a name with no relationship label reintroduces the exact false-friend confusion the feed aggregation prevents. Tier marker does double duty: friend reviews visually outrank stranger reviews in the list (they carry more weight to you). Sort: friend/mutual reviews above neighbor reviews.
- "A neighbor" is the fallback only for deactivated/nameless users.

#### v1.1 — hide-my-profile control (BUILD-READY, real trigger, not open-ended)
A privacy control letting a user hide their profile / render their reviews as "A neighbor." The aggregation/dossier risk is small at launch density but legitimate (practical obscurity is real — "individually public" ≠ "aggregated into a per-person view"). **Have it designed and built, ready to ship the moment we see any discomfort signal** — do NOT start the design then. Trigger = first user complaint / safety report / press question about reviewer privacy.

### Sign-in (returning users)
- Full-page flow: Welcome → Sign in → Phone → OTP → branch.
- Server doesn't pre-check phone (prevents account enumeration). Lookup happens after OTP verifies.
- Post-OTP returns user to entry context if any, else Home.
- No contact-sync ambush on sign-in — let user land on Home, fire N8 nudge if needed.

### Pro Claim
- Primary CTA on Guest Profile tab alongside "Join the Circle" (Option A).
- Also: Pro Profile claim banner ("Are you Mario? Claim this listing").
- Auto-verify when `claimant_phone === seed_business_phone`. Claim form shows the listed number **read-only**; "Send code" to it → instant claim. The **"This isn't my number?"** escape hatch → verify the pro's **own** number (OTP, still establishes identity + anti-spam) + optional context note → admin review (J29), email in 48h. Read-only default keeps the fast path hijack-resistant.

### OnboardingSheet — minimal flow (V1, revised May 30)
The soft-auth sheet is trimmed to **Phone → OTP → Name** for every Guest trigger. Rationale: don't over-ask just to view a contact; defer everything that isn't "prove you're real" to the moment it's needed.

- **Zip / location:** **pre-filled** from the Welcome-screen choice, shown as an editable field. Uses the shared `LocationAutocomplete` control — user can type a city, a neighborhood, or a 5-digit ZIP (all resolve to a place we derive the zip from), or tap "Use current location". Displays the resolved place + zip ("Willow Glen · 94110"). Same control across Welcome / Quick Setup / Search / Profile.
- **Name:** single field with platform-native autofill — iOS `textContentType="name"`, Android `autofillHints="name"`, auto-capitalize words, default keyboard. ~70-85% of devices get a one-tap suggestion. Kept because it personalizes the welcome and powers social attribution later.
- **Contact sync:** removed from the sheet entirely. Fires later via the N8 Home banner / first social moment — NOT during the reveal/save/review task. The friend-match teaser converts better after the user has gotten value.
- **OTP** is the one non-negotiable step (identity primitive + spam guard). Keep a strong context line on step 1 ("Verify to see Mario's number") so the user understands why.
- Net: standard triggers go from a 4-step sheet to **3 steps**. Ledger (J16) still applies the Privacy Firewall (never asks contacts — moot now that sync is gone from all paths, but the principle stands).

### Logout (V1 — moved into scope May 30)
- "Log out" row on Onboarded/Pro Profile menu.
- Tapping → confirm step ("Log out of Home Circle?") to prevent accidental taps.
- On confirm → clears local session → returns to the **Welcome screen** (the one-time Welcome reappears here as the logged-out destination).
- Re-entry is via Sign in (J24.5), which restores full state (server retains everything).

### Friend-of-friend (Mutual Friend) tier
- Surfaced in V1.
- Bridge person named to viewer ONLY if bridge is in viewer's contacts.
- Voucher always anonymous.

### "Verified" → dropped for V1
- Section renamed "NEIGHBORHOOD PROS."
- **Licensed & Insured dropped entirely for V1.** Defer to V1.5 when automatic verification (license board API or 3rd-party verifier) is implemented; returns as a verified badge.

### Contact hash retention
- Server retains. Reinstall restores Fully Onboarded.
- Privacy copy / CCPA flow must surface this.

### Deactivation impact on existing content
Decision #16 nullifies `phone_hash` + PII on deactivate but retains `vouch_text` as public content. UX rule:

- **Attribution on retained reviews: anonymize to "A neighbor"** (no avatar, generic label).
- Sarah deactivates → her existing reviews still appear on Pro Profiles, but as **"A neighbor ★★★★★ 'great work'"** instead of "Sarah J. ★★★★★ 'great work'".
- Friend connection breaks naturally: `phone_hash` is nulled, so Marcus's contact-match lookup stops resolving to Sarah. No "Sarah J. (account closed)" zombie state needed.
- The anonymization falls out of the data model — not a special case to code. Display logic shows the generic label whenever the user record's identity fields are null.

**Sketch implication:** Review card layout has two attribution states:
- **Named** — avatar + display name (default)
- **Anonymized** — no avatar, "A neighbor" label (post-deactivation)

Draw both during the sketch session for the Reviews section.

### Catalog growth via Add a Pro
- Vouch flow & Save flow both can add new pros.
- "Add new pro" form: name, category, location, contact. Creates pro record as side effect.
- Pro records are visible (searchable) once they have at least one non-Private save or any vouch.

### Google Places integration

**Two integration flows in V1:**

#### Flow 1 — Location autocomplete
Used on: Welcome city picker, Search screen location override, Quick Setup zip, Onboarded Profile location setting.

- User types → Google Places autocomplete (filtered to cities/regions) → dropdown of matches → tap to select.
- Result becomes user's persisted location (per location-handling rules).
- Same component reused across all surfaces.
- **UX implication:** Welcome city picker (and similar) is a search-with-dropdown, not a static list.

#### Flow 2 — Pro name autocomplete (Add a Pro)
Used in: Add a Pro Composer, when adding a NEW pro (existing pros come from our catalog).

- User types pro name → Google Places autocomplete (location-biased, ~50mi radius around user's location) → dropdown of business matches.
- Tap selection → pre-fills the new-pro form with: name, category (mapped from Google types), address, phone.
- User can edit any pre-filled field before submitting.
- **"Can't find them?" fallback** below the dropdown → manual-entry path (name + category + phone + address fields).

**UX implication:** "Add new pro" form is NOT 4 separate input fields by default. It's:
1. Big search field: "Search for a business name…" → autocomplete dropdown
2. Selected state: pre-filled card showing name + category + address + phone, with edit affordance
3. Fallback link below: "Can't find them? Add manually →" → expands to 4-field form

#### Field usage (cost-conscious)

**Use (Basic + Contact tiers — cheap):**
- `place_id` (dedup key, stored on pro record)
- `name` (display)
- `types` (map to our category enum via mapping table; fall back to "Other")
- `formatted_address` + `address_components` (display + zip filter)
- `geometry` (lat/lng for distance calc + neighborhood matching)
- `formatted_phone_number`
- `business_status` (skip permanently-closed results)

**Don't use (Atmosphere tier — expensive, dilutes our value prop):**
- `rating`, `user_ratings_total`, `reviews` (our trust model is social, not Google's general public)
- `photos` (encourage user uploads; Google photos are expensive)
- `opening_hours` (out of V1)
- `price_level` (out of scope)
- `website` (defer to V1.5)

**Cost estimate:** Basic + Contact combo ≈ $17/1k requests. Need session token logic to bundle autocomplete + detail fetches.

#### Category mapping (Google types → our enum)

Build a mapping table:

| Google `type` | Our category |
|---|---|
| `plumber` | Plumber |
| `electrician` | Electrician |
| `roofing_contractor` | Roofer |
| `painter` | Painter |
| `general_contractor` | Handyman |
| `house_cleaning_service` | Cleaning |
| `gardener` / `landscaper` | Landscaping |
| `air_conditioning_contractor` (if present) | HVAC |
| (everything else) | Pre-select "Other"; show category dropdown so user can pick |

If category can't be mapped: composer pre-fills name/address/phone but leaves category empty for the user to select before submit.

#### Deduplication strategy

- **Google-sourced pros:** dedup by `google_place_id` (column on pros table). Two users add Mario's via Google → same `place_id` → same record.
- **Manual-entry pros:** dedup by `(normalized_name, normalized_phone)`. Same Joe Handyman with same phone → same record. Same name + different phone → two records (could be two Joes).
- Future migration: if a manual pro later gets matched to a `place_id` (e.g., the pro claims it), merge the records.

#### Abuse / spam prevention (V1 minimum)

- Rate-limit manual pro adds per user per day (suggested: 5/day).
- Social-graph filtering naturally suppresses spam: a fake pro saved Private by one user appears in no one else's feed/search.
- Don't over-engineer for V1; revisit if abuse appears.

#### V1 components that use Google Places

- `LocationAutocomplete` molecular — text input + dropdown, filtered to cities
- `ProAutocomplete` molecular — text input + dropdown, location-biased, with category-enriched suggestions and a manual-entry fallback link
- Both share a Google Places hook with session token handling

---

## Final Journey List

**Counts:** ~30 journeys at Granularity A. Estimated ~150 phone-frames across all diagrams referencing ~35-40 unique screen-states.

**Scope tags are authoritative on THIS list.** Every row carries **[v1]** or **[v1.1]** (or a split). Read this list alone and you build the right thing — do not infer scope from the diagrams. Full rationale in [Scope Tiers](#scope-tiers-v1--v11--resolved-jun-27-closes-topic-5).

### First-touch / Welcome
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J0** | First-touch Welcome | **v1** | First install only. Branches: Browse (Guest) or Sign in (returning). |
| **J0.5** | Change city via Welcome split-button | **v1** | IP default → tap right → picker → confirm. |

### Discovery
| # | Journey | Scope | State variants |
|---|---|---|---|
| **J10a** | Browse Home feed | **v1** | All 4 (data tier varies) |
| **J10b** | Search for a pro | **v1** | All 4. Two entry paths: search bar OR category chip. |

### Core product loops
| # | Journey | Scope | State variants |
|---|---|---|---|
| **J1** | Find & contact a pro (discovery → Pro Profile → call/text) | **v1** | All 4 (Guest = via sheet at call) |
| **J2** | Save a pro (heart tap → instant, Circle default + toast) | **v1** | All 4 (Guest = via sheet) |
| **J2a** | Change save visibility on Saved tab | **v1** | Auth, Onb |
| **J3** | Browse Saved list | **v1** | Auth, Onb |
| **J4** | Add a review to a saved pro | **v1** | Auth, Onb |
| **J5** | Add a Contractor from FAB (composer; new pros + reviews + photos) | **v1** | All 4 (Guest = via sheet) |
| **J6** | Edit / delete own save or review | **v1** | Auth, Onb |
| **J7** | Log a home maintenance entry | **v1** (core CRUD) · warranty + receipt-photo fields **v1.1** | Guest (no-sync), Auth, Onb |
| **J8** | Manage Ledger entries | **v1** | Auth, Onb |
| **J9** | Refer / invite friends | **v1 = dumb share link only** (no attribution) · tracked/attributed refer **v1.1** | All 4 |
| **J11** | Manage own profile | **v1** | Auth, Onb |
| **J12** | ★ Sync contacts (the conversion / hero) | **v1** | Auth → Onb. Drawn richly in ★ Spine, not as a Family-G row. |
| **J31** | View a reviewer's / friend's profile | **v1** | Onb; universal-tappable, visibility-scoped |

### Guest acquisition (sheet-triggered)
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J13** | Guest unlocks contact (N5) | **v1** | Sheet: Phone → OTP → Name. Covers Call + Text. **Result UNLOCKS the contact (PP-2); never auto-dials** (Principle 12). |
| **J14** | Guest saves a pro (N2/N6) | **v1** | Sheet: Phone → OTP → Name. Save auto-completes (silent/reversible). |
| **J15** | Guest adds a review (N7) | **v1** | Sheet: Phone → OTP → Name → composer |
| **J16** | Guest logs Ledger (N3) ⚠ Privacy Firewall | **v1** | Sheet: Phone → OTP → Name (sync never applied) |
| **J17** | Guest signs up from Profile (N4) | **v1** | Sheet: Phone → OTP → Name |
| **J18** | Guest refers (FAB Refer) | **v1.1** | Depends on tracked refer (J9 v1.1). v1 path = the dumb share link only. |
| **J19** | Deep link to Pro Profile (Guest) | **v1.1** | Universal-links infra deferred. In-app nav to any Pro Profile stays v1; shared links open to Welcome/Home. Revisit-trigger: shared-link/SEO traffic becomes a channel. |
| **J20** | Referral link signup | **v1.1** | Can't function without deep linking (J19) — deferred by dependency. |

### Pro role — **ENTIRE FAMILY v1.1** (ops seeds pro data until v1.5; consumer-viewable Pro Profile stays v1)
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J21** | Claim a listing (auto-verify) | **v1.1** | OTP-match → Edit Pro Profile |
| **J22** | Edit Pro profile | **v1.1** | Description, photos |
| **J23** | Pro-targeted entry | **v1.1** | Deep link to own listing → claim banner |

### Returning user / re-auth
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J24** | Welcome Back / partial recovery (decision #15) | **v1** | Bailed mid-onboarding → resume. Result UNLOCKS, never auto-dials. |
| **J24.5** | Returning user re-auth via Sign in | **v1** | Welcome → Sign in → full-page phone+OTP → restored |
| **J30** | Re-auth with expired OTP window (>30d) | **v1** | Forced re-OTP |

### Edge / recovery
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J25** | Permission denied → re-prompt 7d | **v1** | Branch of J12/J13 |
| **J26** | OTP rate-limited (3/24h) | **v1** | Failure state |
| **J27** | CCPA deactivate (decision #16) | **v1** | OTP-verify deletion (compliance) |
| **J28** | No-match empty cascade (Onb on Home) | **v1** | 1st Circle → Mutual Friend → Neighborhood |
| **J29** | Pro claim conflict → admin review | **v1.1** | Rides with Pro Role (claim is v1.1) |
| **J-gate** | Market gating (unsupported area → waitlist) | **v1** | "Coming soon" + email + share. The primary cold-market state. |

### Trust & Safety (moderation) — **v1 (App Store 1.2 launch gate)**
| # | Journey | Scope | Notes |
|---|---|---|---|
| **J-report** | Report a review/photo | **v1** | ⋯ → reason → 24h SLA confirm |
| **J-block** | Block a user | **v1** | Mutual invisibility, undoable |
| **J-contact** | Report a problem (published contact) | **v1** | safety@ + 24h SLA, in Profile legal zone |

### Out of V1 scope (confirmed)
- New device / number change (handle via support)
- Push notifications, vouch replies
- Pro Dashboard analytics, Pro hours/services editing
- Multi-home support

---

## Scope Tiers (v1 / v1.1) — RESOLVED Jun 27 (closes topic 5)

Framework: solo eng, ~20mo runway, $2k/mo, density thesis, pre-seeded markets. v1 = create/consume reviews in a pre-seeded market + auth + compliance. Cuts sourced from strategic-plan.md / decision-log.md.

### v1 — kept (the spine + J31 + photos)
Welcome/auth → Quick Setup → **sync (the hero)** → Home/Search → Pro Profile → Save → text-and-stars Review (**+ photos**) → basic Ledger → market gating. Plus:
- **J31 Friend Profile** — KEPT (universal-tappable, visibility-scoped, flat marked list). Rides on the friend-graph/sync infra that's already MVP-must (J12), so no new dependency.
- **Photos on reviews** — KEPT with **service-based moderation** (see below). Avatars stay initials; pro-profile photos are ops-seeded.
- **Report / Block / Contact UI** — NEW MVP-must surface. App Store Guideline 1.2 gate (text reviews are UGC). Small UI: ⋯ → Report on reviews/photos/pros/users, Block user, published contact + ~24h response. NOT YET SKETCHED — needs one.
- Correctness edges kept: J26 OTP rate-limit, J27 CCPA deactivate, J28 cascade, J30 re-auth, J24/J24.5 recovery, J25 permission-denied.

### Photo moderation posture (v1)
- One managed service (image + text, incl. **CSAM hash-match** — legally mandatory the moment you host user photos, 18 U.S.C. 2258A → NCMEC). Cheaper than bespoke AND the thing that makes photos legally shippable.
- **Synchronous scan at upload → allow/reject inline.** No dual-visibility "pending" state.
- **EXIF strip on upload** (day one) — before/after home photos must not leak GPS.
- Same integration covers the text-review UGC requirement.

### v1.1 — deferred (8 journeys + trims)
- **Pro Role (Bucket 1):** J21a, J21b, J22, J23, J24 + "Claim your business" CTA. Pre-seeded markets → ops seeds/corrects pro data until v1.5. Consumer-viewable Pro Profile STAYS (claimed/unclaimed display, reviews, Call/Text); only pro self-authoring defers.
- **Deep linking (Bucket 3):** J19 deep-link to Pro Profile. **Revisit-trigger:** un-defer the moment shared-link / SEO traffic becomes a channel. In-app nav to any Pro Profile stays; shared links open to Welcome/Home in v1.
- **Tracked Refer (Bucket 4):** J9, J18 (codes + attribution + "N friends joined" badge). **Keep the dumb crumb:** a no-attribution native "share the app" link is near-free and a density product wants some pull-in path.
- **Moderation polish (Bucket 5 residue):** dual-visibility pending state (only needed if service is async), mid-upload resume. J5b sketch TRIMS to v1 reactive version (picker → upload → gallery), not deleted.
- **Ledger enrichments (Bucket 6):** warranty free-text field + receipt photos. Core Ledger CRUD (what/who/date/cost) stays. (Receipt-photo cut is forced by the photo posture anyway.)

### Knock-ons (bank these)
1. **PP-5 disappears** — no self-claim → no pro-viewing-own-listing state. Viewer matrix drops 5→4 (Guest / not-saved / saved / reviewed). Claimed-badge *display* stays (ops claims); "Edit Pro Profile" self-view goes.
2. **User uploads = review photos only.** Avatars stay initials (product choice). Pro-profile photos ops-seeded. Pro Profile gallery in v1 = ops pro photos + user review photos.
3. **Tab bar stays 4** (Home/Saved/Ledger/Profile) — Ledger kept minimal, so the 4→3 simplification doesn't apply.
4. **default=Circle is a privacy invariant** (see Friend Profile section) — never drift to Neighborhood.

### Sketch debt created by topic 5 — ALL RESOLVED Jun 27
- ~~Report/Block/Contact UI~~ ✅ Sketched (Family K).
- ~~J31 sketch lags notes~~ ✅ Universal-tappable, stranger-neutral header, flat marked-list body (chips removed).
- ~~J5b sketch trims to v1 reactive~~ ✅ Picker → upload+scan → gallery; pending/rejected/resume marked v1.1.
- ~~PP-5 removable~~ ✅ Kept but marked v1.1 (Pro Role family banner + PP-5 label).

### Out of V1 scope (confirmed)

### Error / degraded states — note-only (not sketched, captured for build)
These are real states the build must handle but don't warrant journey diagrams:
- **Offline / no connection** — feed/search/call can't load; show retry affordance.
- **Pro deleted or merged** — opening a stale saved listing → 404 / "this pro was merged" → route to merged record if known.
- **Google Places down** — pro autocomplete + location picker degrade; fall back to manual entry (the "add manually" path already in the composer).
- **Server error (500)** on a key action (save/review/OTP-send non-rate-limit) — inline toast + retry.
- **Quota states** — manual-pro-add cap (5/day) and save spam cap (10 soft / 25 hard) → "you've added a lot today, try tomorrow."
- **Account edge cases (support-handled)** — new phone number, recycled number, no-SMS-capable (landline/VOIP) number, banned/moderated user. All route to "contact support", no in-app self-serve in V1.

---

## Implementation Plan Integration

All gaps from the journey-vs-plan cross-reference are integrated into the day-by-day plan via **PATCHES 1-23** (May 25 decision-log entry).

**Reference docs:**
- `V1_PLAN_PATCHES.md` — patch-by-patch mapping (Tier 1-3, ~20 patches)
- Day-by-day plan (Days 1-37, patch-integrated May 25) — current state of truth
- `D17_REVISED.md` — the Day 17 progressive disclosure restructure

No outstanding plan-amendment work remains. Sketches can proceed.

---

## Sketch State Backlog

States to explicitly sketch alongside the journey diagrams. Not separate journeys — variants of existing screens.

### Empty / sparse states
- **Home Onboarded, zero circle activity** — no friend has vouched/saved yet. Activity Feed cascades to Neighborhood tier **implicitly** (no "your circle is quiet" apology — it invents a problem and frames Home as broken). Just the neighborhood section + a positive "Grow your circle" nudge.

### Market gating (launch market-by-market) — RESOLVED Jun 20
Home Circle launches **market-by-market**, not everywhere-at-once. Trust networks are worthless at thin density (one user + one pro = no vouches, no matches, no one to share with), so we gate by **metro** and pre-seed a market before opening it.

- **Unsupported market** → "Coming to [City] soon" + email capture → "You're on the list" + **Share Home Circle** (turns a gated user into a recruiter for their own market). Per-market waitlist signals where to launch next.
- **Gate triggers** at two entry points: Welcome (detected/picked city is unsupported) and Search (searching an unsupported location). The Welcome "Browse Pros in [city]" split-button needs an unsupported-city path.
- **"Be the first to add a pro" (old cold-neighborhood) is DEMOTED to an internal seeding tool** — not a user-facing state. Markets only open once pre-seeded with density, so real users never see an empty catalog. Requires an ops-side seeding plan per market (scrape + invite local pros) since gating removes the organic-add path.
- Market = metro/city level (neighborhood-level gating too granular, would fragment the waitlist).

#### ⚠ LAUNCH DEPENDENCY — seed REVIEWS, not listings (the #1 GTM precondition)
The single biggest non-design risk. Market gating + Pro Role defer means the supply side is a **manual ops process with no in-app organic pro-add at cold start.** The trap: if ops seeds pro *listings* but reviews only come from real users, a freshly-opened market is full of pros each showing "Be the first to review [Pro]" — **the exact emptiness gating was meant to prevent.**

- **Density that makes a market launchable is REVIEW density, not listing density.** A market is "ready" only when seeded pros carry real reviews across the common categories — not when the listings merely exist.
- **The "be the first to review" empty state is the dominant first-week-in-market state** unless seeding covers it. It's not an edge case at launch.
- **Operational precondition:** the family/friends ground team must actually *use and review* local pros (hit a threshold-per-category) **before** a market flips live. This is a go-to-market gate, bigger than any screen.
- This is the thing to write into the GTM/ops plan, not just the product spec.
- **Pro Profile, zero reviews** — copy: "Be the first to review [Pro Name]" per Empty State Inventory.
- **Saved tab, 1-2 saves** — near-empty. Must look intentional, not broken. Harder than truly-empty zero-state.
- **Friend Profile, zero-visible** — copy: "Nothing shared yet" (drawn). NOTE: collapses all-private + truly-empty into one indistinguishable state by design (don't reveal a hidden total); the older "Sarah's saves are private" wording is superseded.
- **Search Results, zero matches** — catalog-driven empty: "No one in your area has reviewed a [Category] yet. Know a great [Category]? Add them →"
- **Saved tab, fully empty (0 saves)** — instructional empty: "Keep the pros you find in one place, ready when you need them."
- **Ledger tab, fully empty** — instructional empty (per Empty State Inventory).

### Pro Profile state variants (5 distinct sketches)
PP-1 through PP-5 per the viewer-state matrix under [Pro Profile structure](#pro-profile-structure). Each is its own sketch.

Note PP-5 specifics: no Call/Text buttons (pro doesn't call themselves); single "Edit Pro Profile" primary action; phone displayed in header but not a tap target; top-nav heart replaced with Share. Tier teaser still shows (Pro can see their strongest social proof).

### Friend Profile header states (NEW)
Privacy rule: only surface what the friend shared; never "N of M". 2 states:
- **Has shared** — "Sarah shares 5 pros with you"
- **Nothing shared** — "Nothing shared with you yet" (hidden and truly-empty collapse into one indistinguishable state by design)

Per-category variants reuse the same logic scoped to chip ("Sarah's Plumber saves are private" etc.).

### ReviewSheet disabled / unrated state
When user has typed review text but not tapped a star, submit button is disabled. Surface helper text under the stars: **"Tap a star to add your review."** Sketch this state explicitly as part of the ReviewSheet sub-flow.

### OTP rate-limit recovery (J26)
Full-screen state shown after 3rd OTP attempt in 24h. Copy: "Too many attempts. Try again in 4 hours." + live countdown timer + "Contact support" link. Only way out of the failure that isn't user-fixable.

### ReviewSheet sub-flow (photo upload — v1 reactive, RESOLVED Jun 27)
Within J4 / J5, the ReviewSheet has an optional photo-upload. **v1 reactive posture:**
- Picker → multi-select up to 10
- Upload → **synchronous managed-service scan** (CSAM hash-match + NSFW) decides allow/reject inline
- Passed → photo in the review gallery. Failed → "couldn't use that photo," rest of review still posts.
- **EXIF stripped on upload** (day one — no GPS leak from home before/after photos)
- No "pending" dual-visibility state (service decides at upload)

**Deferred to v1.1:** async moderation-pending dual-visibility, mid-upload app-kill resume — only if the service can't decide fast enough. Avatars stay initials; pro photos ops-seeded.

**Sketched: J5b (Family F), trimmed to the v1 reactive 3-step (picker → upload+scan → gallery).**

### Photo moderation — v1 reactive posture (REVISED Jun 27, supersedes Jun 20)
Three upload surfaces, but **only review photos are user-uploaded in v1.** All run through the same managed moderation service (synchronous scan at upload — CSAM hash-match + NSFW — allow/reject inline; no pending state).

| Surface | v1 status | On reject |
|---|---|---|
| **Avatar** | NOT uploadable in v1 — stays initials (lettered circle). Upload deferred. | n/a (always initials) |
| **Pro business photos** | Ops-seeded (no pro self-edit in v1, per Pro Role defer) | n/a (ops controls) |
| **Review photos** | **User-uploaded in v1** (J5b) | "Couldn't use that photo," rest of review still posts |

Build once: the service + report/block layer covers review photos now; avatar + pro-photo upload reference the same pipeline when they land (v1.5). EXIF stripped on upload (day one). See Scope Tiers (photo moderation posture) for the full v1/v1.1 split.

### My Reviews sub-page — ✅ SKETCHED (Jun 27)
Reached from Profile → "My Reviews". Reviews only (saves live in the Saved tab — no duplication). Own review rows with ⋯ edit/delete. One screen, sketched in Family G (`MyActivity` component).

### Settings — FOLD INTO PROFILE for MVP (no separate page)
Decision (May 30): do NOT build a separate Settings page for MVP. The gear icon over-promises a "settings world" that doesn't exist (no notifications per Principle 11, no theme, no account editing beyond deactivate). Instead:
- Profile menu gets a visual divider separating daily-use rows (Invite, My Activity, Claim) from a bottom **danger/legal zone**: Privacy Policy · Terms of Service · Deactivate account (styled destructive/muted, decision #16 CCPA) · Log out.
- Deactivate stays visually separated + distinctly styled so it can't be fat-fingered.
- Drop the Settings ⚙ icon from the Profile header.
- **Sketch needed:** updated Profile menu with the danger-zone divider + the Deactivate confirm/OTP flow (overlaps J27 in Edge cases family).

#### RESOLVED (Jun 20) — Profile consolidated, J27 repointed
`ProfileOnboarded` is now the single consolidated Profile: avatar + masked phone + 3-stat row + daily rows (Invite · My Reviews · Claim) + dashed divider + danger/legal zone (Privacy · Terms · Log out · Deactivate, red). No Settings ⚙. `ProfileDangerZone` placeholder retired; J27 (deactivate) and J11b (logout) both use the real `ProfileOnboarded`.

### Card attribution variants
Each pro card / ProActivityRow has two attribution states:
- **Named** — avatar + display name (default).
- **Anonymized** — "A neighbor" with no avatar (post-deactivation per Deactivation rule above).

Draw both during sketches; same component, two render modes.

### Heart state
- Empty (not saved)
- Filled (saved)
- Loading (during save API call / `/me/restore` latency — optimistic UI; engineering decision, flag in sketch annotations as "TBD post-sketch")

---

## Next Steps

Sketches in progress. Scope locked as **Hybrid** (Tier 1 core journeys fully sketched, Tier 2/3 as compact stub diagrams), grouped by family on one canvas. Day labels baked into each phone frame.

Current state: **Family A (Welcome), Family B (Re-auth), Family C (Referral)** drawn. Next up: **Discovery family (J10a Home browse, J10b Search)**, then Core Loops.

See `Home Circle V1 Journeys.html` for the current sketch state.

---

## Confirmed UI / Surface Details (from screenshots)

### Welcome screen
- Wordmark: "Home Circle"
- Hero illustration: two neighbors talking over a fence
- Headline: "Find contractors trusted by your friends"
- Subhead: "Real recommendations from your circle of neighbors, family, and friends"
- City chip: "San Jose ▾" — tappable, split-button right side
- Primary button: "Browse Pros in San Jose"
- Tertiary link: "Already have an account? Sign in"

### Home tab — Guest
- Search bar: "Find a pro your neighbors trust" + filter icon
- Category chips: Plumber, Electrician, Landscaping, ...
- N1 banner (green): "These pros are trusted by your neighbors. Sign up to see who your friends recommend →"
- "Recent activity" section (was "Recent Vouches"): vertical list, mixed saves + vouches
  - Each card: pro icon, name, neighborhood + category, tier badge, attribution (Saved by / ★★★★★ + review), save heart
- "See more →" link
- Bottom nav: Home / Saved / Ledger / Profile
- FAB bottom-right (blue, +)

### Search screen
- Back arrow
- Search input + X clear
- Location dropdown: "San Jose ▾"
- Category chips (one selectable)
- iOS keyboard up

### Search Results
- Back arrow + query chip: "Plumber, Willow Glen"
- **FROM YOUR CIRCLE** section (1ST CIRCLE + MUTUAL FRIEND badges)
- **NEIGHBORHOOD PROS** section (renamed from "Verified...")
- Each card: icon, name, category, rating (if present), save heart
