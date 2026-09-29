# Home Circle V1 Specification

**Status:** Locked, May 8 2026
**Target launch:** Sept-Oct 2026 (solo build; pull in if work completes ahead of schedule)
**Scope:** First public release with full progressive-disclosure architecture (no scope cuts)

This document is the canonical source of truth for v1 product and engineering scope. Any change to v1 scope should be reflected here AND in `decision-log.md`.

---

## Context

V1 is the first public-facing release of Home Circle. The architecture commits to progressive disclosure (Lite browsing → soft auth at action moments → contact sync as deferred conversion) rather than forced upfront signup. This optimizes for cold-acquisition conversion at scale and treats every action moment as a conversion opportunity.

Pre-MVP / V0 was considered as a friends-and-family beta with forced signup; the team committed to V1 directly with the understanding that the architecture is more robust and longer-lived even if launch takes longer than a stripped-down V0.

**Exit goals (unchanged):** $3M minimum, $10M target by EOY 2027, most likely path = strategic acquisition.

---

## User States

V1 supports four user states. State transitions are unidirectional (Guest → Auth → Fully Onboarded; Pro role is orthogonal and additive).

| State | Characteristics |
|---|---|
| **Guest** | Anonymous. IP-located city. No account. Can browse pros, view vouches with attribution, see locked phone/text on Pro Profile. |
| **Auth (No Sync)** | Phone verified via OTP. Name + Zip captured. Contacts NOT synced. Can call/text, save, vouch. Sees friend-shadow teasers. |
| **Fully Onboarded** | Auth + Contacts synced via client-side hashing. Friend match data populated. Sees actual friend names on cards and pro profiles. |
| **Pro (Claimed)** | Orthogonal role. Has claimed a business listing via OTP-match. Can edit description (500 chars) + photos (up to 10). Otherwise has standard homeowner experience. |

---

## Master Surface Matrix

| Surface | Guest | Auth (No Sync) | Fully Onboarded | Pro (Claimed) |
|---|---|---|---|---|
| **Home Feed** | Geographic-proof feed (recently vouched pros in IP zip, neighbor-attribution cards) • Top banner: "Sign up to see friend matches" | Shadow Matches ("3 may have used") with geographic-proof fallback • Inline nudge: "Connect contacts to see who" | Full Circle Feed prioritized by friend activity • No persistent nudge (growth/referral nudges instead) | Same as their homeowner state on home; sees own listing if it appears organically |
| **Search Results** | Geo-sorted by proximity • Phone/text masked on cards • Geographic teaser per result | Geo-sorted with friend-shadow badges • "X may know" teasers • Sync nudge per result | Trust-sorted (friend-vouched at top) • Friend names attribution on cards | Same as homeowner state; their own listing shows "Verified" badge to themselves |
| **Pro Profile** | All vouches visible with attribution • Phone/text LOCKED (tap → onboarding sheet) • Friend match teaser (client-side hash) • Geographic proof fallback | Phone/text REVEALED • All vouches visible • Friend match teaser remains as deferred sync prompt | Phone/text revealed • Friend match section populated with avatars + names • Inline-expand vouch rows • Edit/Delete affordance on own vouch | Same as homeowner state for OTHER pros' profiles • Own profile shows "Edit Profile" CTA → description + photos editor |
| **Save Action (heart tap)** | Triggers Onboarding Sheet (Steps 1-4) • Sync ask: primary • Save persists with Circle default after onboarding completes | Active immediately • Silent save with default visibility: **Circle** • Educational toast first 3-5 saves ("Saved to Circle (visible to friends) — Change") • Tap heart again = unsave | Same as Auth-no-sync (silent save, Circle default) • Toast simplifies after first few saves | N/A on own listing (cannot save self) • Normal on others' listings |
| **AddProComposer (Step 1 — save only, per May 25 progressive disclosure)** | Triggers Onboarding Sheet first • Then composer with VisibilitySelector (Private/Circle/Neighborhood, default Circle) — selector ALWAYS visible, no hide-on-review logic • Static "Save Contractor" CTA, no rating/review fields • Auto-chained to ReviewSheet Step 2 on save success | Active immediately • Same composer behavior with always-visible VisibilitySelector | Active • Same composer behavior | N/A on own listing • Normal on others' |
| **ReviewSheet (Step 2 — review only, NEW per May 25)** | Triggered via `addReview` OnboardingSheet variant; on completion, pro auto-saved (Circle default) + ReviewSheet auto-opens | Auto-shown after Step 1 save success; also reachable from Pro Profile "Add a review" CTA (auto-saves pro if not saved) + Saved tab "Add/Edit review" | Same as Auth | N/A on own listing • Normal on others' |
| **Saved Tab** | Locked → triggers Onboarding Sheet • Empty CTA: "Sign up to start your list" | List of saved pros • Per-row **VisibilityChip** (Private/Circle/Neighborhood, tap → picker bottom-sheet) • Vouched rows show Neighborhood chip greyed (locked by review presence) • No friend overlays • Soft sync nudge: "See if friends also saved" | Same as Auth + social proof: "Also saved by 3 friends" • No sync nudge | Same as homeowner state |
| **Ledger Tab** | Locked → triggers Onboarding Sheet (Steps 1-3 only) • **NO SYNC ASK (Privacy Firewall)** | Personal maintenance entries • FAB to add • **NO sync nudge ever** | Personal maintenance entries • Same as Auth | Same as homeowner state (no pro-specific Ledger features in v1) |
| **My Profile Tab** | TWO equal-weight CTAs: **"Join the Circle"** + **"Claim your business"** (PATCH 12) | Profile details (name, zip) • "Sync Contacts" primary CTA • "Invite friends" CTA • Empty My Activity link | Profile details • Circle size shown ("42 friends") • "Invite friends" referral CTA (PATCH 1) • My Activity link populated | Adds "Edit Pro Profile" link visible only to pro (description + photos editor — NO L&I per PATCH 14) |
| **Friend Profile (NEW per PATCH 4)** | N/A — guest has no friend graph (route renders "Sign in to see your friend's recommendations") | Limited view — sees neighborhood-visible saves & all vouches; circle-visible saves hidden | Full view: header (avatar + name + counter) + CategoryChipsRow + paginated mixed saves/vouches list. Server-side visibility filter enforces who sees what | Same as homeowner state |
| **Refer / Invite (PATCH 1)** | Via FAB Refer or invite link arrival; Onboarding Sheet with zip SKIPPED, sync hard-nudged | Via Profile tab or FAB Refer; ShareSheet pre-fills `homecircle.com/invite/[code]` | Same as Auth + InviteCTA visible on Saved tab empty state and Onboarded Home contextually | Same |
| **FAB: Add a Contractor** (label renamed from Vouch per PATCH 16, then "Add a Pro"→"Add a Contractor" Jul 3; component stays `AddProComposer`) | Tap → Onboarding Sheet (Steps 1-4 with sync) • Then AddProComposer for chosen pro | Direct entry to AddProComposer • Soft sync nudge after submit • Composer's pro autocomplete + "Add new pro" fallback (PATCH 20) | Direct entry to AddProComposer • No sync ask | Direct entry (cannot save/vouch own business) |
| **FAB: Log** | Tap → Onboarding Sheet (Steps 1-3 only, sync HIDDEN per Privacy Firewall) → Add Ledger Entry | Direct entry to Add Ledger Entry • No sync ask | Direct entry • No sync ask | Same |
| **FAB: Refer** | Tap → Onboarding Sheet (Steps 1-3, zip SKIPPED, sync hard-nudged) → ShareSheet | Direct entry to ShareSheet • Sync ask: primary (referrals work better with contacts) | Direct entry to ShareSheet | Same |

---

## The Unified Onboarding Sheet — State Machine

Single bottom sheet handling all soft auth flows. Step sequence varies by trigger.

| Step | Action | Triggers including this step |
|---|---|---|
| **1. Trigger** | Initiating action — sheet opens | All |
| **2. OTP** | Phone entry + SMS code verification | All |
| **3. Quick Setup** | Name + Zip code (Zip pre-filled from Lite session if available) | All EXCEPT Refer (Refer can skip Zip — location not needed for sharing) |
| **4. Sync Choice** | Friend Match Teaser as the hook + Sync Contacts (primary) / Skip (secondary) | Phone reveal, Save, Vouch, Profile signup, Refer, Cold-open • **NOT Ledger trigger (Privacy Firewall hides this step entirely)** |
| **5. Result** | Sheet dismisses • Underlying screen updates (phone unmasks, save fills, etc.) | All |

**Behavior on permission denied (contacts):** Proceed to Result anyway. User has earned the unmask via OTP+Quick Setup. Re-prompt sync later via teasers + Home banner.

**Partial Safety Net:** If user bails between steps, next FAB tap resumes at the exact step they left. Welcome Back UI: "Welcome back, +1 (415) ●●●-0184 — let's finish setting up". OTP re-verification only required if verification window expired (30 days).

---

## Nudge Inventory

| # | Nudge | Triggers | Where | Outcome |
|---|---|---|---|---|
| N1 | "Sign up to see friend matches" | Guest opens Home | Top banner on Home (guest) | Soft auth flow |
| N2 | "Sign up to start your list" | Guest taps Saved | Empty state CTA | Soft auth (Save trigger) |
| N3 | "Sign up to track maintenance" | Guest taps Ledger | Empty state CTA | Soft auth (Ledger trigger — no sync) |
| N4 | "Join the Circle / Pro? Claim profile" | Guest taps Profile | Full-screen CTAs | Soft auth (general) |
| N5 | Locked phone/text on Pro Profile | Guest taps phone/text | Inline locked CTA | Soft auth (Phone/Text trigger) |
| N6 | Locked save heart | Guest taps save anywhere | Inline | Soft auth (Save trigger) |
| N7 | Add-a-Pro FAB locked (renamed per PATCH 16) | Guest taps Add-a-Pro FAB | Sheet opens | Soft auth (Save/Vouch trigger) |
| N8 | "Connect contacts to see who" | Auth-no-contacts on Home | Inline card | Sync contacts flow |
| N9 | Friend match teaser (deferred sync) | Auth-no-contacts on Pro Profile | Inline within profile | Sync contacts flow |
| N10 | "Sync to see who friends recommend" | Auth-no-contacts on Search | Per-result chip | Sync contacts flow |
| N11 | "Sync Contacts" on Profile tab | Auth-no-contacts on Profile | Primary CTA | Sync contacts flow |
| N12 | Welcome toast | Just completed onboarding | Top of next screen | Auto-dismiss (~3s) |
| N13 | Re-prompt after permission denial | Contact denial → next session | Home banner (with 7-day cooldown via localStorage) | Re-prompt sync flow |
| N14 | "Invite more friends" growth nudge | Fully onboarded with small circle | Profile + Home contextual | Share / Refer flow |

---

## Teaser Logic — Three-Tier Cascade (revised per PATCH 11)

Applied across Home cards, Pro Profile, Search Results. **Backend resolver returns exactly ONE tier per pro per viewer**, priority order top-down. If no tier applies, render nothing (no generic-fallback nudge — that lives elsewhere via N13).

| Tier | Condition | Copy template | Computed via |
|---|---|---|---|
| **1 — Network Match** | ≥1 vouch from someone in viewer's 1st-circle (synced contact match) | "✨ 3 people you may know vouched for this pro — sync to see who" (or "Sarah K. and Tom R. vouched" if synced) | Reverse-indexed phone_hash → vouch_id lookup |
| **2 — Mutual Friend** (NEW per PATCH 11) | Network=0 AND ≥1 vouch from a friend-of-friend (voucher is in someone-in-viewer's-circle's circle) | "Marcus C.'s friend reviewed this pro." | Two-hop join on user_contacts hash sets |
| **3 — Neighborhood** | Network=0 AND MutualFriend=0 AND ≥5 vouches in viewer's zip | "5 neighbors in Willow Glen vouched for this pro." | Aggregated count: vouches WHERE pro_id = X AND voucher_zip = user_zip |
| (no tier) | All three conditions=0 | Render nothing (no generic "Sync contacts" nudge here — N13 handles persistent sync re-prompting elsewhere) | — |

**Pre-patch state (May 8 v1 spec):** had 3 tiers: Network Match / Geographic Proof / Generic Fallback. The May 25 patch (a) renamed Geographic Proof → Neighborhood, (b) inserted Mutual Friend as a distinct tier between Network and Neighborhood (previously it was only a card-level tier badge, not a teaser variant), and (c) dropped Generic Fallback entirely (render-nothing on no-signal).

---

## Save + Review Visibility Rules

**Resolved Sept 1 2026 — list-level.** Supersedes the May 25 per-save model (three levels, `visibility` column on the save row). See `decision-log.md` for the rationale.

### The model

| Concern | Where it lives | Values | User-controlled |
|---|---|---|---|
| **Named save attribution** | `users.saves_visibility` | `circle` \| `private` | Yes — one setting, whole list |
| **Review publicness** | implicit in `rating IS NOT NULL` | always public, always bylined | No |
| **Anonymous aggregates** | derived | always counted | No |

Three rules, and they are independent:

1. **`saves_visibility` governs named save attribution only.** `circle` — people whose phone hash is in your contact set see "Saved by [name]". `private` — nobody sees your name against a save, ever.
2. **Reviews are always public and always keep their byline**, regardless of `saves_visibility`. Writing a review is a public endorsement; it is a different act from saving. A user can have a fully private saved list and a publicly bylined review on the same pro.
3. **Anonymous aggregates always count every save, private included** — but never name anyone. This is what keeps neighbourhood social proof working ("Used by 8 neighbors") without leaking who.

### Why the tier axis is unaffected

`ProCard`'s `tier = 1stCircle | 2ndCircle | Neighborhood` describes **the viewer's relationship to the person who saved or reviewed**. It is not a visibility setting and did not change. Dropping `Neighborhood` as a *visibility level* has no effect on `Neighborhood` as a *tier*.

### Backend queries

Server-side filtering is the security boundary. Never trust the client to filter private saves.

- **Friend feed:** `WHERE save.user_id IN viewer_circle AND owner.saves_visibility = 'circle'`
- **Pro profile → Reviews:** `WHERE save.pro_id = X AND save.rating IS NOT NULL` — no visibility filter, reviews are always public
- **Pro profile → "Also saved by" (named):** `WHERE save.pro_id = X AND owner.saves_visibility = 'circle' AND save.user_id IN viewer_circle`
- **Pro profile → aggregate count (anonymous):** `WHERE save.pro_id = X` — no visibility filter; private saves count, they are simply never named
- **Search ranking:** reviews always counted; saves counted in aggregate regardless of visibility
- **Friend profile listing:** `WHERE owner.saves_visibility = 'circle' AND viewer IN owner_circle`, plus reviews unconditionally

### Copy

- Setting, private: **"No one can see your saved contractors."**
- Setting, circle: **"You and your friends can see each other's saved lists."**

Both are list-level statements, which is now literally true. The subline describes *name* visibility, not existence — consistent with private saves still counting in aggregates.

### Two-step Add-a-Pro flow

Unchanged apart from the visibility selector.

- **Step 1 — AddProComposer:** pro search/select + "Save Contractor". **No visibility selector** — the list-level setting already governs it.
- **Step 2 — ReviewSheet** (auto-shown after Step 1, reusable from Pro Profile and Saved): rating + review text + optional photo + "Add review" / "Not yet". Writes to the same row; does not touch visibility.

**Heart-tap save:** silent save, no composer, no sheet. Educational toast on the first 3–5 saves ("Saved · visible to your Circle — Change"); simplifies to "Saved" after. The "Change" action opens the **account-level** saves-visibility setting, not a per-pro picker.

### Edge cases

- **User adds a review to an existing save:** the row gains `rating` / `review_text` / `reviewed_at`. Visibility is untouched. The review is public and bylined even if the list is private — the ReviewSheet says so explicitly.
- **Private list + public review:** a friend visiting the pro's profile *will* see the review with attribution. That is how reviews work. `private` means "don't attach my name to my saves", not "no one can ever discover any connection".
- **Re-sync:** when a non-synced user later syncs contacts, `saves_visibility` is unchanged; the friend-graph join simply starts firing.

---

## Empty State Inventory

| Surface | Empty conditions | Strategy |
|---|---|---|
| Home (auth, no contacts) | No vouches in network | Cascade: Friends → Friends-of-friends → Geographic proof (neighbors in your zip) |
| Home (auth, fully onboarded but small circle) | Circle exists but hasn't vouched | "Refer friends to grow your circle" + Geographic proof (neighbors in your zip) |
| Saved (any state, empty list) | No saves | Instructional: skeleton UI + "Tap the heart on any pro to save them here" |
| Ledger (no entries) | No log entries | Instructional: illustration + "Track repairs and warranties — tap + to add your first entry" |
| Profile → My Activity (none yet — renamed from My Vouches per PATCH 16) | Empty list | "You haven't saved or reviewed any pros yet — find a pro from Home" |
| Pro Profile (no vouches) | Pro has no vouches yet | "Be the first to vouch for [Pro Name]" |
| Search Results (no matches) | No pros in category/zip | **Per PATCH 19:** "No one in your area has vouched for [Category] yet." + CTA: "Know a great [Category]? Add them →" opens AddProComposer with category pre-filled |
| Pro Profile (unclaimed) | is_claimed = false | Banner: "Are you [Pro Name]? Claim this listing" → OTP claim flow |
| Friend Profile (no visible content) | All target's saves Private OR no overlap with viewer's circle/zip | "Sarah's saves are private" (zero-visible state) |
| Friend Profile (no saves in selected category) | Target has saves but none in active filter | "Sarah hasn't saved any [Category] pros yet" |
| **Any surface, offline** | Device offline or request failed unrecoverably | `EmptyState tone=offline` — neutral grey tile, "You're offline / Check your connection and try again" + **Try again**. Never a toast. Added Sept 1 2026. |

---

## Engineering Decisions

| # | Area | Strategy | Rationale | Priority |
|---|---|---|---|---|
| 1 | **Authentication & RBAC** | Single users table with role enum: ['GUEST', 'MEMBER', 'PRO', 'ADMIN'] | Prevents duplicate accounts. Allows seamless homeowner→pro upgrade without new login. Role token refresh on role change. | Critical |
| 2 | **Contact Hashing (Privacy)** | HMAC-SHA256 with server-side secret salt, versioned (hash_v1, hash_v2) for rotation | Bare SHA-256 vulnerable to rainbow tables (10^10 phone numbers, GPU-precomputable for ~$200). HMAC + secret salt is the industry standard. Versioning enables safe salt rotation. | Critical |
| 3 | **Shadow Matching + Contact Hash Storage Protocol** | Reverse indexing: phone_hash → vouch_id lookup table. **Server stores user contact hash sets (pattern 1, not zero-knowledge pattern 2)** — required for cross-device portability + server-side join performance. Client hashes phone numbers locally (HMAC + secret salt), sends hash arrays only — raw phone numbers + contact names + contact photos NEVER leave device. Server stores hashes keyed to user_id. On signup, compute matches against user's contact hashes. **Server returns matches with HC display name + HC photo + phone_hash; client overlays contact-card name from local `{hash → name}` map if available.** Server-known data is always the fallback (so reinstall on new phone with no contact permission still renders attribution correctly using HC display names). | Delivers the "Aha! Sarah vouched here" moment immediately on sync. O(1) lookup at sync time. Server-side hash storage chosen May 25 over zero-knowledge alternative for cross-device durability — see decision-log. | Critical |
| 4 | **Pro Profile Locking** | Optimistic state locks: updatePro endpoint requires PRO role token AND pro_id ownership. is_claimed=true blocks community edits. | Prevents community vandalism of claimed listings. | Critical |
| 5 | **Privacy Firewall** | Contextual route guards: Ledger UI wrapped in hook that disables Step 4 (Sync) of the Onboarding Sheet | Hardcodes the "no sync ask in Ledger" rule architecturally so future features can't accidentally violate it. | Critical |
| 6 | **OTP Pro Claim** | Auto-verify when claimant_phone === business_phone in seed data. Otherwise flag for ADMIN review. | Eliminates ~80% of manual claim verification work. Reuses existing OTP infrastructure. | Critical |
| 7 | **Search Weighting** | Client-side re-ranker. Fetch top 200 results from PostGIS by proximity → re-rank on device by friend_vouch > self_save > friend_save > neighbor_vouch. Page 2+ falls back to pure proximity. | "Fat First Page" pattern: 90% of users never paginate, so 90% of value at 10% of complexity. Avoids recursive SQL joins. | High |
| 8 | **Text Search** | Postgres tsvector + tsquery for full-text search + pg_trgm extension for fuzzy match (typo tolerance) | Postgres handles 20k users easily. Trigram catches "Mariou" → "Mario" misspellings. | High |
| 9 | **Image Storage** | Supabase Storage for vouch photos + pro photos. Cloudflare-backed edge CDN, URL-based transformations (`?width=400&quality=80&format=webp`), signed upload URLs. AWS Rekognition called from a Supabase Edge Function on storage write for async content moderation. | Same Postgres project, one vendor, one bill. Async moderation pattern gives faster perceived upload UX than Cloudinary's blocking moderation. See May 12 "Image storage" entry in decision-log.md. | High |
| 10 | **OTP Provider + Abuse Defense** | Twilio Verify + exponential backoff + hard cap (3 OTPs per number per 24h) + US/Canada country-code allowlist at API layer | SMS pumping is a real threat — bots target premium-rate international numbers. Allowlist blocks 99%+ of pumping. Rate limits prevent legit-number abuse. | Critical |
| 11 | **Pro Edit Scope (V1)** | Description (max 500 chars) + Photos (max 10) only. Hours/phone/services/website edits via support email. | Captures 60-70% of pro dashboard value at ~1.5 weeks of build cost. Avoids feature creep. | High |
| 12 | **Pro Analytics** | DEFERRED to v1.5. Capture events but don't build pro-facing display in v1. | Real value comes when pros engage at volume. Build infrastructure when activation justifies it. | Deferred |
| 13 | **Nudge System** | Centralized useNudge hook / NudgeManager. Inputs: screen, userState, localDataDensity. Returns: nudgeType, teaserText. | Prevents scattered if/else conditionals. One place to reason about nudge logic. | High |
| 14 | **Skip Persistence** | Store sync-skip in localStorage with timestamp. Don't re-prompt within 7 days. | Prevents "annoying app" perception. Honors user choice without losing the conversion entirely. | High |
| 15 | **Partial Account Recovery** | If user completes OTP but bails before Quick Setup, persist their phone-verified state. Next FAB tap resumes at Step 3. Welcome Back UI shows masked phone. Re-OTP only if verification window (30d) expired. | Avoids losing high-intent users who hesitate at name/zip. | High |
| 16 | **CCPA Compliance — Deactivate Flow** | "Deactivate" button: nullify phone_hash + PII in users table. Retain vouch_text (public content). OTP-verify the deletion request to satisfy CCPA "verifiable consumer request" requirement. | Allows compliance while preserving the moat (vouch corpus). OTP verification is free since we already have phone numbers. | Critical |
| 17 | **Privacy Policy** | CCPA/CPRA-compliant policy. Discloses Shadow Match logic, contact hashing, retention periods, third-party sharing (Twilio, Supabase, AWS Rekognition, Google Places, PostHog, Sentry). | Legal requirement (CCPA, App Store, Play Store). Cannot ship without this. | Critical |
| 18 | **Image Upload Auth** | Signed Supabase Storage upload URLs scoped to user_id + bucket + content type, generated by `POST /vouch/photo/upload-url`. Server validates upload completion (storage webhook) before linking URL to vouch row. | Prevents arbitrary image uploads bypassing app. | High |
| 19 | **Save + Review Visibility — independent axes (revised May 25 progressive disclosure)** | One `saves` table with nullable `rating` + `review_text` + `visibility` columns. **`save_visibility`** (Private/Circle/Neighborhood, default Circle hardcoded) controls save-feed broadcast — user-controlled per save via VisibilityChip. **Review publicness** (implicit in `has_review`) is independent — reviews always render on pro profile + feed search ranking, regardless of `save_visibility`. Adding a review does NOT modify `save_visibility`. Progressive disclosure UI: AddProComposer (save only) → ReviewSheet (review only, optional Step 2). Server-side filter enforces save_visibility on save-attribution queries; reviews bypass the filter on pro-profile + search queries. | See "Save + Review Visibility Rules" section above. Earlier draft conflated the two axes; corrected May 25 — review publicness and save-feed visibility are different signals serving different user needs. Per-pro `save_visibility` preserves user agency for the divorce-lawyer-style edge case. | High |
| 20 | **Observability** | Sentry for error logging + basic performance metrics. Database query log for slow queries. | Cheap insurance. Catch bugs before users report them. | High |
| 21 | **Referral Attribution (PATCH 1)** | Per-user 6-char base62 referral code generated lazily, cached on user row. `referrals` attribution table with UNIQUE on `new_user_id` (first-attribution-wins). Deep-link signup attaches `?ref=[code]` query param to signup_completed payload. ShareSheet pre-fills outbound message with `homecircle.com/invite/[code]`. Special Welcome variant when arriving via referral link (friend pre-attached to incipient friend graph). | Core viral growth loop. Was entirely missing from May 8 plan — caught in journey-definition session May 25. | High |
| 22 | **Sign-in 3-branch flow + state restoration (PATCH 6)** | Post-OTP backend lookup branches by user state: A) Recognized+Onboarded → return full record + JWT, client calls `/me/restore` for saves+ledger+vouches+contact-matches → routes to Home or entry-context. B) Recognized+Partial (OTP-only) → routes to Quick Setup pre-filled. C) Not recognized → fresh signup. Server-side hash storage (Decision 3) means Branch A users restore full friend graph without re-syncing contacts. | Reinstall on new phone, multi-device login, and partial-account recovery all use the same backend lookup. Eliminates the "treat returning user as new" failure mode. | Critical |
| 23 | **Deep Linking (PATCH 5)** | iOS Associated Domains + `apple-app-site-association` at `homecircle.com/.well-known/`. Android app links manifest. Universal link routes: `/pro/:slug` (bypasses Welcome on cold launch) and `/invite/:code` (Referral Welcome variant). Deep link arrivals set session flag that skips Welcome on first launch. | Required for Refer/Invite viral mechanic to actually fire from outbound shares. Realistic budget 1 day (was 0.5 in initial PATCH 5; bumped for Apple's Associated Domains verification friction). | High |

---

## Quick Stats

- **User states:** 4 (Guest / Auth-No-Sync / Fully Onboarded / Pro-Claimed)
- **Primary screens:** 7 (Home / Saved / Ledger / Profile / Pro Profile / Search Results / **Friend Profile** — added per PATCH 4 May 25) × 4 states = 28 screen-state combinations
- **Sub-pages:** 7+ (Welcome, AddProComposer, Edit composer, **My Activity** (renamed from My Vouches), Add Ledger Entry, Edit Pro Profile, Sign-in, **Referral Welcome variant** — added per PATCH 1)
- **Onboarding sheet states:** 5 steps × ~6 trigger variants (including Refer with zip-skipped) × edge cases = ~32 sheet states
- **Distinct nudges:** 14
- **Teaser tiers:** 3 (revised May 25 per PATCH 11 — networkMatch / mutualFriend / neighborhood; was 3 pre-patch but with different composition)
- **Empty states:** ~10 distinct surfaces × variants per state (added Friend Profile zero-visible + Friend Profile category-empty)
- **FAB action variants:** 9 (3 actions × 3 user states) — note: FAB Vouch renamed to FAB Add-a-Pro per PATCH 16
- **Engineering decisions:** 23 (most Critical or High priority; added Decisions 21-23 May 25 for Referral, Sign-in 3-branch, Deep Linking)

**Estimated total v1 design surface: ~130-140 distinct states to define and build (was 115-125 pre-patch; +10-15 from Friend Profile, Refer, restructured Save/Vouch states, and visibility chip variants).**

---

## Out of Scope for V1 (Deferred to V1.5+)

- "Popular / Trending in Neighborhood" section on Home Feed (cut May 6, 2026 — dilutes trust-through-network value prop with generic popularity ranking; thin signal at MVP density. Revisit post-launch if trust-tier feed engagement is low)
- **Licensed & Insured pro self-attest field (PATCH 14, dropped May 25)** — self-attestation creates legal exposure without delivering trust signal value (pros will always claim L&I even if false). Deferred to v1.5 with actual verification (license-board API integration or 3rd-party verifier like Veriff/Persona); when re-introduced, returns as a verified BADGE earned through real verification, NOT a self-attest field.
- Pro Dashboard analytics ("12 viewed today")
- Pro Ledger Verification (two-sided notification flows)
- Pro hours / phone / services / website edit (handle via support email)
- Push notifications
- Vouch quality enforcement (text/photo required)
- Helpful votes on vouches
- Multi-home support per user
- Vouch reply / response flows
- Advanced Ledger features (service logging beyond Add Home, reminders, history)
- Web app surface (mobile only for v1)
- Pro response to vouches
- Friend-of-friend trust tier (computed but not surfaced as separate tier in v1)

---

## Timeline & Resourcing

> **Superseded Sept 1 2026.** The May-8 "LOCKED" timeline targeted mid-Sept to mid-Oct 2026 from a May 8 start. Actual progress: auth complete, ~15–20% of v1 built.
>
> **The active plan is `docs/v1-build-plan.md`** — six phases, Sept 1 → submission Fri Oct 23 2026, public launch early-to-mid Nov after Apple review.

**Resourcing:** Milind solo, AI-assisted, ~8h/day, 5 days/week. Weekends are buffer, not planned capacity.

**Standing triggers to reconsider scope:**
- Falling more than one phase behind: cut scope rather than pushing work forward. Pro-side role is already out; the next cut is review photos.
- A feature taking 2x estimate: reassess approach before continuing.
- Compliance (Phase 5) is a **submission gate**, not polish — it cannot be the thing that gets cut.

---

## Document Maintenance

- This document is the canonical v1 spec
- Changes to v1 scope must be reflected here AND in `decision-log.md`
- Design specifics (colors, typography, exact dimensions) live in `design-system.md` and reference this spec
- Day-level work breakdown lives in `v1-build-plan.md` (`sprint-tracker.md` is superseded)
- Error, loading and write-model semantics live in `arch.md` §Client Write Model
