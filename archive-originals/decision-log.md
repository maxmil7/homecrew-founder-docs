# Home Circle — Decision Log

| Date | Decision | Rationale | Status |
|------|----------|-----------|--------|
| Jul 18, 2026 | Sync nudge lives on **both Home and Saved/Circle** as **one shared banner with a single dismiss state** (`sync_nudge_dismissed_at`): dismiss on either hides both; both return together after the 7-day cooldown; banner-X and primer-sheet "Not now" write the same flag; nag-cap retires it after ~2–3 declines. Search's empty "FROM YOUR CIRCLE" prompt is **inline & exempt** (persists). This resolves the earlier "flag before adding" note on the Saved-tab nudge (`permission-primer-triggers.md` #3). | Both surfaces lack the friend-signal and benefit; two independent banners would nag twice / feel broken when dismissed on one and seen on the other. | Active |
| Jul 18, 2026 | ~~PARTIALLY SUPERSEDED Sept 1 2026: the visibility control and `showVisibility` property were removed (list-level visibility). The footer layout, tier badge placement, rating and heart all stand.~~ ProCard redesigned to a footer layout (all 8 variants, in place): tier badge moved from the title row to a **footer row** (bottom-left); title row now carries the pro name + aggregate **★ 4.9** rating (right); reviewed attribution switched to reviewer-name-forward without inline stars/quote ("Reviewed by **Elena R.** +4" / "Reviewed by **Marcus C.**'s friend" / "Reviewed by 5 neighbors in **Willow Glen**"); heart moved to footer-right; new **visibility control** (people + caret icon) sits left of the heart. Added a `showVisibility` BOOLEAN component property (default **false**). Saved tab renders `showVisibility=on` (badge · visibility · heart — you own the save, set its visibility inline); Search results / Home feed render it **off** (badge · heart). One shared component drives both — the Saved wrapper hack (external divider/foot) was removed. | Card should read as one unit; visibility is a Saved-only, per-pro affordance; reviewer-forward line avoids duplicating the title rating's stars. Matches the sketch. | Active |
| Jul 18, 2026 | Follow-up (not yet done): heart should render **filled** on the Saved tab (savedByViewer=true) — currently outline everywhere. Wire a `savedByViewer` boolean / filled-heart state next. | The heart is currently the not-saved outline on all screens; on Saved it should read as already-saved. | Open |
| Jul 18, 2026 | ~~SUPERSEDED Sept 1 2026 (list-level visibility — no per-pro control).~~ Saved-list cards: the per-pro visibility control ("Circle ⌄" = who can see you saved this) + overflow (⋯) live **inside** the card as a footer row below a hairline divider — not floating in the gutter below the card. Implemented at the `SavedRow` wrapper level (card chrome on the row, ProCard instance made transparent), so the shared ProCard component is unchanged and Home/Search/Pro Profile are unaffected. | Visibility is a per-pro attribute; it should visually belong to the card, not read as loose UI in the background. | Active |
| Jul 18, 2026 | Components/V1 board reorganized into 5 labeled sections: `00 Foundations — Tokens`, `01 Atoms`, `02 Molecules`, `03 Sheets & Organisms`, `04 App Screens — by Journey`. App Screens is journey-grouped, reading as one end-to-end flow: Onboarding (Phone→OTP→Name) → Contact Sync → Home/Activation → Discovery → Pro Profile → Saved → Ledger → Profile → Re-auth → Market Gating & Edge (onboarding + canonical screens folded in as the leading journey rows rather than a separate section). Legacy `Atoms / V1` + `Canonical Screens / V1` frames and stray icon scaffolding deleted; all components/screens absorbed. Component node IDs unchanged. | Prep for full v1 design review. | Active |
| Apr 2026 | Circle tab: All + Saved (both pro-centric) | Consistent mental model — user is always browsing pros, not reviews. Category filters inline on All handle the "by category" need without a third tab. | Active |
| Apr 2026 | Home feed: review/vouch-centric, Circle: pro-centric | Home answers "what's happening in my network" (social). Circle answers "who can I hire" (directory). Different purposes justify different card formats. | Active |
| Apr 2026 | All is default Circle tab (not Saved) | All always has content (even for new users). Saved may be empty early on. Most sessions are discovery ("I need a plumber") not retrieval. | Active |
| Apr 2026 | Hearts only, no bookmarks | Redundant with hearts. Removed bookmarks for consistency across all screens. | Active |
| Apr 2026 | Exit target: $3M min, $10M target, EOY 2027 | Personal obligations. $3M realistic for strategic acquisition. Timeline allows ~20 months. | Active |
| Apr 2026 | Hyperlocal launch: San Jose first | Milind's home base. Can leverage personal network. Homeowner-heavy suburbs. | Active |
| Apr 2026 | Pre-launch data seeding via $1K gift card campaign | Solves cold start. Collects real pro recommendations + homeowner contacts for launch day activation. | Active |
| Apr 2026 | Segmented control (not tabs) for All/Saved | Cleaner visual pattern. Black/3% bg container, white active tab with subtle shadow. Matches modern iOS patterns. | Active |
| Apr 24, 2026 | Circle All scope: friends + FoF + neighbors | Three tiers cover the trust spectrum without expanding to anonymous reviews. Tier badges (1ST CIRCLE / MUTUAL FRIEND / NEIGHBOR) communicate provenance on each card. | Active |
| Apr 24, 2026 | Circle All sort: trust tier first, recency within tier | Circle is intent-driven (user opens it to find a pro). Trust beats recency for hiring decisions. Group 1 = any 1st-circle vouch, Group 2 = any 2nd-circle, Group 3 = neighbor-only. | Active |
| Apr 24, 2026 | Multi-tier pros: highest tier wins | Simplest MVP rule. A pro with both friend and neighbor vouches lands in the friends group. Easy to explain, easy to implement. Can revisit with weighted scoring later. | Active |
| Apr 24, 2026 | Home feed sort: pure recency, all three tiers included | Home is discovery-driven (social feed). Recency matches the social-feed mental model. All tiers included to avoid empty feeds during cold-start when 1st/2nd circle is thin. | Active |
| Apr 24, 2026 | Tier inclusion in Home feed is a backend config | If neighbor vouches dominate the feed at scale, flip a flag — no app update needed. Watch metric: % of Home scroll impressions that are neighbor-tier. | Active |
| Apr 24, 2026 | Soft vouch decay: 6 months on Home, 18 months on Circle | Stale vouches lose signal value. Home is more time-sensitive (feed) so shorter window. Circle (directory) tolerates older vouches but still demotes them within their tier. Tunable. | Active |
| Apr 24, 2026 | Search is global, not per-tab; returns pros | One search entry point. Always returns pro cards with supporting vouch evidence ("Vouched by X + N others"). Existing search screens (1:419, 1:526, 1:648) already built for this. Search is intent-driven, so trust-first sort matches Circle. | Active |
| Apr 24, 2026 | Voucher names only shown when voucher is in viewer's 1st circle | Privacy: we only have a voucher's name in the viewer's UI if the viewer has them in their contacts. For mutual-friend and neighbor tier vouchers, the voucher's identity is never surfaced. ToS covers the disclosure. | Active |
| Apr 24, 2026 | Bridge attribution for MUTUAL FRIEND cards: "Vouched by [N] friend(s) of [Bridge Name]" | Names a 1st-circle bridge person (whom the viewer already knows from their contacts) instead of staying fully anonymous. The named portion gives an exact count of bridge-attributed vouches: "a friend of Sarah J." (1) or "2 friends of Sarah J." (2+). The actual voucher names are never surfaced. | Active |
| Apr 24, 2026 | "+ N others" semantics in vouch lines | "+ N others" represents all additional vouches NOT covered by the named portion — across any tier. Examples: "Elena R. + 2 others" = Elena (named) + 2 more vouches from anywhere (1st circle, FoF, or neighbor). "2 friends of Sarah J. + 5 others" = 2 explicit FoF through Sarah + 5 more vouches via other bridges or neighbors. Honest counting; doesn't conceal total endorsement. | Active |
| Apr 24, 2026 | Bridge selection when multiple bridges exist | When a pro has FoF vouches through multiple 1st-circle bridges, name the bridge with the MOST friends vouching for that pro. Tiebreaker: most recent vouch through that bridge. The other bridges' vouches roll into "+ N others." Surfaces the strongest signal first. | Active |
| Apr 24, 2026 | NEIGHBOR cards include neighborhood, no "+ N others" needed | Format: "Vouched by a neighbor in [Neighborhood]" or "Vouched by N neighbors in [Neighborhood]". Because NEIGHBOR is the lowest tier (highest-tier-wins rule), a NEIGHBOR-badged card has only neighbor-tier vouches — no other tier to roll into "+ N others." | Active |
| Apr 24, 2026 | Vouch attribution patterns by tier (locked) | 1 voucher: 1ST CIRCLE → "Vouched by Elena R."; MUTUAL FRIEND → "Vouched by a friend of Sarah J."; NEIGHBOR → "Vouched by a neighbor in Willow Glen". Multiple vouchers (with mixed tiers possible): 1ST CIRCLE → "Vouched by Elena R. + N others"; MUTUAL FRIEND → "Vouched by N friends of Sarah J. + M others"; NEIGHBOR → "Vouched by N neighbors in Willow Glen". | Active |
| Apr 24, 2026 | Tier badge visual hierarchy | 1ST CIRCLE = filled dark green (#1A3B2B, white text). MUTUAL FRIEND = light green fill (#E8F5E9, dark green text). NEIGHBOR = green-tinted outline (transparent fill, #1A3B2B border at 25% opacity, text at 60% opacity). Three distinct treatments make the trust hierarchy readable at a glance without parsing labels. | Active |
| Apr 25, 2026 | Three-state Circle: Guest → Pre-Contacts → Authenticated | Each state adds capability and gets its own nudge banner. Guest (no account) shows a "Join Home Circle" sign-up nudge. Pre-Contacts (account, no contacts) shows a "Find your circle" contact-sync nudge. Authenticated has no nudge. All three states share the same shell (segmented control, filter chips, NEIGHBORHOOD FEED label on All tab, pro-centric cards) so transitions feel like the screen getting richer, not a different screen. | Active |
| Apr 25, 2026 | Guest mode: hide FAB, show lock on Saved tab | Guests can't vouch (no account, no contractor relationships) so the "+" FAB is functionally useless and would lead to a sign-up gate. Hidden entirely to keep the screen focused on conversion. Saved tab shows a lock icon to communicate the gate before the user taps and hits an unexpected wall. | Active |
| Apr 25, 2026 | Guest location resolution: IP geolocation, geofenced to launch market | Guests haven't given us a location, and an iOS permission prompt before showing anything is a friction wall. Use Cloudflare Workers' built-in `request.cf.city` for free city-level IP geolocation. **Granularity:** city only — IP can't reliably resolve neighborhood. So voucher copy stays anchored to the pro's city (where the voucher lives) rather than the user's. **Geofence:** if IP resolves outside the South Bay launch market, fall back to generic "in your area" copy + show a soft email-capture prompt ("Coming to [city] soon"). **VPN/data center fallback:** when IP can't resolve cleanly, default to generic copy. **Cache:** server-side per session, not re-resolved per page view. **Replaced once user signs up + sets neighborhood in onboarding** — IP is only used for the brief guest window. | Active |
| Apr 25, 2026 | Empty Saved tab: heart icon + text, no CTA, FAB preserved | Empty Saved is a low-stakes informational state — user opened Saved before hearting anything. Composition: 80×80 light-green icon holder with dark-green heart (visual anchor + signals the "save" action color identity), title "No saved pros yet," subtitle "Tap the heart on a pro to save them here." No CTA because the action lives one tap away (adjacent All tab) — a "Browse Pros" button would be patronizing. FAB preserved because vouching is a separate action from saving and stays accessible. Two variants share the same shell: Pre-Contacts (`636:2`) keeps the Find Your Circle nudge above; Authenticated (`643:2`) drops it and centers the empty state. Same chips-removal logic as Cold Start (no pros to filter). | Active |
| Apr 25, 2026 | Cold Start All (no vouches anywhere): use canonical 2-tab All/Saved IA, drop secondary CTA, drop filter chips | The earlier cold-start design used a 4-tab segmented control (All / 1st Circle / Mutual / Neighbor) to let the user "browse by tier." That broke the locked 2-tab All/Saved IA used in every other Circle state — a user landing on cold start would have to learn a different navigation pattern that disappears the moment vouches arrive. Replaced with the canonical Pre-Contacts All header (`590:113`). Also dropped the secondary CTA "Browse neighborhood vouches" (nothing to browse if no vouches anywhere) AND the filter chips strip (chips imply "filter the feed by category" but there's no feed to filter — same logic). Single primary "Vouch for a Pro" CTA gives one clear next step. NEIGHBORHOOD FEED label also omitted. Principle: an empty state is the populated state with content removed, not with empty affordances retained. When the first vouch lands, chips + feed label + cards all appear — no IA shift. | Active |
| Apr 25, 2026 | MVP auth: phone-only (no Apple, no Google OAuth) | Decided to ship v1 with **Continue with Phone as the only auth method**. Apple and Google OAuth options removed from the sign-up sheet (`1:859` and clone `583:1357`). Reasoning: (1) reduces engineering scope for MVP — phone auth + SMS verification is a single integration vs. three, faster to ship and test; (2) phone is the only auth that gives us the contact graph anchor we need (matches users to their contacts list during onboarding for the Pre-Contacts → Authenticated transition); (3) Apple/Google add password-recovery surface area that we don't need yet. Tradeoff: phone numbers are a bigger ask than OAuth tokens — users associate them with SMS spam. Mitigation: heading on the sheet does more trust-building work than before (was previously redundant with the three buttons; now the primary trust signal). Apple/Google nodes preserved as invisible in Figma — restoring them for v2 is a one-line change with no layout work. | Active |
| Apr 25, 2026 | Out-of-market guest gets its own screen (`668:2`), not a copy variant of `663:2` | The Apr 25 IP geolocation decision spec'd "generic 'in your area' copy + email capture" for the geofence fallback but didn't decide whether that should be a new screen or a runtime copy variant of the in-market cold start. Chose new screen because the value exchange differs fundamentally: in-market = "be the first to vouch" (contribution ask, sign-up CTA); out-of-market = "we'll let you know when we launch" (waitlist promise, email capture). Same illustration + same shell + different copy/CTA/input arrangement (out-of-market adds an inline email input field between subtitle and button — input then action, paired as visual siblings). One screen trying to serve both states would dilute both asks. Tradeoff: more design surface to maintain, but the IP-based runtime switch is already happening so adding a screen swap to that branch costs nothing in code. Email capture stays a waitlist (no account created); when we launch in their city the user gets a notification email and onboarding starts then. | Active |
| Apr 25, 2026 | Guest Cold Start All CTA: "Sign Up" (single primary, not email capture or dual CTA) | New screen `663:2` covers the in-market guest whose IP resolved into the launch market but no neighbor vouches have been seeded yet. Three CTA options were on the table: Sign Up (conversion-first), email capture (low-commitment ask, lose the account), or dual CTA (Sign Up primary + email-capture link below). Chose Sign Up alone: sign-up is the primary conversion goal across every guest state and the cold-start framing reframes the ask — "be the first to vouch in your area" turns the empty state into a founding-member opportunity rather than a dead end. Email capture is reserved for the out-of-market geofence scenario (different problem, different copy, separate variant). Dual CTA was rejected because it adds cognitive load to a screen that already has very little content; one clear next action is the right pattern. Title "Join Home Circle" (matches existing 615:2 nudge brand) + subtitle "Be the one your neighbors thank later. Vouch for a pro you trust." (parallels `1:3137` framing, swaps friends → neighbors because guests have no contacts). | Active |
| Apr 25, 2026 | Pre-Contacts Cold Start All CTA: "Connect Contacts" (value-first, not seeding-first) | New screen `656:2` covers the user who signed up but hasn't synced contacts AND whose market has no neighbor vouches yet. CTA could either ask the user to seed the network ("Vouch for a Pro," matching `1:3137`) or ask them to unlock value ("Connect Contacts"). Chose value-first: the user just signed up and gets immediate personal payoff from sync — they see their network's recommendations unlock. Vouching is a contribution ask before the app has demonstrated worth, which inverts the value exchange. Vouching remains one tap away via the FAB. On `1:3137` (post-sync Cold Start) the user is already past contact sync, so vouching IS the natural next ask — the two screens use different CTAs because they sit at different points in the user journey, not because the visual treatment differs. | Active |
| Apr 26, 2026 | Phone input goes inline on the sign-up sheet (no separate fullscreen step) | Now that auth is phone-only (Apr 25 decision), the "Continue with Phone" button is a tap that immediately leads to a fullscreen phone-entry screen (`1:1007`) — that fullscreen exists only because OAuth alternatives used to share the sheet. With Apple/Google gone, the button becomes a redundant indirection. Replaced "Continue with Phone" with an inline phone input field on the sheet itself + a "Send Code" CTA below it. Saves one tap, removes a screen, and keeps the sign-up flow contained within a single surface. The input matches the email-capture pattern from `668:2` (366×56 white pill, cornerRadius 28, 1.5px stroke #1A3B2B @15%) so we have one consistent inline-input language across guest entry points. **US-only, +1 hardcoded** — no country picker for MVP. South Bay launch market is US-only and adding a country picker burns surface area on a problem we don't have. Easy to revert (add a left-side picker pill inside the same input frame) when we expand. Tradeoff: international friends/family signing up to use the app outside the US will get blocked at signup until v2. Acceptable cost for a launch-market MVP. | Active |
| Apr 26, 2026 | Sign-up and sign-in unified — submit looks up the number, no manual "Sign in" link | The sheet previously carried "Already have an account? Sign in" as a secondary action. With phone-only auth, that link is dead weight: the OTP step is identical for both new and returning users (we send a code to the number; they enter it). On submit, the API looks up the number and routes accordingly — new number → account creation flow continues into onboarding; existing number → straight into the authenticated experience. The user doesn't have to know or care which path they're on. Removed the "Already have an account? Sign in" row (`1:918` and clone `583:1416` hidden, preserved for potential restoration). One CTA, one flow. Tradeoff: a returning user who taps the heading expecting a "Sign in" affordance might wonder if they're in the right place — mitigated by the title-by-context pattern ("Sign up to save pros" works for both signup and sign-in since the user IS signing up to do the action, regardless of account status). | Active |
| Apr 26, 2026 | OTP step happens via in-place sheet morph (not a fullscreen transition) | After the user taps Send Code, the sheet **morphs in place** — phone input swaps for 6 OTP boxes, Send Code swaps for "Didn't get a code? Resend code (timer)". Title changes from "Sign up to [context]" to "Verify your number" with subtitle "Code sent to +1 (XXX) •••-••XX". Sheet shape, footer, and background-blur all stay constant. **Why morph over transition:** keeps the user anchored to the same surface, no jarring fullscreen swap, no perceived distance from the original action context. Tradeoff considered: a fullscreen OTP screen gives more breathing room for the auto-fill chip ("From Messages: 842910") and explicit Verify CTA, but the sheet has enough room for 6 50×56 boxes and we auto-submit on the 6th digit so no Verify button is needed. The "From Messages" auto-fill is a system iOS affordance that appears above the keyboard regardless of UI — we don't need to show a static mock for it inside the sheet. **Implementation:** `1:932` repurposed as the sheet morph state (cloned `1:859` shell, swapped phone input for OTP boxes and Send Code for resend row). Old fullscreen screen `1:1007` deprecated (renamed + DEPRECATED banner overlay added; preserved in file). 583:1357 (Saved-tap clone) synced with the same inline phone input + Send Code + sign-in-row-removal treatment. | Active |
| Apr 26, 2026 | OTP → setup-profile transition: micro-success on the sheet, then dismiss (no separate "Account Created" interstitial) | When the user enters the 6th OTP digit, the sheet should give a brief positive-reinforcement beat before dismissing into setup profile: 6 boxes flip to dark-green filled state instantly, title morphs to "You're in!" with a small checkmark, ~700ms hold, then the sheet slides down and setup profile loads. Two reasons: (1) the dwell hides the network latency naturally — OTP validation + account creation happen in flight during the animation, so users don't see a spinner or perceive lag; (2) it acknowledges the milestone without burning a tap on a marketing-style "Account Created!" interstitial. Save explicit success interstitials for moments that earn them — first vouch posted, first Circle match, etc. If the OTP call fails, boxes shake red and title becomes "Code didn't match" — known iOS pattern. Returning users (existing phone in DB) get the same micro-state but route to home instead of setup profile. **Not yet built in Figma** — captured here as the build spec. | Active |
| Apr 26, 2026 | Wrong-number recovery: "Edit" link on OTP sheet, no back button on setup profile | The OTP sheet shows the phone number the code was sent to. Tapping the "Edit" link (inline with subtitle, dark-green DM Sans Bold) morphs the sheet back to phone-input state — bidirectional sheet morph, not a separate back-stack pop. This is the meaningful escape hatch for the most common error case (mistyped phone number) and lives at the step where the user can actually see the wrong number. Paired with: **no back button on setup profile** (`1:1083` and `1:1125`). After verify, the account exists; "back" to OTP forces a re-verify (user-hostile), and "back" to phone input contradicts the verification just completed. Removing the back button keeps the post-auth flow forward-only without trapping users — the Edit on OTP is the safety net at the right step. **No "Skip" on setup profile either**: first/last name is the lightest possible onboarding ask, a placeholder name creates downstream pain (anonymous attribution in feeds, profile nags), and forcing it isn't egregious for a 2-field form. Tradeoff: someone who genuinely changes their mind has to kill the app — annoying but rare. If we see drop-off between OTP and setup-profile completion in analytics, that's the signal to revisit; loosen the funnel only when data demands it. | Active |
| Apr 26, 2026 | Drop the 3-dot progress indicator from setup profile screens | The 3-dot indicator at the top of `1:1083` and `1:1125` was a holdover from when phone entry, OTP, and setup profile were all separate fullscreen steps in a 3-step flow. With phone + OTP collapsed onto the sign-up sheet, the post-auth flow is just 2 fullscreen steps (setup profile + contacts permission). Three dots overstates the journey, two dots feels paltry, and at this length the user can see they're not done from the screen content (titles + form fields + CTAs). Progress affordances earn their keep on 4+ step flows where the user is genuinely lost — at 2 steps they're visual debt. Removed entirely (preserved as invisible nodes, restorable). Keeps the header chrome-free post-auth, which pairs with the no-back-button decision to give those screens a focused, single-task feel. | Active |
| May 3, 2026 | Rahul (PM) feedback ironed out: 9 implements locked, 4 killed, 5 deferred to post-MVP | Full feedback session triaged. **Locked implements (sub-tasks under #9):** A1 Home card hybrid rebalance, A2.5 default-active "All" chip on Circle category chips, A3 drop name+neighborhood from onboarding, A4 Google Places integration in Vouch Add New Pro, A5 carousel pagination dot indicators, A6 MUTUAL FRIEND attribution audit (done same session), A7 Vouch Success context-aware exit routing, A8 verify Saved hearts on Pre-Contacts cards, B10 example cards in Pre-Contacts Cold Start. **Killed:** A2 trust-tier filter chip reorder, B9 trust-tier filter row, B11 Saved tab position in 4-item nav (all conflicted with locked 2-tab All/Saved IA), plus the "flip Circle to voucher-led" interpretation of A1 (killed by Saved tab being structurally pro-centric — see separate entry). **Deferred to post-MVP:** D19 saved-search "be on the lookout" notifications, D20 contractor-side companion app, D21 multi-platform review broadcasting, D22 AI-mediated lead capture, D23 per-lead monetization. | Active |
| May 3, 2026 | Circle stays pro-led (Saved tab is the structural anchor) | Rahul proposed flipping both tabs: Home becomes pro-led (contractor + job primary), Circle becomes voucher/circle-member-led. Pro-led Home is a real product win and gets implemented (see A1). But flipping Circle is incompatible with the Saved tab — saved pros are inherently a pro-curated list, there's no coherent way to make Saved voucher-centric without breaking what saving IS. Two tabs in a segmented control with two card structures and two content types isn't a tab switcher anymore. So Circle stays pro-led with All/Saved structurally aligned. Differentiation between Home and Circle is now content-source (broader pool, recency-sorted vs trust-tier strict), not card-structure. | Active |
| May 3, 2026 | A1 Home card hybrid rebalance (typography only, no structural flip) | Keep voucher-led card structure on Home (Sarah Jenkins leads as subject) but elevate the discovery target so contractor + category is scannable at a glance. Bump inline contractor name to display weight (Instrument Sans Bold ~16-17px), add category pill (PLUMBER, ROOFER) inline next to contractor name, optionally pull rating to same row. Voucher remains grammatical subject; pro + category becomes scannable via typography weight, not layout. Sweep: 1:1209, 1:1430, 284:2, 21:219. Circle cards untouched. | Active |
| May 3, 2026 | Home MUTUAL FRIEND attribution: keep bridge avatar, name slot reads "[Bridge]'s friend" (possessive) | Folded into A1. The voucher-led Home card structure shows avatar + name + "vouched for X." For MUTUAL FRIEND tier the actual voucher is anonymous, so showing the bridge person AS the voucher (current state — "Marcus Chen vouched for...") contradicts tier semantics. Three options considered: (A) keep bridge's avatar, change name slot to "Marcus's friend" — possessive form, fits the slot, reads naturally; (B) anonymize avatar + use "A friend of Marcus Chen"; (C) restructure cards differently for MUTUAL FRIEND. Chose A: the bridge person's face IS the social proof (you trust Marcus → Marcus trusts this person), so anonymizing the avatar throws away the most valuable visual signal. Asymmetric with Circle's "a friend of Marcus C." attribution but each phrasing fits its grammatical context (sentence subject vs descriptive label). | Active |
| May 3, 2026 | Circle category chips: add "All" chip default-active, single-select | At MVP scale data is sparse — defaulting to a category filter (e.g. "Plumbing" pre-selected) means the user lands on a near-empty screen even when 10 pros exist in their network across all categories. Browse-by-default, filter-by-tap is the right pattern. Implementation: add explicit "All" chip at position 0, active by default. Tap any category → that chip activates, "All" deactivates. Tap "All" → resets to show everything. Single-select for MVP (multi-select adds complexity for niche power-user benefit). Visually consistent with the existing All/Saved segmented control above (one-thing-active pattern stacked twice). Sweep: 554:321, 554:158, 590:2, 606:2, 583:672. | Active |
| May 3, 2026 | Drop name + neighborhood from onboarding — phone-only signup | Two screens (1:1083 Name Setup locked, 1:1125 Name Setup contacts) get deprecated. Onboarding becomes phone → OTP → Home. Reasoning: (1) every required signup field drops conversion ~5-10% per industry data; (2) name display logic doesn't actually need user input — voucher names come from the viewer's own contacts (1ST CIRCLE = "Elena R." pulled from contacts; MUTUAL FRIEND = "a friend of [Bridge]" where Bridge is from contacts; NEIGHBOR = no name shown), so a name-less HC user is fully attributable across the social graph regardless of what they typed; (3) neighborhood defaults to IP geolocation (already our approach for guest), user edits in Profile if wrong. The user's typed name is only ever used in their own Profile screen — default to "Set up your profile" placeholder there. My Vouches and Vouch Detail (mine) already use "You" for self-attribution. | Active |
| May 3, 2026 | Google Places integration in Vouch Add New Pro is v1 scope (not v1.1) | Auto-populate business name / category / service area / phone from Places search. ~70-80% expected coverage. Manual entry stays as fallback for the rest. Phone number becomes dedup key — same number across users = same pro. Solves two problems: (1) vouch creation friction (the form was real cognitive work for users who just want to recommend someone), (2) duplicate-pro proliferation (different users adding the same pro with different name spellings). Engineering: 1-2 days backend (Places API key, search endpoint, caching) + small frontend update (results dropdown + auto-fill logic). Considered pushing to v1.1 to ship faster but the duplication problem compounds — every week of delay means more cleanup later. Worth doing now. | Active |
| May 3, 2026 | Example cards in empty Circle states: Pre-Contacts Cold Start (656:2) only | Two example cards labeled "EXAMPLE" pill in upper-right, positioned between hero stack and Connect Contacts CTA. Skip on guest screens (663:2, 583:672) because their primary job is conversion to sign-up — example cards compete with the Sign Up CTA for attention. Skip on authenticated cold start (1:3137) because the user has already done everything; example cards from "what others are doing" reads as "look at this thing you don't have." Skip on empty Saved variants (636:2, 643:2) because the action ("tap a heart on a pro") happens elsewhere — examples there feel disconnected from the action. 656:2 is the cleanest case: signed-up user, next action (Connect Contacts) directly unlocks "your circle's vouches," example cards preview exactly what the CTA tap delivers. Tight cause-and-effect loop justifies the visual weight added to the cold-start screen. | Active |
| May 3, 2026 | Sheet/overlay frames refactored into standalone overlay components | Today most sheet-style overlays are built as full 430×932 frames with the sheet positioned over a backdrop (Sign-Up Sheet 1:859, OTP morph 1:932, Auth Success 707:2, FAB Action Sheet 1:1942, Log Out 785:2, Delete Account 785:12, Vouch Delete 842:2, OTP Error 785:24, Claim Phone Entry 814:2, Claim OTP 814:80, Claim Success 814:244, Saved-tap clone 583:1357). For prototyping (Figma's Open Overlay interaction) and engineering (one component reused across many parent screens), each becomes a standalone component sized to its own bounds — sheet + drag handle + backdrop dim only, no full-screen wrapper. Underlying-content frames stay separate; the overlay layers on top via prototype interaction or code. Pattern already exists: Unclaimed Tooltip Popover 823:2, Network Error Banner 785:308. Should happen BEFORE prototyping work since prototyping leverages the overlay model. Captured as task #10. | Active |
| May 3, 2026 | Top-level frame names append node ID in parens; nested nodes referenced via parent frame | Process decisions for navigability. (1) **Frame naming:** all 63 top-level frames on Page 1 had their ID appended in parens, e.g. "Search Results (1:526)" — searchable directly in Figma's layers panel. Bulk-applied today via plugin script. (2) **Nested node referencing:** in conversation, never cite a nested node by its bare ID alone — there's no way to find it in Figma without knowing the parent frame. Format: "Search Results (1:526) → 1:592" or compressed "1:526 → 1:592." (3) **Page reference omitted** — file is single-page (Page 1 / 0:1) so the page ID is redundant noise; reintroduce automatically if more pages are added. Both rules captured in `.claude/skills/chad/SKILL.md`. | Active |
| May 6, 2026 | 4-tab Home / Saved / Ledger / Profile architecture (Circle tab dropped) | Major product re-architecture. Original 4-tab structure had Circle tab (with All + Saved sub-tabs). Audit revealed Home and Circle/All were 80% the same surface — both showed pros vouched by your network, only sort + card structure differed. Two tabs for "different sorts of the same data" is hard to defend. Restructured: Home becomes unified pro-discovery (search + sortable directory + filter chips), Saved becomes its own top-nav tab (single-purpose retrieval surface for saved pros), Ledger unchanged (Pillar 2 home maintenance), Profile unchanged. Differentiation between Home and Saved is now content-source, not card-structure: Home = pros vouched by your network (broad, sortable); Saved = pros YOU've curated (narrow). Drops the deferred Saved-Pros-on-Profile sub-page (746:60) since Saved is now top-nav. Affects ~12-15 currently-designed Circle screens (deprecated as standalone, card patterns repurposed onto Home/Saved/Search Results). Captured as task #27. | Active |
| May 6, 2026 | Saved tab single-purpose (no Friends sub-tab, no Vouched sub-tab) | Two proposals to add sub-tabs to Saved were considered and killed: (1) Friends sub-tab showing connected contacts on the app — dilutes Saved's high-frequency retrieval job for unproven Path-B people-first hypothesis; at MVP network density (100-500 users in South Bay), Friends view would be near-empty creating bad first impression; (2) Vouched sub-tab showing pros user has vouched for — frequency mismatch (saving = high-frequency intent, vouching = low-frequency action history); My Vouches already has appropriate placement at Profile sub-page (746:2); permanent UI chrome on Saved would tax 95% of users for 5% who care. Both rejected. Where vouched-status SHOULD show: small "You vouched for this" indicator on Pro Profile pages (task #30) + checkmark on Saved cards if user also vouched. Friends/People hypothesis revisited post-launch when network density justifies and behavior data shows demand (e.g. >15% session activity tapping voucher names). | Active |
| May 6, 2026 | Saved tab named "Saved" not "Circle" | Considered renaming the Saved tab to "Circle" to preserve the brand-nav alignment lost when the original Circle tab was dropped. Competitive analysis (Pinterest, Spotify, Zillow, Houzz, Airbnb) consistently uses simple/conventional language for saved-collection tabs. For MVP acquisition where new users have zero brand context, "Saved" is unambiguous and matches industry pattern with zero-learning recognition. "Circle" requires a moment of cognitive interpretation. Brand asset "Circle" stays alive in the app name (Home Circle) where it does branding work without UI tax. Heart icon + "Saved" pictographic affordance is also clearer than heart icon + "Circle". Rename later is a 5-minute switch if post-launch data signals demand for branding the tab. | Active |
| May 6, 2026 | App name "Home Circle" retained | Considered renaming the app given the brand-nav alignment loss (Home + Circle tabs no longer literally spell the brand). Decided to keep. Reasons: (1) The "Circle" concept lives in the data layer — tier badges (1ST CIRCLE / MUTUAL FRIEND / NEIGHBOR) and attribution patterns ("a friend of Marcus C.", "a neighbor in Willow Glen") are the brand's literal expression; that's a stronger anchor than a tab label. (2) Sunk brand work is real: weeks of design-system, decision-log, strategic-plan, sprint-tracker, brief, plus in-app copy ("Welcome to Home Circle", etc.). (3) Two-pillar reading reinforces the name: "Home" = maintenance + recording (Pillar 2), "Circle" = trust network (Pillar 1) — both halves carry weight. (4) Names get meaning from product traction, not vice versa (Twitter was Twttr, Yelp was ToTheNines). Compensatory move: lean into "Home Circle" terminology in onboarding copy ("Welcome to your Home Circle") and tier-badge tooltips so the brand-concept is explicit without nav changes. Revisit only if user research reveals confusion or trademark conflict. | Active |
| May 6, 2026 | Home leads with pros (Search Results card pattern) | Original Home design used voucher-led card structure (Sarah Jenkins as social subject, "vouched for X" as supporting line). Per product framing — "find pros used by your friends" — Home should lead with pros, not vouchers. Adopted Search Results card pattern (1:526 → 1:569): pro avatar + name + tier badge + meta + attribution. Card structure is pro-led; voucher attribution is supporting evidence. Same pattern shared across Home + Saved + Search Results so the visual language is consistent across pro-discovery surfaces. Drops the italic quote that Home cards had — it's vestigial social-feed flavor that doesn't fit pro-discovery intent. Default sort on Home: trust-first (1st circle → mutual → neighbor) per Apr 24 sort lock. Captured in task #11. | Active |
| May 6, 2026 | No section headers on Home for MVP | Considered using Search Results' "FROM YOUR CIRCLE" / "VERIFIED NEIGHBORHOOD PROS" section headers on Home to group cards by tier. Decided to skip for MVP. Reasons: (1) Network sparsity — at launch user has 2 first-circle vouches + 5 neighbors at most; section headers framing 2 cards look proportionally heavy; (2) Search Results NEEDS sections because queries return mixed network + neighborhood results that need disambiguation; Home has no query, no disambiguation problem; (3) Tier badges + sort order already create implicit visual sections (badge color transition 1ST CIRCLE filled → MUTUAL FRIEND light → NEIGHBOR outlined); (4) Cleaner first-launch impression with less UI chrome; (5) Easy to add later if user testing reveals confusion. Add post-launch if data shows tier hierarchy is unclear. | Active |
| May 6, 2026 | MVP scope cuts: Popular in Neighborhood, notifications, two Profile sub-pages | Three concrete cuts to sharpen MVP scope. (1) "Popular in Neighborhood" section on Home — cut. Dilutes trust-through-network value prop with generic popularity ranking that every other contractor app already shows. We win on trust-curated, not aggregate popularity. At MVP scale popularity signal is thin (5-10 vouches per pro at most). Add post-launch if trust-tier feed engagement is low. (2) Push and email notifications — cut entirely. Sparse network at launch = empty notifications = bad signal. Notification fatigue happens fast when users opt in before seeing value. 2-4 days of engineering each that doesn't move core value prop. Add post-launch v1.1 when network density supports meaningful events (>1K users, avg 5+ contacts on app per user). Eliminates Push Notification Priming screen (was task #5, now deleted). (3) Saved Pros sub-page in Profile (746:60) — already deferred Apr 28; now formally killed since Saved is top-nav per restructure. Notification Settings sub-page (746:176) — killed since notifications themselves are out. Profile shrinks from 8 rows to 6 (Edit Profile, My Vouches, Connected Contacts, Privacy, Help & Support, Log Out). Net: ~2-4 days engineering + 2-3 hours design saved, sharper value prop. | Active |
| May 6, 2026 | A4 Google Places integration: two-scenario framing | New pro vs existing pro flows distinguished. SCENARIO 1 — New pro (not in HC database): Vouch Add New Pro 1:2465 restructured with Google Places search as primary input; manual entry only as fallback link AFTER Places returns zero results. For user's VERY FIRST vouch ever, manual fallback is hidden entirely — they MUST select from Places (forces canonical data on first user-generated entry, prevents downstream pro-claim mismatches). After first vouch, manual link can appear as secondary affordance. SCENARIO 2 — Existing pro (in HC, possibly saved): Vouch Search Autocomplete 1:2719 returns HC pros first (saved + network-vouched), Places results second labeled as new-pro candidates. Tap HC pro → 1:2411 with pro pre-filled. Tap Places result → new pro flow with Places data pre-filled. Dedup at search time. OPTIMIZATION: Add "Vouch for [Pro Name]" CTA on Pro Profile pages (1:705 + 798:2) per task #29 — saves search step when user is already viewing the pro. Phone number is dedup key. Engineering: 1-2 days backend (Places API + caching + dedup). | Active |
| May 6, 2026 | Two-pillar value prop preserved through restructure | Home Circle's value prop is two-fold: (Pillar 1) Find pros vouched by your friends — the launch differentiator that drives MVP scope; (Pillar 2) Track and record your home maintenance — the longer-term direction that compounds value over time. The 4-tab restructure (Home/Saved/Ledger/Profile) explicitly preserves both: Home + Saved carry Pillar 1; Ledger is Pillar 2's dedicated surface (top-nav presence signals it's strategically important even if low-frequency at MVP). Cross-pillar integration already exists in designs: Log Maintenance form (1:2065) tags a pro from your circle; Post-Vouch Prompt (1:2161) offers to log work to Ledger after vouching; A4 Google Places makes new-pro entry low-friction for both flows. MVP onboarding copy should mention both pillars upfront ("Find pros your friends trust. Keep a record of your home.") to anchor the dual-pillar story. Pillar 2 stays minimal at MVP (no reminders, warranty tracking, spending analytics, multi-property — all post-launch). Brand name "Home Circle" reinforces dual reading: "Home" = Pillar 2, "Circle" = Pillar 1. | Active |
| May 6, 2026 | Pro Profile vouch info pattern (1:716) adapted to Home cards | The Pro Profile main info element 1:705 → 1:716 shows two patterns adopted onto Home cards: (1) Pro location with separator dot, e.g. "PLUMBER · Willow Glen" inline on the meta row — gives users "is this pro near me" signal at scan time without taking a dedicated row. (2) Voucher avatar stack: 2× 24×24 overlapping circles right-aligned next to attribution text. Reuses imageHashes from Pro Profile (be389487... and d7ded22...) so the same Sarah J. face appears on Home and Pro Profile — visual consistency across surfaces. Avatar stack only applies to 1ST CIRCLE tier (where voucher names + photos come from viewer's contacts); MUTUAL FRIEND shows single bridge avatar (Marcus's photo, since the bridge is in viewer's contacts but the FoF voucher is anonymous); NEIGHBOR has no avatar (voucher anonymous, no photo source). Card height grew slightly to accommodate (148h with the dedicated vouch row + avatars). | Active |
| May 6, 2026 | Canonical pro-led Home card structure locked (1:1430 → 1:1524 reference, with iteration tweaks queued) | The pro-led Home card structure landed today as the canonical reference for all 4 Home screens + Saved tab + Search Results consistency. STRUCTURE: 60×60 pro logo (rounded full circle) on left at (20, 17). Content area at (96, 17), 274w × 114h. Row 1 — Title row (23h): pro name "Flow State Plumbing" Instrument Sans Bold 16px left, tier badge 1ST CIRCLE filled green right-aligned. Row 2 — Meta row (22h, y=30): PLUMBER pill (light green E3ECE6 fill, dark green 1A3B2B text, 9px DM Sans Black) inline with "·" separator + "Willow Glen" location text (DM Sans Medium 13px @55%). Row 3 — Stars row (22h, y=60): 5 expanded amber "★" characters (DM Sans Bold 14px, color E09833) + "5.0" rating number (DM Sans Bold 14px). Row 4 — Vouch row (24h, y=90): "Vouched by Sarah J. + 3 others" (DM Sans Medium 11px @60%) left-aligned, 2× 24×24 overlapping voucher avatars (1.5px white stroke) right-aligned. Card height 148h. CONTENT NOTE: Tier badge in title row is the major visual signal — pairs pro identity with trust tier ("this pro your circle backs"). 5-star expanded display gives emotional weight that compact ★ 5.0 didn't. Pro Profile-derived location + avatars carry social-proof richness. ITERATION TWEAKS QUEUED (task #32): bump star size, bump "Vouched by" text size, revisit avatar gap. SCALING NOTE: MUTUAL FRIEND variant uses light-green outlined badge + "Marcus Chen" in title + single bridge avatar; NEIGHBOR variant uses outlined NEIGHBOR badge + no voucher avatar (anonymous voucher) + attribution "Vouched by 2 neighbors in Willow Glen". | Active |
| May 6, 2026 (evening) | Canonical Home card vouch row pattern locked: text-left + 2-avatar stack right + bold "Sarah J." substring | Continued iteration after canonical card initial lock. Three vouch-row patterns explored: (A) single named avatar inline left + attribution text following — clean, named-anchor strong, but loses aggregate social proof; (B/cluster) 3-avatar overlapping cluster on left + attribution text following — richest visual, tightens with 12px overlap, but feels crowded; (V2/Pro Profile-derived) attribution text left + 2-avatar right-aligned stack — Sarah on top z-order, second voucher behind. Built side-by-side comparison on cloned screen 921:4 vs canonical 1:1430 → 1:1524. **V2 won** for being less crowded — text-led reading, faces as supporting visual on the right, intentional middle space reads as breathing room not gap. Added Bold weight to "Sarah J." substring within the attribution text (DM Sans Bold for "Sarah J.", DM Sans Medium for "Vouched by ..." and "+ 3 others") so the named voucher pops as the visual anchor in the line — matches Pro Profile 1:716 attribution treatment. Canonical card locked May 6 evening; cloned screen 921:4 preserved in file as comparison artifact (can delete or repurpose later). Sarah's avatar uses imageHash be389487cffa1a9c16e9057423843e6961a03e41 (from Pro Profile vouchers); second voucher avatar uses d7ded22a812bbedfae2ff209c3cfdd21ecfc6fb5. Card height 148h. Next: scale canonical pattern to MUTUAL FRIEND + NEIGHBOR variants on 1:1430, then to other 3 Home screens. | Active |
| May 7, 2026 | Canonical Home card meta row pattern: location text left, category pill right (no "•" separator) | Iterated via side-by-side comparison on cloned screen 921:4. Original placement was [PLUMBER pill] · Willow Glen with "•" separator dot. Swap puts Willow Glen text first, PLUMBER pill right. Wins because: (1) mirrors title row's text-then-badge rhythm (visual balance across rows); (2) consistent with Circle All v2 (554:355) which uses same location-then-pill pattern (cross-surface visual coherence); (3) the heavy green pill becomes a clean visual punctuation rather than a left anchor competing with the avatar; (4) reading flow reads naturally as a phrase ("Willow Glen Plumber"). Trade-off: less ideal for column-scan-by-category, but Home is trust-curated by network so social-proof scanning dominates over category scanning. Separator dot dropped — pill's distinct fill provides separation. Locked May 7 across all 3 cards on 1:1430 (1:1524, 1:1557, 1:1591) and propagated to other Home screens during scaling. | Active |
| May 7, 2026 | Pro Profile claimed (1:705) retrofit to 2-card + inline bookmark pattern | Cross-surface consistency fix per May 3 followup. Pro Profile claimed previously used 3-card Call/Text/Save action row; Pro Profile Unclaimed (798:2) was redesigned May 3 to use 2-card Call/Text + inline bookmark icon button. Applied same retrofit on 1:705 May 7: Save card 1:758 hidden (preserved invisible, restorable), Call 1:742 + Text 1:750 widened from 120w to 187w each with internals recentered (icon at x=83.5, label at x=78.5/79, lock badge at x=167), Text repositioned to x=195 (8px gap from Call), Bookmark Button cloned from 798:2 (838:2) placed at body-(370, 254) on 1:705's body container 1:706. Both pro profile variants now use identical canonical action-row pattern. | Active |
| May 7, 2026 | Save and Vouch decoupled — unified add-flow with optional rating/review | Major product expansion captured for Phase 1.7 / Phase 2 implementation (task #34). Currently Vouch flow conflates three distinct user actions (Track / Save / Vouch) into one heavyweight flow that requires public endorsement. Unified model: rating + review become OPTIONAL in the existing Vouch flow (1:2411 + 1:2465). User fills rating + review → public Vouch (named voucher attribution + tier badge). User skips → private Save (named-saver attribution, same tier badge). Single flow, two outcomes. Solves multiple user pain points: (a) "I want to remember this pro but don't want to publicly endorse them yet"; (b) "I want my friend Bob to know which pros I use without forcing every pro through a public review"; (c) lower-friction add-pro experience increases save creation rate. Three entry points: heart on existing card (already exists), FAB action sheet renamed "Vouch for a Pro" → "Add a Pro" (1:1942), Saved tab header "+ Add" button (post-restructure). | Active |
| May 7, 2026 | Tier badge semantics decoupled from endorsement: badge = proximity, attribution = strength | Two-axis trust signal model. Tier badges (1ST CIRCLE / MUTUAL FRIEND / NEIGHBOR) represent NETWORK PROXIMITY only ("how close is the signal source"). Attribution language represents ENDORSEMENT STRENGTH ("how strong is the signal"). Examples: 1ST CIRCLE badge + "Vouched by Sarah J." (strong endorsement, named voucher in your contacts) vs 1ST CIRCLE badge + "Saved by Marcus C." (soft signal, named saver in your contacts). Same proximity tier, different strength. Sort weight within tier: vouches outrank saves (Vouched multi > Vouched single > Saved multi > Saved single). Vouching incentive preserved. | Active |
| May 7, 2026 | Vouch visibility always Neighborhood (public review semantics) | A vouch is conceptually a public review — the whole point is to inform other potential customers. Aligns with Yelp/Google/Angi convention. No visibility toggle on vouches at MVP — eliminates UI complexity at-time-of-vouching. Users who want "private review to my circle" can express that via Save (without rating/review) — the named-saver attribution surfaces in friends' feeds. v1.5 may add Circle-only vouch option if data shows demand. | Active |
| May 7, 2026 | ~~SUPERSEDED Sept 1 2026 (list-level — see the Sept 1 entry)~~ Save visibility: per-pro selector at save time, Circle default hardcoded, no list-level state | Simpler than initially proposed. Single source of truth per save record. 3-option visibility selector (Private / Circle / Neighborhood) in add-flow with hardcoded Circle default. NO list-level toggle (redundant given per-pro state), NO user-level default preference setting (YAGNI for MVP — if defaults need to change at scale, change the hardcoded default in a backend deploy). Privacy-conscious users mark each save Private individually — minor friction (~10 saves/year for typical user) acceptable for MVP. v1.5 may introduce user-level defaults if behavioral data demands. Resolution rule: pro visible to friend = (per-pro visibility allows). Friend visibility based on their tier relationship to user (1st circle / FoF / neighbor). | Active |
| May 7, 2026 | Save → Vouch graduation: explicit visibility-shift messaging | When user adds rating + review to an existing save (graduating Save → Vouch), visibility automatically shifts to Neighborhood (since vouches are always Neighborhood). UI surfaces this explicitly: at-time-of-add the submit button label changes based on whether review filled ("Save to your list" subtitle "Visible to your circle" vs "Save & Vouch" subtitle "Review visible to your neighborhood"). Later graduation (saved pro → adds vouch) gets explicit prompt: "Vouches are public reviews — your review will be visible to your neighborhood. Continue?" Edge case: if pro was Private, graduating to vouch flips to Neighborhood — strong confirmation prompt warranted. | Active |
| May 8, 2026 | Vouch Success 1:2619 dismiss-routing depends on entry context | Different entry points to the Vouch flow have different natural exit destinations. After user posts a vouch and the Vouch Success state holds (~700ms), auto-dismissal routes to: (a) Log Maintenance flow → Vouch flow → exit to Ledger (the user came from logging work; they expect to return there to see the entry now tagged with the pro); (b) FAB → Vouch for a Pro → exit to Home (the user came from the global add affordance; their fresh vouch surfaces in the Home feed where they/their network can see it); (c) Pro Profile → "Vouch for this Pro" CTA → exit back to Pro Profile (user came from viewing the pro; their fresh vouch now visible in the pro's vouches section); (d) any other entry → default to Home. Engineering wires via context prop on success screen. Doc-only at MVP — Vouch Success 1:2619 design unchanged, routing handled at engineering. NOTE: after #34 Save/Vouch decoupling, similar routing applies for Save-only outcomes (no vouch posted) — defer per-outcome routing nuance to that implementation. | Active |
| May 8, 2026 | B10 example cards in Pre-Contacts Cold Start — killed for MVP | Original scope (May 3) was 2 placeholder cards with EXAMPLE pill on 656:2 to preview "what your circle's vouches will look like once you sync contacts." With #27 restructure deprecating 656:2 (Circle screens replaced by unified Home pro-discovery), the contextual moment for example cards no longer exists in the same form. Considered migrating to Home cold-start (authenticated user, zero vouches in network) but that surface is rare at MVP scale and adding example cards there risks confusing users about what's real vs placeholder. Killed B10 entirely for MVP. Cold-start surfaces will use illustration + "vouch for someone you trust" CTA pattern (already in canonical). Revisit if post-launch user research shows new users have trouble understanding what the app will surface. | Active |

## 2026-05-08 — Cut "X people found this helpful" from MVP

**Decision:** Remove helpful-vote functionality from Vouch Detail screens for MVP.
Hidden the helpful-row containers `1:2774 → 1:2824` (Vouch Detail Other's) and
`793:2 → 793:52` (Vouch Detail Mine). Category pills (`1:2822` / `793:50`) retained.

**Rationale:**
- Conflicts with our trust model — we differentiate on *who* vouched (network attribution
  via tier badge + named voucher), not on *how many* upvoted. Layering popularity voting
  on top of network attribution muddies the message.
- Cold-start failure mode — at MVP launch most vouches will display "0 people found this
  helpful" or "1 person", which reads worse than no counter at all. Yelp's helpful counts
  work because they have decades of voting depth.
- Implementation cost — vote storage, dedup, undo, anti-gaming, "you already voted" state
  for what is essentially a non-core mechanic.
- Yelp/Google use helpful-votes because anonymous reviews need a secondary trust layer.
  We don't — the network *is* the trust layer.

**Future revisit (v1.5+):** Network-scoped variant — "3 people in your circle agree" —
becomes interesting once vouch density grows. Conditional on (a) sufficient network
density, (b) explicit user ask, (c) confirmed value during post-launch interviews.

## 2026-05-08 — Deprecate Vouch Detail screens; pro profile becomes single source of truth for vouch presentation

**Decision:** Mark `1:2774` (Vouch Detail Other's) and `793:2` (Vouch Detail Mine) as DEPRECATED.
Vouch presentation consolidates onto the Pro Profile via inline expand/collapse pattern (Google
Reviews-style, not Yelp tap-to-detail).

**What replaces the detail screens:**
- **Reading vouches:** Pro profile vouch rows show truncated text by default with "Read more".
  Tap expands inline, no navigation. Photos either inline carousel inside expanded row or
  lightbox-style overlay (no separate screen).
- **Editing/deleting your own vouches:** Two entry points —
  (a) inline kebab on your own vouch row on the pro profile (Edit jumps to Edit Vouch screen,
      Delete uses existing Vouch Delete Confirm Sheet from #1)
  (b) Profile → My Vouches sub-page (net-new, tracked as #38)
- **Post-vouch flow:** Vouch Success routes to pro profile per #17 — user lands on the pro
  and sees their newly-posted vouch in social context.

**Rationale:**
- Pro-centric model means vouches live on pro profiles. A separate "view this vouch" screen
  is redundant — the pro profile is where context (who else vouched, trust tier mix) is
  preserved.
- Removes two screens from MVP.
- Matches industry pattern — Google Reviews uses inline expand. Yelp uses tap-to-detail
  but they have a different UX surface (separate review pages indexed for SEO, comments,
  etc.) that we don't need.
- Self-edit pattern: list → edit (skipping a "view your own" intermediate state) is the
  Yelp/Google model and is faster.

**Implications:**
- #37 (Pro Profile vouch row redesign) is now MVP-blocking.
- #38 (Profile → My Vouches sub-page) is net-new and MVP-required.
- Edit Vouch screen needs an entry point from both the inline kebab and the My Vouches list
  — ensure it accepts a vouch ID parameter.

## 2026-05-08 — V1 architecture committed (supersedes V0 / interim plans)

**Decision:** Commit to a full progressive-disclosure V1 architecture as the next launch. Skipping V0 friends-and-family forced-signup beta. V1 spec consolidated in `docs/v1-spec.md`.

**Architectural commits (locked):**

1. **4 user states** — Guest / Auth-No-Sync / Fully Onboarded / Pro-Claimed
2. **Lite mode + soft auth via unified bottom sheet** — guests browse with IP-located content; signup gates fire at high-intent action moments (phone reveal, save, vouch, ledger, refer)
3. **5-step Onboarding Sheet state machine** — Trigger → OTP → Quick Setup (Name + Zip) → Sync Choice (with friend match teaser hook) → Result (sheet dismisses, action completes)
4. **Privacy Firewall on Ledger** — Ledger trigger never asks for contact sync (private utility intent)
5. **3-tier teaser cascade** — Network match → Geographic proof (≥5 zip vouches) → Generic fallback
6. **OTP-matched pro claim** — auto-verify when claimant_phone === seed business_phone; admin handles mismatches
7. **Minimal Pro Edit** — description (500 char) + photos (10 max) only; everything else via support email for v1
8. **Vouch visibility default** — Synced users vouch to Circle; Non-synced users vouch publicly (Neighborhood) for Proof of Life. Manual override available per vouch.
9. **HMAC-SHA256 with versioned salt** for contact hashing (rainbow table defense)
10. **Reverse-indexed shadow matching** for friend match teaser
11. **Fat First Page** for search ranking — fetch 200 by proximity, client-side re-rank by social signals, fall back to proximity for page 2+
12. **Postgres tsvector + pg_trgm** for text search (no Elasticsearch in v1)
13. ~~**Cloudinary/Imgix** for image hosting~~ — SUPERSEDED May 12, 2026 by **Supabase Storage** + AWS Rekognition moderation. See May 12 "Image storage" entry below.
14. **Twilio Verify + rate limit + US/CA country-code allowlist** to defend against SMS pumping
15. **CCPA-compliant Deactivate** (nullify PII, retain vouch text as public content) + CCPA-compliant Privacy Policy
16. **useNudge hook / NudgeManager** for centralized nudge logic
17. **localStorage-persisted skip** with 7-day cooldown to avoid annoying users
18. **Partial Safety Net** — half-completed accounts resume at exact dropout step, "Welcome Back, +1 (415) ●●●-0184" UI

**Cut from V1 (deferred to V1.5+):**
- Pro analytics dashboard
- Pro Ledger Verification (two-sided)
- Pro hours/phone/services/website edit
- Push notifications
- Vouch quality enforcement
- Helpful votes
- Vouch Detail screens (already deprecated)
- Web app surface

**Process note:**
This decision was the result of a multi-hour deep-dive that initially explored a V0 friends-and-family beta before committing to V1 directly. Prior conversation considered:
- Forced upfront signup (rejected — doesn't match progressive-disclosure brand)
- JIT signup with no infrastructure (rejected — too scattered, too many half-flows)
- The Lite + Soft Auth model with unified bottom sheet (locked)
- Multiple peer reviews from Milind's friend on engineering details

**Implications:**
- V1 spec lives at `docs/v1-spec.md`
- Existing task list mostly reflects V0-era work and needs cleanup
- Resourcing/timeline conversation pending — June 30 is aggressive for full scope solo, credible with 2-3 engineers in parallel
- Existing Figma work (canonical Home cards, Pro Profile retrofit, etc.) carries forward — they're the same surfaces, just embedded in the new state architecture


## 2026-05-08 — Resourcing & timeline locked: Solo, full scope, Sept-Oct launch

**Decision:** Option A — Milind builds v1 solo with full scope. Target launch shifted from June 30 to mid-September through mid-October 2026. Pull in date if work completes ahead of schedule.

**Rationale:**
- Full v1 scope is the right product per the architecture work this session
- $2k/month budget doesn't accommodate hired engineering help
- No co-founder available; recruiting one wouldn't help June 30 anyway (ramp time)
- Phased-launch approach (Option E) considered but rejected — Milind prefers single launch with complete scope over closed beta + open launch
- Solo with AI-assisted velocity over 17-22 weeks is achievable at sustainable pace

**Implications:**
- Sprint tracker rebuilds around 17-22 week timeline (V1-P2 / Task #59 unblocked)
- Pace > sprint mode: intentional avoidance of burnout
- Critical path = backend foundation (V1-E1 / Task #49). Most engineering depends on it.
- Highest-risk design surface = Onboarding Sheet state machine (V1-D6 / Task #45). Start early.
- Privacy Policy + compliance can run in parallel with build (V1-C1 / Task #57)

**Reconsider triggers:**
- Falling >2 weeks behind a sprint goal → consider scope cuts (Pro state is first cut, saves 3-4 weeks)
- Finding a willing co-founder or affordable contractor → revisit resourcing
- A specific feature taking 2x estimate → reassess approach

## 2026-05-08 — V1 Figma migration map locked

**Decision:** Locked migration plan for moving from 60 existing Figma frames to component-driven architecture (51 components per v1-component-inventory.md).

**Key resolved decisions:**
1. Vouch Success → BottomSheet composition (not new screen, not ConfirmSheet variant) — preserves Pro Profile context
2. Profile sub-pages → 3 new canonical screens: ConnectedContactsScreen, PrivacyScreen, HelpSupportScreen (raises canonical count from 14 → 17)
3. Pro Claim → reuses OnboardingSheet with `trigger: proClaim` prop variant — no duplicate Claim flow components
4. OTP Error → inline state of OnboardingSheet/StepOTP (not a toast) — "Stop and Fix" semantics
5. Network Error → BOTH variants: NudgeBanner/Error (persistent, connection lost) + Toast/Error (transient, single request failed)
6. Loading Circle → subsumed into Spinner atomic component with size + color props

**Cleanup classification (60 frames):**
- 11 KEEP_AS_CANONICAL
- 25 DECONSTRUCT (extract patterns into components)
- 11 PROMOTE_TO_COMPONENT
- 5 KEEP_DEPRECATED (already marked, leave as reference)
- 5 DELETE (Circle pre-#27 architecture, duplicate Home Auth Ledger nudge)
- 3 ARCHIVE_TO_BACKLOG (Out-of-Market variants → V1.5+ Backlog Figma page)

**Design system rule locked: Single-source principle**
Any pattern that recurs across surfaces has ONE component as source of truth. Other surfaces consume via props — they don't replicate logic. Specifically applies to FriendMatchTeaser (Shadow Match), BottomSheet (sheet behavior), LocationBadge (location display + edit). When deconstructing, extract LOGIC not just pixels.

**Files updated:**
- `docs/v1-migration-map.md` (locked, source of truth for migration)
- `docs/v1-component-inventory.md` (updated counts, OnboardingSheet trigger list, design system rules)

**Process note:**
This concludes the v1 architecture planning phase. Next: begin Sprint 1 (V1-D-A — atomic component library).

## 2026-05-08 — V1 Design Tokens locked + set up in Figma + code

**Decision:** v1 design token system (per `docs/v1-design-tokens.md`) implemented end-to-end:

**Figma side (`Components / V1` page, `Tokens / V1` collection):**
- 18 primitive color variables (forest 5, cream 2, gray 6, amber 1, red 2, neutrals 2)
- 36 semantic color variables (text 9, surface 8, border 4, action 7, badge 6, overlay 2) — aliased to primitives where possible, baked-in alpha for opacity-modified variants
- 10 spacing variables (base-4 scale, scoped to width/height/gap)
- 8 radius variables (scoped to corner radius)
- 4 border-width variables (scoped to stroke)
- 7 opacity variables
- 9 z-index variables
- 3 effect styles (shadow/card, shadow/floating, shadow/suggestion)
- 16 text styles (heading 5, body 4, label 2, special 5)
- Total: 111 design tokens

**Code side (`apps/mobile/`):**
- `tailwind.config.js` — full Tailwind/NativeWind config with all tokens, names match Figma 1:1
- `src/design/motion.ts` — duration/easing/autoDismiss tokens (code-only per Decision 5)

**Visual reference gallery built on `Components / V1` page** (`Tokens / Reference Gallery` frame, 1400×4352px) — color swatches, spacing scale, radii, border widths, shadow examples, type samples.

**Pages created in Figma:**
- `Components / V1` — where component library lives
- `Backlog / V1.5+` — for archived out-of-market frames (per migration map)

This completes Sprint 1 Day 0-1 milestone. Sprint 1 Day 2 (foundational atoms: Divider, Spinner) is next.


## 2026-05-08 — Accelerated 7-week plan locked: full-time, June 30 submission, vertical slicing

**Decision:** Removed 3h/day constraint. Committing to full-time effort (~8h/day, 5 days/week) to hit App Store submission by June 30 2026.

**Strategic shift from previous plan:**

Previous: horizontal layers (all atoms → all molecules → all organisms → all canonical screens) over 22 weeks at 3h/day.

New: **vertical slicing** — build "happy path" end-to-end by Week 3, then layer in features. If we run out of time, we have a working app, not a beautiful component library.

**Key principles:**
1. Vertical slicing — happy path (Welcome → Phone → OTP → Home → Pro Profile → Vouch) end-to-end by Week 3
2. Pull highest-risk backend forward — Auth + HMAC + Shadow Matching by Week 2-3
3. Parallelize design + code per feature (Atomic Loop rhythm: 30-60 min Figma → 2-3h code → wired same day)
4. Analytics from Day 1 — PostHog SDK installed Week 1, events fired as features ship
5. Compliance work distributed across Weeks 4-6 (Privacy Policy drafted Week 4, lawyer review Week 5, Deactivate flow Week 6)
6. Beta testing starts Week 3 with first TestFlight build

**What got cut to fit June 30:**
- Pro analytics dashboard (Pro-facing) — events captured but no charts
- Extended Pro Dashboard — only description + 10 photos (already locked)
- Complex transitions — use OS defaults for sheets/navigators
- Help/FAQ page — replace with single Support email link
- Custom photo cropper — use platform-native cropper (iOS `UIImagePickerController` / Expo `ImagePicker` built-in crop) before upload to Supabase Storage. Server has no transform step on upload — Supabase Storage transformations (resize/quality) happen on read via URL params.
- Most edge case polish (defer to post-launch iteration)

**What's KEPT (non-negotiable):**
- 4 user states + Lite mode + Soft Auth model
- Shadow Matching (the differentiator)
- HMAC hashing with versioned salt
- CCPA-compliant Deactivate + Privacy Policy
- PostHog + Sentry instrumentation

**Honest timeline:**
- Submission target: Friday Jun 26 2026 (Week 7)
- Final submission deadline: Tuesday Jun 30 2026 (4 buffer days)
- Realistic public launch: Mid-July 2026 (after Apple review cycles)

**Z-index tokens updated:** scale shifted from 0-80 (10-step) to -1 through 4000 (1000-step) per friend's feedback. Adds `z-below: -1` for background decorations. Renamed `z-bottom-nav` → `z-nav`. Updated in v1-design-tokens.md, tailwind.config.js, and Figma Variables.


## 2026-05-08 — Plan refinements: TestFlight Zero real-contact validation + Reviewer Demo Materials

**Two adjustments to v1-day-by-day-plan.md:**

1. **Day 15 (TestFlight Zero) made more explicit** about real-contact validation on physical devices in San Jose. Recruit 2 beta testers Day 14 (not Day 15) so they're ready to test on real iPhones with real contact lists (500+ contacts) immediately upon TestFlight upload. Validates SMS OTP carrier delivery + phone number format normalization + iOS 17 "Selected Contacts" permission + Hashed Shadow Matching with real data.

2. **Day 30 (Week 6) gets Reviewer Demo Materials deliverable** added: staging environment with seeded data (30 demo pros, 5 demo users with cross-vouches), reviewer test account hashed against demo users' contacts, 60-90 second demo video showing Shadow Match flow, reviewer notes for App Store Connect submission.

**Why:** Social-graph apps frequently get rejected by Apple reviewers with "Could not verify user experience" because the reviewer's test account has no friends in the network. Pre-populated reviewer account + demo video shows the value before the reviewer gets stuck.


## 2026-05-12 — Welcome screen hero illustration locked (Day 2)

**Decision:** Welcome screen hero illustration uses the Midjourney-generated "two neighbors chatting over a white picket fence with symmetric two-house framing" variant.

**Sourcing:** Generated in Midjourney via iterative refinement starting from sleek.design baseline. Final prompt used `--niji 6 --sref [sleek_image_url]` for style consistency.

**Asset location:** `mockups/illustrations/welcome-hero.png` (2048×2048 PNG).

**Composition rationale:**
- Symmetric two-house framing reinforces "neighborhood" brand without overcomplicating
- Conversational hand gesture conveys active recommendation moment
- Color palette (warm earth tones + teal sky) harmonizes with forest-green brand
- Character design (mature but friendly proportions) matches homeowner target demographic
- Texture is subtle — reads as polished consumer app, not children's book

**v1.5 candidate:** Bespoke professional illustration (Fiverr/Dribbble hire, $200-500) if user feedback indicates illustration quality is a friction point. Not expected based on MVP scale.

**Process note:** Spent ~90 minutes on illustration iteration. Acceptable for v1 launch screen (highest-visibility first impression), but flagging that future asset iterations should be time-boxed harder. The art-direction time-sink trap is real.

## 2026-05-12 — Discipline rule: daily deliverables are non-negotiable

**Decision:** Within each day, iteration time is allowed (design quality benefits). But the day's listed deliverables must be completed before day close. No pushing items into next day's plan to absorb today's slippage.

**Rationale:** The 7-week sprint to June 30 has zero buffer. Slipping deliverables day-to-day compounds. By the time we notice, we're 2 weeks behind. This rule forces honest end-of-day completion.

**Re-baselining triggers (unchanged):**
- Week boundary review
- Falling behind by 2+ days on critical path
- Structural blockers (external dependencies)

**Practical effect today (Day 2 — May 12):**
- Welcome screen Figma iterated ~5 times today (good — final design is locked + token-compliant)
- React code mirror was initially deferred to Day 3 — REVERSING that decision now
- Day 2 closes with: Welcome code mirrored to final Figma, even if it means evening work


## 2026-05-12 — SDK upgrade pulled forward to Day 3 (correction to earlier "defer to v1.5" advice)

**Mistake:** When Expo CLI flagged version mismatches between project (SDK 54) and latest Expo packages, I incorrectly advised ignoring them and deferring the upgrade to v1.5. The warnings were flagging a real incompatibility, not preference.

**Symptom:** When testing on physical iPhone via Expo Go (which ships the latest SDK 56 runtime from App Store), got `TurboModuleRegistry.getEnforcing: 'PlatformConstants' could not be found` — the JS bundle from project is incompatible with the native runtime in Expo Go.

**Decision:** Use Path A (SDK-54-specific Expo Go from https://expo.dev/go) tonight to unblock Day 2 verification. Upgrade to Expo SDK 56 first thing Day 3 (before Phone Entry work) for stable long-term footing.

**Lesson:** Expo CLI version-check warnings should be taken seriously when they're more than a patch bump apart. They often signal Expo Go runtime incompatibility, not just preference.

**Task #77** (v1.5 SDK upgrade) is now SUPERSEDED by Day 3 morning task. Keeping #77 around as note that the upgrade is done.

## 2026-05-12 — Spacing token discipline locked + retrofit

**Decision:** Every gap between elements in Figma must reduce to a single spacing token OR a sum of tokens. Off-scale values (11px, 33px, 47px, 59px, etc.) are not allowed in locked designs.

**Rationale:** Code-side gaps map directly to Tailwind classes (`mt-md`, `gap-xl`, `p-2xl`). Off-scale values either force `mt-[11px]` arbitrary syntax (breaks the system) or invite silent dev rounding that drifts design from spec.

**Audited screens retrofitted today:**
- Phone Entry (1089:140): fixed 11px gap → 12px (`space-md`); trimmed headline container dead padding (h=72 → h=36)
- Welcome (1072:52): fixed brand-row/hero overlap (-24px → +24px gap), fixed headline negative offset (-17 → 0), fixed 33px → 32px gap (`space-3xl`), fixed 59px → 48px (`space-4xl`) subhead-to-CTA gap, trimmed hero container internal padding

**Process change:** before locking any screen, run gap audit. Subtract bottom-of-upper-element from top-of-lower-element, verify result is in token scale {0, 4, 8, 12, 16, 20, 24, 32, 40, 48} or a sum thereof.

**Rule documented in:** `docs/design-system.md` → "Spacing Standards" → "LOCKED RULE: Every gap MUST reduce to spacing tokens"

**Exception:** intrinsic element heights (text line-heights, icon sizes, illustration native dimensions) are NOT gaps and don't need to be on-scale.

## 2026-05-12 — Phone Entry + OTP code-mirror audit (Day 3 wrap)

**Audit method:** Pulled absolute Y coordinates for every block on Figma 1089:140 (Phone Entry) and 1120:2 (OTP), computed gap deltas, compared against the spacing tokens used in `PhoneEntryScreen.tsx` and `OtpScreen.tsx`.

**Phone Entry — all on-token, all matching Figma except one:**
- Heading mt-3xl (32) ✓, subhead mt-md (12) ✓, input mt-5xl (48) ✓, privacy line mt-md (12) ✓, container px-2xl (24) ✓
- **Fixed:** Continue button bottom margin was `mb-lg` (16). Figma spec is 34px above safe-area bottom (frame 932 − home-indicator 34 − button-bottom 864 = 34). Bumped to `mb-3xl` (32) — closest token, accounts for SafeAreaView's auto-inset.

**OTP — all on-token, all matching Figma after one Figma change:**
- Subhead mt-md (12) ✓, OTP boxes mt-5xl (48) ✓, resend mt-2xl (24) ✓, OTP box gap 16 ✓ (was 8, fixed earlier today), subhead row gap 8 ✓
- **Figma drift fixed:** code added a back button atom for nav consistency with Phone Entry, but Figma OTP didn't have one (relied solely on the "Edit" inline link). Added back button to Figma 1120:2 at x=16, y=56 — matches Phone Entry's back button position exactly. Edit link remains as the inline shortcut to fix the phone number; back button is the conventional nav affordance.

**Rule reinforced:** code mirror audit means BOTH directions — code conforms to Figma OR Figma updates to match code when the code change is the right one. Source of truth lives wherever the more thoughtful decision lives, not strictly in Figma.

**Token additions made during audit:**
- `tailwind.config.js` fontSize: added `'input': ['17px', '24px']` (iOS-native input value size; was previously `text-[17px]` arbitrary)
- New module `src/design/colors.ts`: centralized all SVG / native-prop hex constants (FOREST_700, TEXT_PRIMARY, TEXT_TERTIARY, TEXT_DISABLED, etc.) so brand color changes are one edit, not five

**New atoms shipped:**
- `BackButton` (40×40 hit target, 20×20 chevron, `-ml-sm` optical alignment) — used on Phone Entry + OTP, ready for Quick Setup, Sync Choice, etc.

## 2026-05-12 — Image storage: Supabase Storage replaces Cloudinary (supersedes Apr 2026 tech-stack row #13)

**Decision:** Use Supabase Storage for all vouch and pro photos. Drop Cloudinary entirely. Run image moderation as an async pipeline using AWS Rekognition called from a Supabase Edge Function triggered on storage write.

**Reverses:** Apr 2026 tech-stack row #13 ("Cloudinary/Imgix for image hosting"). Mark that row Superseded.

**Trigger:** Day 4 prep conversation — Supabase already locked in as the prod Postgres host. Adding a second image-specific vendor on top of Supabase doubled the vendor count for no actual feature gain.

**What Cloudinary brought:**
1. Image CDN with auto-WebP/AVIF format negotiation and auto-quality tuning
2. Built-in AI moderation as a paid add-on (Rekognition under the hood, latency 500–1500ms)
3. URL-based on-the-fly transformations
4. Upload widget for client integration

**What Supabase Storage brings to replace it:**
1. Cloudflare-backed edge CDN (300+ PoPs globally) — performance parity with Cloudinary for US-focused launch
2. URL-based on-the-fly transformations (`?width=400&quality=80&resize=cover&format=webp`) — included on Pro tier
3. Signed upload URLs for direct browser/native uploads
4. Same Postgres project, same auth path, same dashboard, same bill

**What's lost (and the mitigation):**
1. **Auto-format negotiation.** Cloudinary inspects `Accept` header per request. Supabase requires explicit `&format=webp` in URL. Mitigation: detect once in client, append correct format param in image helper — 3 lines of code.
2. **Auto-quality intelligence.** Cloudinary's `q_auto` picks quality per image content. Supabase quality is set per URL. Mitigation: use `quality=80` across the board — bandwidth penalty is single-digit percent and irrelevant at v1 scale.
3. **Inline AI moderation.** Cloudinary's moderation add-on returns moderation status in the upload response. Supabase has nothing equivalent — moderation is now our responsibility, see pipeline below.

**Moderation pipeline (async, Option B from May 12 conversation):**

```
1. Client requests signed upload URL from POST /vouch/photo/upload-url (~50ms)
2. Client uploads directly to Supabase Storage signed URL (~300-800ms) ← upload done
3. Storage write fires Postgres trigger → Supabase Edge Function
4. Edge Function calls AWS Rekognition DetectModerationLabels (~500-1500ms)
5. Edge Function updates photo row: moderation_status = approved | flagged | review
6. Supabase Realtime push (or client polling) flips photo state in UI

Perceived upload time: ~500ms (uploader sees their own photo immediately via optimistic UI)
Time-to-visible-to-others: ~2s
```

**Why async beats sync (and beats Cloudinary's blocking pattern):** Cloudinary's "inline" moderation is actually 1.5–3s of blocked upload UI. Our async path returns the upload in 500ms and runs moderation in parallel — uploader keeps scrolling, photo enters feed for others once approved. Better UX than the vendor we're replacing.

**Cost comparison at v1 scale:**
- Cloudinary free tier: 25 GB storage + 25 GB bandwidth + 25K transformations/month. Moderation is a paid add-on.
- Supabase Pro: $25/month for 100 GB storage + 200 GB bandwidth + transformations included. Plus ~$1 per 1000 images for Rekognition.
- For Home Circle launch volume (target ~500 active San Jose users, ~5 vouches × 2 photos = ~5000 photos in 6 months), Supabase Pro + Rekognition runs <$30/month total. Cloudinary's first paid tier alone is $89/month.

**Lock-in tradeoff acknowledged:** This decision pairs storage with the DB host. If we later switch off Supabase for Postgres (to Neon, Fly, or self-hosted), we'd either migrate storage too or keep a standalone Supabase storage-only project. Acceptable — picking Supabase for both DB and storage is intentional, and a separate storage-only Supabase project is cheap if ever needed.

**Implementation impact:**
- Day 21 in `v1-day-by-day-plan.md` is now "Supabase Storage integration" not "Cloudinary integration"
- Day 22 covers moderation (now via Rekognition Edge Function, was previously rolled into Cloudinary's add-on)
- Privacy Policy third-party processors list: drop Cloudinary, add Supabase + AWS Rekognition
- One fewer API key to provision, one fewer dashboard to log into, one fewer dependency in `apps/api/package.json`

**Tasks touched:** #56 (V1-I1) renamed to "Twilio, Supabase Storage, AWS Rekognition, Sentry, Google Places."

## 2026-05-12 — API runtime: Bun. Hosting: Railway (main API) + Supabase Edge Functions (async tasks)

**Decision:**
- **Runtime:** Bun for the main API process (replaces unspecified Node default that was implied in `v1-day-by-day-plan.md` Day 4).
- **Main API host:** Railway running Bun + Hono. Single long-running process.
- **Async / event-driven tasks:** Supabase Edge Functions (Deno runtime). Specifically: `moderate-photo` (storage write trigger → Rekognition), cron jobs (cleanup-expired-otps, scheduled aggregations), webhook receivers (Twilio status callbacks).

**Why Bun over Node:**
1. All-in-one tooling (native TS, built-in test runner, built-in package manager) saves ~45 min Day 4 setup and ongoing DX friction.
2. Memory footprint 30–50% lower (~40–70 MB vs ~80–120 MB idling) — frees a tier of Railway sizing as we scale.
3. Peak QPS per CPU core 3–5× higher than Node — survives launch viral moments without scrambling instances.
4. All committed dependencies work on Bun (Hono, Drizzle, postgres-js, Twilio SDK, AWS SDK v3, Supabase JS, jose). Verified May 12.

**Honest tradeoff acknowledged:** Bun runtime advantages are *not* user-perceptibly faster at v1 scale. Per-request latency savings ~1–2ms, dominated by Postgres query latency (15–50ms) and external API calls (200–1500ms). Bun's value at v1 is operational headroom and DX, not user-facing speed.

**Why Railway + Edge Functions over pure-Edge-Functions:**
1. Edge Functions are Deno-only. Going all-Edge would invalidate the Bun decision (Hono is portable but you lose all Bun tooling benefits).
2. Edge Functions cold-start in 150–300ms — visible on first hit to the Home feed after idle. Long-running Bun on Railway has no cold start.
3. A 30-route Hono API in one Bun process is way cleaner than 30 separate Edge Function deployments with 30 separate log streams.
4. Edge Functions force pgBouncer/Supavisor pooling; Railway can hold direct pooled connections to Supabase Postgres (one less network hop).
5. Edge Functions DO shine for event-driven async tasks. The moderation pipeline already lives there per the May 12 image-storage decision — keeping the pattern for cron/webhooks is consistent.

**Why Railway over Fly:**
1. Cleaner DX for solo founder operating the infra (UI is nicer, CLI is straightforward).
2. First-class Bun support with starter templates.
3. Single-region US deployment is sufficient for San Jose launch market — Fly's multi-region edge proximity advantages don't apply yet.
4. Reversible — if we ever want global edge presence, Hono code is portable and migrating to Fly is a 1-hour build-script change.

**Architecture summary:**

```
Mobile app (Expo)
       │
       │ HTTPS
       ▼
Railway (Bun + Hono)           ← main API: auth, vouches, search, pros, contacts, ledger
       │
       ├──→ Supabase Postgres (DB)
       │
       └──→ Supabase Storage ──┐
                               │ on storage.insert trigger
                               ▼
                    Supabase Edge Function (Deno)
                    moderate-photo → AWS Rekognition

Plus scheduled Edge Functions:
- cleanup-expired-otps (Supabase Cron)
- twilio-status-webhook (external POST receiver)
```

**Costs at v1 launch scale:**
- Railway main API: ~$5–15/month
- Supabase Pro (includes 500K Edge Function invocations): $25/month
- See `docs/accounting.md` for full cost projections including external services.

**Open follow-ups:**
- Decide pgBouncer/Supavisor pooling config Day 5 when wiring Drizzle connection pool.
- Decide Sentry vs OpenTelemetry for Edge Function observability (Sentry has Deno SDK now, but coverage thinner than Node).

## 2026-05-13 — Local dev Postgres via Docker; Supabase Postgres in production (split-tier database setup)

**Decision:** Use plain `postgis/postgis:16-3.4-alpine` Postgres in a Docker container for local development. Use Supabase's hosted Postgres (Pro tier) for staging and production. Same Postgres major version (16) and same PostGIS extension version on both sides to guarantee migration parity.

**Why split, not Supabase CLI for both:**

The "consistent dev = prod" pattern would have used Supabase CLI locally, which spins up the full Supabase stack (Postgres + GoTrue + PostgREST + Realtime + Storage + Studio) as ~6 Docker containers. We don't use most of that stack — auth is custom JWT via `hono/jwt` (not GoTrue), API is Hono not PostgREST, no Realtime in v1, Storage is a separate Supabase project concern that doesn't need local emulation. Running the full Supabase stack locally would be ~250 MB RAM and ~30 second cold-start for value we don't consume. Plain Postgres in one container is ~50 MB RAM and ~2 second cold-start. Same DB layer, fraction of the overhead.

**Why this works without parity drift:**

1. **Drizzle migrations are vanilla SQL.** Anything that runs against local Postgres 16 runs identically against Supabase Postgres 16 (and vice versa). No Supabase-specific syntax, no proprietary extensions in the schema.
2. **Same PostGIS extension version.** Both local (`postgis/postgis:16-3.4-alpine`) and Supabase Pro use PostGIS 3.4. `pg_trgm` ships with both as a built-in extension.
3. **The only Postgres-flavor difference: connection pooling.** Supabase Pro requires connecting via Supavisor (their pgBouncer-compatible pooler) which mandates `prepare: false` in the postgres-js client. Local Docker accepts direct connections with prepared statements. We've already configured `prepare: false` in `apps/api/src/db.ts` so the client behaves identically against both.

**Implementation status (already shipped Day 4):**
- `docker-compose.yml` at repo root — Postgres container, port 5432, `homecircle/homecircle/homecircle` user/password/db, named volume `homecircle-pg-data` for persistence across restarts
- `apps/api/src/db.ts` — single shared DB client, configured `prepare: false` for Supavisor compatibility
- `apps/api/src/env.ts` — `DATABASE_URL` is the single env switch; default points at local Docker, prod sets it to the Supabase pooler URL
- `apps/api/README.md` — first-time setup walkthrough showing `docker compose up -d postgres` → fill `.env` → `pnpm db:migrate` → `pnpm dev`

**Bug prevented Day 4 → Day 9:** docker-compose.yml originally used `postgres:16` (no PostGIS). Bumped to `postgis/postgis:16-3.4-alpine` Day 4 evening so Week 2 Day 9 PostGIS migrations don't fail with "extension postgis does not exist." Existing `homecircle-pg-data` volume preserves any seed data through the image swap.

**⚠️ Caveat on the image swap (corrected May 13):** the PostGIS image's `CREATE EXTENSION postgis;` only auto-runs when the container initializes a fresh data directory (via `docker-entrypoint-initdb.d/` scripts). On an existing volume, that init step is skipped, so swapping `postgres:16 → postgis/postgis:16-3.4-alpine` against an existing volume leaves PostGIS uninstalled despite the image having the binaries. Same trap applies to `pg_trgm` later. Two paths to handle this:
1. **One-shot manual fix (current):** after image swap, run `docker compose exec postgres psql -U homecircle -d homecircle -c 'CREATE EXTENSION IF NOT EXISTS postgis; CREATE EXTENSION IF NOT EXISTS pg_trgm;'` — documented in `local-dev-workflow.md` gotchas.
2. **Durable fix (Day 9 when PostGIS migration lands):** add a hand-written `packages/db/drizzle/0001_extensions.sql` migration that runs `CREATE EXTENSION IF NOT EXISTS postgis; CREATE EXTENSION IF NOT EXISTS pg_trgm;`. Idempotent — works on both fresh volumes (where postgis is pre-init'd) and existing volumes (where it isn't). Same migration runs against Supabase prod where both extensions are available but require explicit `CREATE EXTENSION` to enable. Single source of truth.

**Operational pattern (documented in `docs/local-dev-workflow.md`):**

| Concern | Local | Production |
|---|---|---|
| Image | `postgis/postgis:16-3.4-alpine` | Supabase Pro Postgres 16 + PostGIS 3.4 |
| Connection | Direct TCP, prepared statements OK | Supavisor pooler, `prepare: false` required |
| Migration command | `pnpm --filter @homecircle/db db:migrate` against local URL | Same command against Supabase URL via Railway deploy hook |
| Schema drift detection | `drizzle-kit check` runs in CI, fails build if generated SQL doesn't match committed migrations | Same check in CI gates Railway deploy |
| Backups | None (ephemeral local) | Supabase Pro daily backups + 7-day point-in-time recovery |
| Reset | `docker compose down -v && docker compose up -d && pnpm db:migrate` | Never reset prod; rollback via PITR if needed |

**Trade-off acknowledged:** Choosing Supabase as the prod DB host while NOT using Supabase CLI locally means we're trusting that "Drizzle SQL runs identically on both" remains true. If Supabase ever introduces a Postgres-flavor divergence (custom syntax, forced extensions, etc.) we'd find out at deploy time, not local-dev time. Mitigation: run migrations through staging Supabase project before prod, verify schema drift in CI. Risk is low because Supabase's Postgres is intentionally vanilla — they sell on "no lock-in, just Postgres."

## 2026-05-16 — Destructive red unified to `action-destructive` token (`#D14B47`)

**Decision:** All destructive surfaces in the file bind their red fills to `colors/semantic/action/action-destructive` (aliases `red-500 = #D14B47`). The previous raw `#C25E5E` value used on the Vouch Delete Confirm Sheet (`842:2`) and Delete Account Confirm Sheet (`785:12`) — and which I'd propagated to the new Toast Error icon earlier in the session — is retired.

**Three reds discovered, conflicting:**
- `colors/primitives/red/red-500` (the token system's value) = `#D14B47`
- Vouch Delete + Delete Account sheets (May 3, raw) = `#C25E5E`
- Toast Error icon (my first pass, raw, matching the sheets) = `#C25E5E`

**Three resolution paths considered:**
- Align everything to token `#D14B47` (token wins, retrofit the sheets)
- Update the token value to `#C25E5E` (sheets win, change the token + any code-side mirror)
- Leave the drift unresolved as known tech debt

**Why path 1:** the token system is the canonical source of truth for the design system. The sheets predated the token system; they were authored with raw hex because tokens didn't exist yet. Treating them as drifted-from-token and bringing them in line is the right architectural direction. Slight visual delta (sheets' red shifts slightly brighter / more saturated) but the system becomes self-consistent.

**Executed today:** 6 raw `#C25E5E` fills bound across the 2 sheets — the destructive button frame fill, the muted `@10%` ellipse backplate, and the "!" text glyph on each. Opacity preserved on the @10% ellipse via the post-bind re-apply (the Figma API gotcha where `setBoundVariableForPaint` resets paint opacity to 1). Toast Error icon also rebound. Input atom error state + FormField error state were already bound correctly via Milind's draft work.

**Future destructive surfaces** must bind to `action-destructive` (for borders + icons + filled buttons) or `text-destructive` (for inline error text content) — the semantic split preserves room for future theme work where destructive text and destructive surfaces might diverge for accessibility/contrast. Both tokens currently alias `red-500` so they're visually identical.

## 2026-05-16 — Day 6 atom architecture: 5 atoms shipped + token-bound

**Decision:** Day 6's "Error UI primitives" deliverables expanded into 5 reusable Figma atoms, all bound to the `Tokens / V1` color collection (54 color variables, semantic + primitive). Final atom inventory:

| Atom | Node | Variants | Tokens bound | Purpose |
|---|---|---|---|---|
| Toast | `1198:47` | 4 (Success/Error/Info/Compact) | 10 | Bottom-mounted notification; Card-form for Success/Error/Info, Pill-form for Compact |
| Input | `1211:17` | 5 (Default/Focused/Error/Disabled/Filled) | 11 | Single-line text field; Filled state added today for pre-filled values |
| FormField | `1215:68` | 10 (5 states × 2 required) | 12 | Label + Input INSTANCE + helper/error text slot. Embeds Input as live instance for downstream propagation |
| ProCard | `1227:90` | 3 (tier=1stCircle/MutualFriend/Neighbor) | 11 | Canonical pro-led card pattern; consumed by Home / Saved / Search Results |
| LocationInput | `1232:16` | 2 (state=Idle/Loading) | 8 | Input + GPS icon button; permission-on-intent geolocation |

**Architectural notes:**

- **FormField embeds Input via live instances (not clones).** Future Input atom changes propagate to all 10 FormField variants automatically. Same pattern applied during Quick Setup conversion — the screen's form fields are FormField instances, not inline copies.
- **Toast variant `type=Compact` uses full pill rounding (9999)** while the Card-form Toast variants (Success/Error/Info) use `16px` matching Standard Card spec. The split is deliberate: pill form signals "ephemeral / lightweight / action-oriented," card form signals "structured notification."
- **Input atom defaults genericized** from `"Phone number"` / `"(415) 555-2671"` placeholder text to `"Placeholder"` / `"Sample input"`. The phone-specific defaults read as prescriptive — designers using the atom might think it was phone-only. Generic defaults signal "override this with your content."
- **Helper text slot in FormField is always reserved (24h)** even when empty (Default/Disabled variants). Layout consistency across variants > tight vertical packing on Default.
- **Mixed-font ranges preserved by NOT overriding** — attribution text like "Vouched by **Sarah J.** + 3 others" has a bolded substring. Figma's `node.characters = ...` setter flattens mixed ranges. The ProCard adoption pass exposed this: 4 Neighbor cards with overridden attribution lost the "Willow Glen" bold. 5-min fix via `setRangeFontName` queued.

**Figma API gotcha learned today:** `figma.variables.setBoundVariableForPaint(paint, 'color', variable)` returns a paint with `opacity` reset to 1, regardless of the paint's input opacity. Must re-apply opacity after binding. Hit this twice today — once on the Toast Undo button (`white @ 12%`) and once on the destructive sheet ellipse backplate (`#C25E5E @ 10%`). Documented for future binding work.

## 2026-05-16 — Permission-on-intent canonical pattern (Contacts + Location)

**Decision:** Native OS permission prompts (Contacts, Location) only fire on explicit user action — never on screen mount. The pattern was already locked for Contacts (Day 9 Sync Choice via Onboarding Sheet); today's LocationInput atom extends the same pattern to Location.

**LocationInput specifics:**
- Screen mount: no prompt. Field renders with subtle GPS crosshair icon at `text-quaternary` on the right edge.
- User taps GPS icon: native iOS / Android location permission prompt fires.
- Permission granted: icon flips to `action-primary` (Loading state), async geolocation runs, reverse-geocode populates field, helper text updates to "Using your current location: {city}, {zip}".
- Permission denied: toast "Couldn't get your location — type your zip below" with field re-focused. NO automatic re-prompt (iOS permission dialog only shows once per install).
- Previously-denied state on subsequent visits: tap surfaces "Open Settings to enable location" toast instead of re-prompt (iOS limitation).

**Rationale:**
The cognitive friction of OS permission prompts comes from being unexpected. When the user explicitly initiated the action ("I want the app to find me"), they expect the prompt and grant it. Pre-mount prompts force users to make a decision about a system they don't yet understand — denial rates spike, and once denied, iOS doesn't show the prompt again, blocking the feature for the user's whole session (or until they navigate to Settings).

**Why this scales beyond LocationInput:**
- Search "Where" location field (future): same pattern
- Vouch Add New Pro location: same pattern
- Edit Profile zip: same pattern
- Day 9 Contacts permission via Onboarding Sheet: same pattern (already locked)

**Code primitive (Day 8 plan update):** a shared `useGeolocation()` hook is the single source of truth for the permission-on-intent lifecycle. Consumed by BOTH LocationBadge's GPS button AND LocationInput's GPS icon. Returns `{ status: 'idle' | 'loading' | 'success' | 'denied' | 'error', coords?, postalCode?, city? }`. No mount-time effect; consumer calls `requestLocation()` on user-initiated event only.

## 2026-05-16 — Quick Setup routing branch: new users see screen, returning users skip

**Decision:** The Quick Setup screen is a new-user-only surface. Returning users (phone-switch, reinstall, etc.) bypass Quick Setup entirely after OTP verify and land directly on Home with their stored profile loaded.

**Mechanism:** `POST /auth/otp/verify` response shape extended to include `{ jwt, userId, profileComplete: bool, missingFields?: ['name', 'zip'], ipZip?, ipCity? }`. The mobile client routes by `profileComplete`:
- `true` → skip Quick Setup, land on Home with all server-side data (vouches, saved pros, ledger entries, contact graph) re-hydrated for the new device
- `false` with missing fields → show Quick Setup with any partial fields pre-filled

**Why this matters:**
A user signs up months ago, types name + zip on Quick Setup. Switches phones. Reinstalls. Signs in with same phone number. Forcing them through Quick Setup again — re-typing data we already have — is broken UX. The server already knows them; the client just needs to know to route around the onboarding screen.

**Implications for Quick Setup design:**
Since the screen ONLY serves new users, the design leans into that:
- Both fields required (no skip path)
- Hero copy is screen-level (`"Tell us about you"`) not field-level — both Name and Zip get equal weight
- Zip pre-fills from IP geolocation via Cloudflare `cf.postalCode` returned in the OTP verify response (covers 80%+ of users before they see the screen)
- LocationInput GPS icon allows fast correction when IP guessed wrong

**Welcome-back morph queued (not v1):** OTP success state could render `"Welcome back, [Name]"` for returning users with stored name. ~15 min Figma work; non-blocking.

## 2026-05-16 — ProCard naming locked (vs ProProfileCard / ProVouchCard)

**Decision:** The canonical pro-led card pattern across Home / Saved / Search Results is named **`ProCard`** as a Figma atom (`1227:90`) and React component.

**Rejected names + why:**

- **ProProfileCard** (my initial proposal): ambiguous. "Pro Profile" sounds like the card contains the Pro Profile page, but our actual Pro Profile is the full-screen `1:705` (claimed) or `798:2` (unclaimed) surface. The card LINKS to the Profile but isn't a card-version-of-the-Profile. The name conflates two artifacts.

- **ProVouchCard** (Milind's suggestion mid-session): makes the card sound about the vouch when actually the card is about the pro. Per the May 6 lock, we went pro-led over vouch-led explicitly. The vouch attribution row is metadata explaining WHY this pro surfaces to the user — not the card's identity. Plus the name breaks when:
  - The vouch row is empty (search result with no vouch from network — the card still shows)
  - The tier varies (1st Circle vs Mutual vs Neighbor share the same card; tier is one variant, not the card's identity)
  - If we ever surface a card without a vouch context, "ProVouchCard" becomes wrong

- **ProCard** (locked): clean, accurate, doesn't claim to be a profile or a vouch. Card is fundamentally about the pro; vouch attribution is a slot/component within it.

## 2026-05-16 — ProCard adoption sweep across 4 Home screens

**Decision:** All 10 inline pro cards across the 4 Home screens replaced with `ProCard` atom instances. Atom changes now propagate to all 4 screens automatically.

**Scope:**
- `1:1430` Home Auth - Ledger nudge — 3 cards (1stCircle / MutualFriend / Neighbor)
- `284:2` Home Auth - Fully onboarded — 3 cards (1stCircle / MutualFriend / Neighbor)
- `1:1209` Home Auth No Contacts — 2 Neighbor cards (Flow State Plumbing + Bright Arc Electrical with N-neighbors attribution)
- `21:219` Home Guest — 2 Neighbor cards (same content as 1:1209 with different vouch counts)

**Per-card replacement:**
1. Capture content (pro name, location, pill category, rating, attribution, logo image hash, voucher avatar image hashes)
2. Determine tier (1stCircle / MutualFriend / Neighbor) from existing badge text
3. Create instance of corresponding ProCard variant
4. Apply text overrides for each role-identified text node in the instance
5. Apply image fill overrides via captured imageHash values
6. Position instance at original parent index + (x, y)
7. Delete original inline frame

**Why now, despite earlier deferral:**
The earlier session-internal deferral reasoning ("Home screens aren't finalized") was reconsidered: the ProCard atom IS stable (we just locked it), and adoption is visually neutral (the atom was cloned FROM these cards). Future home-screen polish becomes EASIER with atom adoption, not harder — atom changes propagate to all 10 instances without manual screen edits.

**Known minor regression:** mixed-font ranges on overridden attribution text got flattened. Specifically the bolded "Willow Glen" substring at the end of the 4 Neighbor cards' attribution ("Vouched by N neighbors in Willow Glen") lost its bold weight when the override fired. Figma's `node.characters = ...` setter resets mixed ranges. Documented for follow-up: re-apply Bold to the "Willow Glen" range via `setRangeFontName` — 5 min total across the 4 cards.

**Atom-vs-content separation now clear:** ProCard atom defines styling, structure, token bindings. Instances on screens define content (text overrides, image fills). Future product/copy iterations happen at the instance level without touching the atom.

## 2026-05-16 — Floating labels rejected for v1; pattern-by-context locked

**Decision:** Stick with two pattern choices by context:
- **Multi-field forms** (Quick Setup, future Edit Profile, future Vouch Composer) — fixed-top labels via FormField molecular
- **Single-field sheet surfaces** (Auth Sheet phone input `1:859`, OTP sheet `1:932`) — no top label, placeholder serves as label-by-context

Reject Material Design floating labels (label inside field at full size when empty, shrinks-and-rises to above-field position on focus or when filled).

**Why reject floating:**
1. **Not iOS-native.** Apple Mail, Messages, Settings, Wallet, App Store — none use floating labels. They use fixed-top or placeholder-only. Floating labels are a Material Design pattern; importing them into an iOS-first MVP makes the app feel less platform-native.
2. **Implementation cost.** Floating labels need: animation state machine (empty → focused → filled), label positioning logic, font-size interpolation, accessibility handling (screen readers handle this poorly without explicit ARIA work). React Native libraries exist but are imperfect. For a Day 6 implementation, this is 2-3 extra hours per field of careful work.
3. **Solves a problem we don't have severely.** Quick Setup has plenty of vertical space. The ~24px saved per field by floating labels doesn't unlock a meaningfully better layout.
4. **Required asterisk gets awkward.** With the label inside the field, the red `*` ends up inline with placeholder-sized text. When the label shrinks up, the asterisk shrinks with it and becomes nearly invisible.
5. **Loses the ability to show an example as a separate hint.** Label + placeholder gives two slots: "what this field is" AND "what shape the answer takes." Floating collapses to one slot.

**The redundancy Milind originally noticed (4 affordances pointing at "type your name") was a copy problem, not a label-pattern problem.** Solved by rewriting Quick Setup hero from `"What should we call you?"` to `"Tell us about you"` — moving from field-level question to screen-level framing. Both field labels then earn their place by disambiguating different fields.

**Revisit floating labels** if/when we add a screen where vertical space genuinely doesn't allow top labels — at that point the implementation cost might be worth it.

## 2026-05-16 — Input atom success state deferred (symmetry-only argument insufficient)

**Decision:** Input atom locked on 4 canonical states (Default / Focused / Error / Disabled) plus the Filled state added today for pre-filled-value rendering. No "Success" state in v1.

**Asked but rejected:** a success state symmetric with the error state — green border + checkmark icon when a field is validated correct.

**Why reject:**
1. **No MVP use case warrants it.** Walking through every Input field in v1:
   - Phone number → no real-time validation; success = OTP screen renders
   - OTP → auto-submits on the 6th digit; success = screen morph
   - Name → no validation, any name valid
   - Zip → already validated via richer "Looks like San Jose..." helper text confirming the IP-derived city

2. **The Quick Setup Zip already shows something better than a checkmark.** Helper text "Looks like San Jose — we'll show pros near you" is **contextual confirmation** — it tells the user not just "this is valid" but "and here's what it means for your experience." A generic checkmark glyph would be a *less* informative replacement.

3. **The argument for adding success is symmetry-with-error.** Weakest reason to add UI. Errors need an affordance because users must know WHICH field to fix when a form fails to submit; success doesn't have the same job-to-be-done.

**When to add a success state in the future:**
- Username availability check (async, definite binary outcome)
- Email verification handshake ("We sent you a code" → "Verified ✓")
- Real-time field validation where helper text is too noisy (e.g., password complexity checks where ✓ appears as criteria are met)

None exist in v1 scope. If/when one does, success state takes ~15 min to extend onto Input + FormField.

## 2026-05-16 — Quick Setup screen composed entirely from atoms

**Decision:** Quick Setup's two form fields are built as **FormField INSTANCES** (not inline FormField copies). Specifically:
- Name field = `FormField` instance, variant `state=Focused, required=true` (Focused because the field auto-focuses on screen mount)
- Zip field = `FormField` instance, variant `state=Filled, required=true` (Filled because the field renders with IP-pre-filled value before user touches it)

**Why instances vs inline:**
- Future FormField changes propagate to Quick Setup automatically
- Demonstrates the atom is real and consumed (not just decorative)
- Other future screens consuming FormField (Edit Profile, Vouch Composer, etc.) inherit the same propagation behavior

**Trade-off accepted:** Name field grew from 89h → 121h because FormField variants all have a reserved 24h helper-text slot (empty in Default/Disabled/Focused-without-helper variants). Layout consistency across variants > tight vertical packing on Default. Quick Setup adjusted accordingly; Continue button stayed at y=812 (stable bottom-of-screen anchor).

**The `Filled` Input atom state** was created today specifically for Quick Setup's Zip field — the existing 4 Input states didn't model "field has a pre-filled value but isn't currently being typed in" (Default = placeholder styling, Focused = focused border + filled text styling, neither matched the IP-prefilled-but-unfocused case). New Filled state has default border styling + filled-text styling. The corresponding FormField `state=Filled` variant was added afterward to enable the FormField wrapper.

## 2026-05-25 — V1 Plan Patch Integration Session (20 patches from journey-definition work)

**Context:** Milind ran a journey-definition session and produced a `V1 Plan Patches` doc enumerating 20 gaps + restructures the May 8 v1-day-by-day-plan needed before sketching could begin. This entry records the architectural decisions that landed during the patch review + integration work. The patch list and per-patch deliverables live in `v1-day-by-day-plan.md` (tagged inline as `PATCH N`); this entry captures the *why* behind the patches that involve real product or engineering trade-offs.

### Decision 1: Save / Vouch unification — one data model, two render modes (PATCH 3)

Save and Vouch were originally separate flows (D16 + D17). Collapsing them into one `saves` table with optional `rating` + `review_text` + `visibility` columns. `has_review = (rating IS NOT NULL)` is the vouch discriminator. AddProComposer is the single composer; rating + review fields are optional; dynamic submit label ("Save to your list" vs "Save & Vouch — visible to neighborhood"). Heart-tap = silent save with Circle default, no composer. The graduation case (save → add review) is just an UPDATE on the same row.

**Why:** matches the unified add-flow we designed May 7, eliminates dual-write headaches on graduation, single source of truth per record. Sort logic (Vouched-multi > Vouched-single > Saved-multi > Saved-single) preserved via has_review + visibility joins, not separate tables.

### Decision 2: Save visibility model — per-pro, NOT list-level (flip-flopped twice, locked May 25)

> **🗄️ SUPERSEDED Sept 1 2026 — reversed to list-level on the third flip.** See the Sept 1 entry at the end of this log for the full trail and the evidence that reopened it. Preserved below as the May 25 record.


Final landing: **per-pro visibility** for saves (Private / Circle / Neighborhood), default Circle hardcoded, user override via VisibilityChip on Saved tab row or VisibilitySelector in AddProComposer.

**Flip-flop trail:**
1. May 7 entry (line 69): per-pro chosen
2. May 25 morning discussion: Chad recommended flipping to list-level (simpler, contractor domain doesn't have the cross-domain privacy heterogeneity that justifies per-pro)
3. May 25 afternoon: Milind weighed the simplification but decided to keep per-pro for v1
4. **Final: per-pro stays.** Decision logged here to make the flip-flop visible so future-us doesn't relitigate.

**Why per-pro stayed:** preserves user agency for the occasional sensitive-save edge case (divorce lawyer, marriage counselor — rare in contractor domain but real). Cost of per-pro state is acceptable at MVP scale (~10 saves/user/year). Heart-tap silent save preserves universal heart UX speed; the per-pro override is opt-in via the chip.

**Vouches:** always Neighborhood, no toggle. VisibilitySelector hides when review fields filled in composer.

### Decision 3: "Vouch" never appears in V1 UI strings (PATCH 16 — language refactor)

Internal taxonomy still uses "vouch" (data model column names, event names like `vouch_submitted` for analytics continuity). User-facing language uses "Save" + "Add a review". Component renames: VouchComposer → AddProComposer, VouchRow → ProActivityRow, VouchSuccess → AddProSuccess. "My Vouches" page → "My Activity" (holds both saves and vouches). One UI surface where "Vouch" appears: the submit button label "Save & Vouch — visible to neighborhood" when review fields are filled (makes the visibility-shift contract unmistakable).

**Why:** "Vouch" is jargon users have to learn. Plain language ("Save", "Review") is universally understood. Brand "Vouch" concept lives in tier badges (1ST CIRCLE / MUTUAL FRIEND / NEIGHBOR) and the act of writing a review IS the vouch — no need for the word as a noun in UI.

### Decision 4: Server-side contact hash storage + client-side name overlay (architecture protocol)

Architectural pattern locked: server stores user contact hash sets (pattern 1, not zero-knowledge pattern 2). Required for cross-device portability — reinstall on a new phone restores the friend graph from server state, no need to re-sync contacts before the app works. Client hashes phone numbers locally (HMAC + secret salt), sends hash arrays only; raw phone numbers + contact names + contact photos NEVER leave the device.

Server returns matches with HC display name + HC photo + phone_hash; client overlays contact-card name from local `{hash → name}` map if available. On reinstall with no contact permission yet, attribution renders with HC display name (graceful degradation). After re-sync, attribution upgrades to contact-card name if user has saved a custom label (e.g., "Sarah J." instead of HC display "Sarah Jenkins").

**Why pattern 1 over pattern 2:** Cross-device durability + server-side join performance outweigh the privacy benefit of zero-knowledge. Server having phone hashes is a manageable privacy footprint (hashes alone don't reveal identity; would require cross-referencing); contact NAMES and PHOTOS staying client-only is the meaningful privacy boundary that the client-side overlay preserves.

### Decision 5: Sign-in 3-branch flow + reinstall behavior (PATCH 6)

Post-OTP backend routes by user state: A) Recognized+Onboarded → restore full state via `/me/restore`, route to Home; B) Recognized+Partial (OTP-only) → resume Quick Setup pre-filled; C) Not recognized → fresh signup. Server-side hash storage means Branch A users restore their full friend graph + matches without re-syncing contacts. Soft contact-sync re-prompt offered post-restoration if their local map is empty.

**Why:** eliminates the "treat returning user as new" failure mode that would bite on every reinstall, multi-device login, and partial-account-recovery scenario.

### Decision 6: Refer / Invite is core v1 (PATCH 1) — was missing entirely from May 8 plan

Per-user 6-char base62 referral code; `referrals` attribution table; ShareSheet integration; special Welcome variant for referral-link arrivals with friend pre-attached. Inserted as a new D19. Required for J9, J18, J20 from the journey-definition session.

**Why ship in v1:** core viral growth-loop feature. Without it, every other acquisition channel works in isolation; with it, signups beget signups. Cost: 1 day net add to the schedule.

### Decision 7: Friend Profile screen is core v1 (PATCH 4)

New screen at `/friend/:userId` showing a friend's saves/vouches with server-side visibility filtering (PRIVATE never returned, CIRCLE requires contact match, NEIGHBORHOOD requires zip match). Inserted as a new D22. Implements the "Alex asks Sarah for her contractor list" use case.

**Why ship in v1:** arguably the most viral interaction the app supports — turns a private save list into shareable social capital. Cost: 1 day net add.

### Decision 8: Licensed & Insured DROPPED from v1 (PATCH 14)

No L&I self-attest field on Edit Pro Profile. No L&I line on Pro Profile cards or Search Results. Deferred to v1.5 with actual verification (license-board API or 3rd-party verifier like Veriff/Persona); when re-introduced, returns as a verified BADGE earned through real verification, not a self-attest field.

**Why drop self-attest:** self-attestation creates legal exposure without delivering trust signal value (pros will always claim L&I even if false). The "Self-reported" disclaimer doesn't fully insulate against liability, especially because we're a *trust* marketplace. Better to ship without than ship a half-measure.

### Decision 9: Visibility chip interaction — tap-to-open-sheet, not tap-to-cycle (PATCH 8 refinement)

VisibilityChip on Saved tab rows: tap opens a bottom-sheet picker (3 radio options + Cancel + Save). NO cycle interaction (tap once → Private → tap again → Circle → etc.), NO long-press. Single interaction, standard pattern.

**Why:** tap-to-cycle is unconventional and error-prone (user wants Neighborhood, taps once, gets Private, has to tap twice more to get back to Circle and then forward to Neighborhood — 4 taps for a wrong destination). Long-press has poor discoverability on mobile. Picker sheet is the standard mobile pattern; one tap to pick, done.

### Decision 10: FAB action set is Add-a-Pro / Log Maintenance / Refer Friends (PATCHES 2 + 16)

Three actions in the FAB menu. NO separate "Save" and "Vouch" entries — the unified add-flow means one composer handles both. Refer Friends is the new third action (added with PATCH 1).

**Why:** matches the Save/Vouch unification. Separate Save and Vouch FAB entries would re-introduce the mental model that these are distinct flows, which is exactly what we collapsed.

### Decision 11: Schedule impact — submission shifts Jun 26 → Jun 30 (no buffer remaining)

Net schedule add: +2 new days (D19 Refer + D22 Friend Profile). Submission day becomes Tue Jun 30 (the original Jun 30 deadline buffer is fully absorbed). Public launch target slips from mid-July to late-July. Any Apple-rejection cycle pushes public launch into early August.

**Why accept the slip:** the patches close real product gaps (Refer + Friend Profile are core viral mechanics) and architecture gaps (sign-in 3-branch + deep linking + server-side hash protocol). The cost of shipping without them — missing viral loops, broken reinstall UX — is worse than a 2-day submission slip. If Week 7 slips further, cut-list updated in v1-day-by-day-plan.md "Buffer Strategy" section adds Friend Profile + deep linking as deferrable to v1.5.

---

**Cross-file impact of this session:**
- `v1-day-by-day-plan.md` — major restructure; D16/D17 reframed for Save/Vouch unification; new D19 (Refer) + new D22 (Friend Profile); D21-D35 renumbered to D23-D37; W7 split into W7+W8 with submission on D37 Tue Jun 30
- `v1-spec.md` — Surface Matrix updated (new Friend Profile + Refer/Invite rows; Save/Vouch rows restructured; FAB Vouch → Add-a-Pro); Teaser Logic revised to 3 tiers (added Mutual Friend, dropped Generic Fallback); Vouch Visibility Rules rewritten as Save+Vouch Visibility Rules; Engineering Decisions 3 + 19 amplified; new Decisions 21 (Referral), 22 (Sign-in 3-branch), 23 (Deep Linking); Out of Scope adds L&I; Quick Stats refreshed (7 primary screens, 23 engineering decisions, ~130-140 total surface states)
- `decision-log.md` — this entry
- `sprint-tracker.md` — not yet updated; needs day-number refresh (D16-D19 references etc.) — flag for next sprint-planning session

**Open work flagged for follow-up sessions:**
- Update sprint-tracker.md task references that point to old day numbers
- Decision-log entry #5 (Sign-in 3-branch) needs to inform the Privacy Policy draft on D21 — the policy must disclose the reinstall flow / server-side state restoration
- Verify error-handling-matrix.md has rows for the new patch flows (referral signup failures, Friend Profile visibility-filter edge cases, deep-link malformed-code) — likely needs an additive pass

### Addendum (later May 25) — PATCHES 21, 22, 23 (gap catches during plan cross-check)

After integrating the original 20 patches, Milind ran a cross-check against the journey list and surfaced three additional gaps. Integrated same day:

**PATCH 21 — N13 7-day cooldown scheduling (J25 edge case):** Engineering Decision #14 (Skip Persistence) was already in the spec but no day actually implemented the writer or the reader. Split across D9 (writer — persist `{skipped_at, dismiss_count}` to localStorage on sync skip or permission-denied) and D10 (reader — NudgeBanner suppresses if skipped_at within 7 days; surfaces after; resets on next dismissal). Failure-mode tested D26.

**PATCH 22 — >30-day OTP expiry path (J30 edge case):** Engineering Decision #15 mentioned the 30-day verification window but the specific UX wasn't called out. Extended PATCH 6 (Sign-in 3-branch) to include a pre-branch check on `last_otp_verified_at`; if >30d, returns `otp_expired=true` flag → forced re-OTP screen with "It's been a while — verify your phone to continue" copy. Defensive double-check on `/me/restore` returning 401 with `code: 'otp_expired'`. Failure-mode tested D26.

**PATCH 23 — Google Places UX details (5 sub-items):**
1. **Location bias ~50mi radius** on pro autocomplete (NOT on city autocomplete) — D8
2. **Field-tier discipline:** Basic+Contact only, skip Atmosphere (cost control — Atmosphere is the priciest tier and we don't surface that data) — D8
3. **Category mapping table** (`apps/api/src/places/category-map.ts`) — Google `types` → internal enum, fallback to manual selection for no-match cases — D8
4. **Dedup strategy:** `google_place_id` UNIQUE for Places-sourced; `(normalized_name, phone_hash)` UNIQUE for manual entries — D17
5. **Manual-entry rate-limit:** 5/day per user, rolling 24h window, disabled-state UX after cap — D17

**Schedule impact of PATCHES 21-23:** None. All three are inline additions to existing days (~30-45 min each); no new days, no further submission slip.

**Process note:** these gap catches validate the "cross-check journeys against plan" step before sketching. Three more passes of this gap-catching discipline likely surface 1-3 more items each; budget for that during sketch sessions.

### Addendum 2 (May 25 — D17 progressive disclosure restructure + independent visibility axes)

After integrating PATCHES 1-23, Milind proposed a substantial D17 restructure: replace the unified composer (with dynamic submit label that toggled between "Save to your list" and "Save & Vouch — visible to neighborhood" based on review-fields-filled state) with a two-step progressive disclosure flow.

**Decision A — Progressive disclosure replaces unified composer (D17 restructure):**
- **Step 1 — AddProComposer:** Save action only. Pro search/select + VisibilitySelector (always visible, no hide-on-review-fields logic) + static "Save Contractor" CTA. No rating/review fields.
- **Step 2 — ReviewSheet:** Auto-shown bottom sheet after Step 1 save success. Optional rating + review. Reusable in 3 entry points: post-save intercept, Pro Profile "Add a review" CTA (auto-saves pro if not already saved), Saved tab "Add/Edit review".

**Why progressive disclosure won:**
- **PATCH 16 stays fully clean.** "Vouch" never appears in V1 UI strings — even the "Save & Vouch — visible to neighborhood" submit label carve-out goes away. Static "Save Contractor" + separate ReviewSheet with friendly copy is the unambiguous UX.
- **ReviewSheet reuse is genuine and load-bearing.** Three entry points collapse to one component instead of three near-duplicate composer pre-fill modes.
- **Low-intent saver has no empty rating fields staring at them.** A user digitizing their plumber off a business card doesn't want a rating field nagging them.
- **High-intent reviewer only adds ~1 extra tap.** Save → sheet appears → fill review → submit. Same end state as unified composer but clearer chunking.
- **Visibility model becomes explicit.** Instead of "selector disappears when review filled" (subtle, may confuse users about what just happened), Step 2's header copy explicitly says what the review will be visible to.

**Trade-offs accepted:** Two screens instead of one for high-intent users (real but small cost); ReviewSheet has to handle reuse correctly across 3 entry contexts (more state management — acceptable).

**Decision B — `save_visibility` and review publicness are INDEPENDENT axes (correction to earlier draft):**

Earlier drafts had review submission auto-upgrade `save_visibility` to NEIGHBORHOOD (the "graduation" UX with "Vouches are public reviews — your save will become visible to your neighborhood. Continue?" warning). Milind challenged this: the review is public, but the SAVE's visibility (controls whether friends see "Saved by Marcus" in their feeds) is a different signal that should stay user-controlled.

**Correct model:**
- `save_visibility` (Private / Circle / Neighborhood) controls save-feed broadcast — user-controlled per save, default Circle
- Review publicness (implicit in `has_review`) is independent — reviews always render on the pro's profile + influence search ranking, regardless of `save_visibility`
- Adding a review does NOT modify `save_visibility`. A user can save Private + write a public review: save stays Private (no friend-feed broadcast), review appears publicly on pro profile.

**Why this is the right model:** The privacy goal of Private save was "don't broadcast this save via my social feed" — not "no friend can ever discover any connection." Reviews are a separate surface with conventional public semantics (the same as any platform — Yelp reviews are public regardless of who wrote them). Conflating the two axes was a category error.

**Search-ranking implication:** When bubbling up a pro in search, reviews count as public signals (all of them, regardless of reviewer's save_visibility); Private saves are excluded from social-proof scoring (user explicitly opted out of broadcast). This naturally separates the two axes in the ranking model.

**Backend query semantics (load-bearing):**
- Friend feed: `WHERE save.visibility != PRIVATE` (save-attribution queries respect visibility)
- Pro profile Reviews section: `WHERE save.rating IS NOT NULL` (reviews bypass visibility — always public)
- Pro profile "Also saved by": filtered by `save.visibility` (savers respect visibility)
- Search ranking: reviews counted; Private saves excluded from social-proof signal

**Decision C — Three sub-decisions confirmed:**
1. **"Add a review" CTA on Pro Profile always-visible.** Tap auto-saves the pro (default Circle visibility, silent — no toast, no composer) and immediately opens ReviewSheet. 1 tap, 1 screen for the high-intent reviewer regardless of prior save state.
2. **Step 2 ReviewSheet auto-shown immediately on save success.** Not a delayed toast. Less ambiguity. 1-tap "Not yet" dismisses for save-only users.
3. **Event taxonomy:** Step 1 = `pro_saved`; Step 2 = `vouch_submitted` (internal taxonomy stays `vouch_*` for analytics continuity even though UI says "review"). New event `review_skipped { source }` distinguishes "save-only intent" from "wanted to review but errored out". New event field `save_visibility_at_submit` on `vouch_submitted` captures whether reviewers tend to have Private or public saves (useful for ranking-model tuning).

**Component renames cascading from this decision:**
- `AddProSuccess` → **`SaveSuccess`** ("AddPro" implied pro-creation which is only one path; saves on existing pros are common too, so "SaveSuccess" is more accurate)
- Dual-copy state on SaveSuccess goes away — always says "Saved [Pro Name] to your list" + visibility readout. ReviewSheet has its own post-submit toast: "Review posted — neighbors searching for [Category] will see it ✓"

**Cross-file impact:**
- `v1-day-by-day-plan.md` — D17 fully restructured; knock-on changes to D7 (added `addReview` trigger), D11 (always-visible "Add a review" CTA + auto-save behavior), D14 (locked CTA in guest variant), D16 (split tap-row Edit into "Edit details" vs "Add/Edit review"; VisibilityChip now ALWAYS interactive including on reviewed rows — correction to earlier draft), D18 (AddProSuccess → SaveSuccess rename; single-copy state), D26 (added PATCH 23 backend implementation work + independence-of-visibility-axes verification test)
- `v1-spec.md` — "Save + Vouch Visibility Rules" renamed to "Save + Review Visibility Rules"; section rewritten for independent axes; Surface Matrix updated (AddProComposer Step 1 now save-only with always-visible selector; new ReviewSheet Step 2 row); Engineering Decision #19 rewritten
- `decision-log.md` — this addendum
- `sprint-tracker.md` — not yet updated; AddProComposer reference renamed; ReviewSheet added as new component
- `v1-component-inventory.md` — needs ReviewSheet added; SaveSuccess rename; flag for next inventory pass

**Schedule impact:** None. Restructure of the same scope (1 simpler component + 1 small new component vs 1 heavy component). Net dev time roughly equivalent.


## 2026-07-03 — FAB label "Add a Pro" → "Add a Contractor"

**Decision:** The user-facing FAB action label changes from "Add a Pro" to **"Add a Contractor."**

**Rationale:** Consistency with the action/marketing register already in use — the Welcome hero reads "Find contractors trusted by your friends" and the composer CTA is "Save Contractor." "Add a Contractor" matches those; "Add a Pro" was the lone "pro" in the full action phrasing.

**Scope — two-register split (locked):**
- **"Contractor"** is the user-facing term in full action/marketing phrases: "Add a Contractor," "Save Contractor," "Find contractors…"
- **"Pro"** is retained as compact object shorthand in dense labels and object/type names: card subtitles ("Share a pro you trust"), Pro Profile, and internal component names (`AddProComposer`, `ProCard`, `ProAutocomplete`) — these do NOT change.

**Applied:** Live FabActionMenu in Figma (`1496:340`) relabeled + title set to auto-width (the longer string wrapped otherwise). Docs updated: `JOURNEY_DEFINITION_NOTES.md` (FAB menu + post-save routing table + J5 + vocabulary note), `v1-spec.md` (Surface Matrix FAB row), `v1-design-build-inventory.md`, `v1-day-by-day-plan.md` (D-plan FAB actions). Internal component names intentionally left as `AddProComposer` etc.

**Open:** the composer's own header string will read "Add a Contractor" when built (component file name stays `AddProComposer`).


## 2026-07-03 — Mutual-friend attribution → possessive form

**Decision:** The Mutual Friend tier attribution changes from "Reviewed by a friend of **Marcus C.**" to **"Reviewed by Marcus C.'s friend"** (bridge name bolded, "'s friend" regular). Save form parallels: "Saved by Marcus C.'s friend."

**Rationale:** Milind's call after seeing both rendered on the TierTeaser. Possessive is more compact and leads with the recognized person (the trust anchor). Noted tradeoff: with our abbreviated "First L." name format it creates a `C.'s` cluster and the bold ends just before the apostrophe — accepted as fine.

**Applied:** Figma — `TierTeaser` (`1543:355`) MutualFriend variant + `ProCard` (`1227:90`) both mutual-friend variants. Docs — `JOURNEY_DEFINITION_NOTES.md` (Principle 5 + tier-teaser resolution), `v1-drift-fix-spec.md` (§1 ProCard copy table + §2 TierTeaser), `v1-spec.md` (teaser logic), `v1-day-by-day-plan.md` (D-plan teaser copy). Historical session notes left unchanged.


## 2026-07-03 — Icon system: standardize on Phosphor

**Decision:** Adopt **Phosphor Icons** as the single icon system (design + code), replacing the ad-hoc sleek-baked glyphs.

**Why Phosphor over Lucide / Material Symbols:** our components lean on **filled-vs-outline state pairs** (SaveHeart empty/filled, star empty/filled, active/inactive tabs). Phosphor ships the same icon in an outline weight AND a Fill weight, so those pairs stay consistent within one family — Lucide is stroke-only (filled states need manual fill). Phosphor: 9,000+ icons, 6 weights, MIT license, `phosphor-react-native` for the RN/Expo build, official Figma plugin. Warm/rounded feel fits the brand (and the rounded corners fix the lock-icon gripe that prompted this).

**Conventions (locked):**
- Default UI weight = **Regular** (outline). Use **Fill** for selected/active/on states (filled heart, filled star, active tab). Lock uses Regular.
- Base grid 24px; scale to 20/16/14 as needed. Bind icon color to the semantic text/action tokens (don't bake hex).
- Code: `phosphor-react-native`. Design: Phosphor Figma plugin (or SVG import via `figma.createNodeFromSvg`).

**Icon mapping (v1 needs → Phosphor):** Lock → `Lock` · Circle/friends → `UsersThree` · Neighborhood → `Globe` (or `MapPin`) · chevron → `CaretDown` · heart → `Heart` / `Heart`(Fill) · star → `Star` / `Star`(Fill) · search → `MagnifyingGlass` · add → `Plus` · kebab → `DotsThree` · back → `CaretLeft` · report → `Flag` · block → `Prohibit`.

**Follow-up:** replace the sleek one-off icons in the built components (`VisibilityChip` lock/people/globe/chevron; `SaveHeart` heart/lock/spinner) with Phosphor; optionally swap the `createStar` stars for Phosphor `Star`/`Star`-Fill for full consistency.


## 2026-07-06 — ProCard badge consistency + anonymized "a neighbor" mode

**Badge consistency:** The Neighborhood pill on `ProCard` was the odd one out — label "NEIGHBOR" and an outlined style, while 1st-Circle/Mutual-Friend were token-bound filled pills. Relabeled to **"NEIGHBORHOOD"** and switched to the cream-filled `badge-neighbor-bg` / `badge-neighbor-text` tokens (widened + right-aligned the pill). All three tier badges are now consistent filled pills. Also removed a **stray leftover heart button** (an old duplicate `Button` at y≈67) that was hidden behind the reviewer avatars on reviewed variants but exposed on saved variants, producing a double-heart.

**Anonymized mode (decision):** Added a **`count` axis (one/many)** to `ProCard`, mirroring `TierTeaser`. Neighborhood `count=one` reads **"…by a neighbor in Willow Glen"** (singular, anonymized — no name, no avatar); `count=many` keeps **"…by N neighbors in Willow Glen"** (aggregate social proof). Chosen over replacing the aggregate (would lose multi-neighbor proof) or keeping aggregate-only (would render "1 neighbors"). Both attribution types (reviewed/saved) get the singular variant.

**Applied:** Figma — `ProCard` (`1227:90`) now **8 variants** (tier × attribution × count); two net-new neighborhood `count=one` variants. Docs — `v1-design-build-inventory.md` (ProCard row → ✅ done). Resolver note: `resolveTierForPro` should emit `count` for the neighborhood tier the same way it does for `TierTeaser`.


## 2026-07-06 — Permission primer = in-app bottom sheet only (v1)

**Decision:** In v1 the contact-sync permission primer is **always an in-app bottom sheet**, never a standalone full-screen page — for every entry point (Home N8 nudge, locked-matches card, optional Search empty-circle band, J25 re-prompt).

**Rationale:** Every sync entry point is contextual — the user is looking at a specific surface and sync lights *that* up. The sheet keeps the surface **dimmed-but-visible** behind it, which is the persuasion itself ("sync to reveal the friends behind this feed you're already looking at"). A full page severs that context and reads as a chore-step. A full-page primer only earns its place as a mandatory dedicated onboarding step — and we explicitly pulled sync out of onboarding (lean 3-step Phone→OTP→Name sheet; sync deferred to N8), so that case doesn't exist in v1.

**v1.1 flag:** Referral entry (**J20**) — a friend-pre-attached arrival where seeing the friend's circle is the whole point — may warrant a fuller "connect to see **Marcus**'s circle" screen. Revisit only if/when referral onboarding ships. Not v1.

**Precondition (governs all primer surfaces):** The primer only appears when contacts permission is **not yet granted**. Already-granted re-sync re-reads contacts silently — no primer, no OS dialog.

**Sheet content (working spec):** small friends icon · payoff-led headline · one hashed-privacy paragraph ("we match your contacts privately — hashed on your device, never uploaded or stored") · "Find my friends" primary · "Not now" secondary.

**Applied:** Docs — `permission-primer-triggers.md` (Presentation section RESOLVED). Figma — the full-screen `Contact Sync Permission` frame (`1625:340`) is superseded; primer to be rebuilt as a bottom-sheet. Midjourney hero prompt for the primer is moot for v1 (sheet uses a small Phosphor icon, not a hero illustration).


## 2026-07-16 — Post-sync success ("14 friends found") = full screen, not a sheet

**Decision:** The contact-sync success payoff is a **full-screen** moment (`SyncSuccess`), not a state of the primer sheet.

**Rationale:** The bottom sheet is right for the *primer* because its job is to keep the underlying surface visible ("sync to reveal the feed behind me"). The success moment is the opposite — it's the payoff reveal, the emotional peak of activation, and it deserves the full canvas: room for the count to land big, avatars to animate in, a celebratory beat before dropping the user into their now-lit Home. A sheet with a dimmed Home behind it reads as "minor confirmation" and, worse, would show the underlying Home *churning* (anonymous→named, feed re-sorting to friend-first) through a translucent backdrop. Full-page cover → do the work → reveal the transformed Home clean.

**Beat sequence:** primer (sheet) → iOS permission dialog → **full-page `SyncSuccess`** ("14 friends found", celebratory) → tap "See my feed" → lit Home.

**Built:** Figma — `SyncSuccess` full screen `1646:334` in Canonical Screens / V1 (paper bg `surface-default`, Phosphor `CheckCircle` in `action-primary`, "14 friends found" DM Sans Bold 28, subline, "See my feed" primary; all token-bound). The "14" is dynamic.

**Loading note:** No spinner on the primer's "Find my friends" button — during the iOS dialog our sheet is backgrounded (nothing to animate). The only real wait is *after* Allow while we hash-on-device + POST `/contacts/sync` + shadow-match (~1–2s); that "Finding your friends…" transitional state sits between the dialog and `SyncSuccess` (candidate: refresh the legacy `Loading Circle` 1:2911, or a lightweight full-screen loader). Not yet built.


## 2026-07-16 — Sync processing state + zero-match branch (one continuous surface)

**Decision:** Contact sync isn't instant (hash-on-device → upload hashes → reverse-match against graph → compute tiers ≈ 2–4s), so between the iOS dialog and the reveal there's a **branded processing state**, not a bare spinner. It lives on the **same full page** that becomes the reveal, so it *morphs* rather than cutting to a new screen.

**Beat sequence:** primer (sheet) → iOS dialog → **"Finding your circle…"** (processing, 2–4s) → **"14 friends found"** reveal → tap → lit Home.

**Design-in requirements:**
- **Floor duration ~1.5s minimum** even if the match returns instantly — the anticipation makes the reveal land harder; never flash past it.
- **Zero-match branch:** if sync finds no friends, the processing state resolves to a **graceful** "No friends here yet — here's what your neighborhood trusts" (routes to neighborhood feed), NOT the celebratory reveal. The loader must not imply a payoff that isn't coming.

**Motion (code — can't live in static Figma):** icon holder pulses during processing (optional count tick-up); on match-return *after the floor*, morph in place — holder → `SuccessCheck` (found) or → `Globe` glyph (zero-match); "Finding your circle…" → the resolved headline; progress dots → button fades in. Continuous surface, never a hard cut.

**Built (Canonical Screens / V1, all morph-aligned — icon slot + headline share the same positions):**
- `SyncSuccess — Finding` `1663:298` — forest-50 people holder, "Finding your circle…", working subline, progress dots (no button).
- `SyncSuccess — Found` `1646:334` — `SuccessCheck`, "14 friends found", "See my feed".
- `SyncSuccess — No matches` `1664:298` — `Globe` glyph, "No friends here yet", neighborhood pivot, "See neighborhood pros".


## 2026-07-16 — Contact-sync denial result = centered alert dialog (J25)

**Decision:** After the user denies the iOS contacts prompt (tapped "Find my friends" → OS dialog → "Don't Allow"), we show a brief **centered alert dialog** — not a bottom sheet, not full screen. The primer borrows context (sheet); this is a low-stakes acknowledgment that just needs a tap to dismiss back to Home, so a small centered modal fits.

**Copy (from sketch):** title "Contacts access off" · body "No problem — you're still in. We'll remind you later if you'd like to find your friends." · primary "Got it". Reassuring, non-punitive; sets up the J25 7-day re-prompt without pointing at a Settings toggle (there isn't one).

**Path distinction:** primer **"Not now"** → just dismiss the sheet back to Home (no dialog — they never engaged the OS). Primer **"Find my friends" → OS "Don't Allow"** → this dialog. Both are J25; only the OS-denial branch earns the acknowledgment.

**Built:** Figma — `ContactsDeniedDialog` `1667:340` (Components / V1). Centered `surface-card` card, rounded 24, drop shadow; token-bound. Backdrop/scrim consumer-managed (like `BottomSheet`). Could generalize to a reusable centered `AlertDialog` primitive later.


## 2026-07-16 — ProActivityRow = extend ProCard (don't fork); reviewer-forward reviewed attribution

**Decision:** The Home "Recent activity" feed row is **not** a separate component. A pro + its strongest social signal in the same list context is the same object as ProCard — a separate `ProActivityRow` would duplicate ~90% of the layout and drift. The reviewer-forward treatment is just a richer **reviewed-variant attribution line** on `ProCard`.

**ProCard attribution line now switches on signal type:**
- **Reviewed** (has_review) → reviewer name (bold) + inline amber stars + single-line truncated quote: "**Sarah J.** ★★★★★ 'great work, fair price'" (1st-circle); "**Marcus C.**'s friend ★★★★★ 'reliable, came on time'" (mutual, anonymized name).
- **Saved** → "Saved by Sarah J." — plain, no stars (locked marker distinction).
- **Neighborhood** → "Reviewed by N neighbors in Willow Glen" / "a neighbor" — count only, no stars/quote.

**Guardrails:** (1) tier badge always travels with the card so a stranger's review never reads as a maybe-friend; (2) quote is single-line truncated with ellipsis — the card is a teaser, the full review lives on the Pro Profile.

**Applied:** Figma — `ProCard` (`1227:90`) reviewed variants reworked (attribution → reviewer-forward; aggregate stars row + voucher avatars hidden). `ProActivityRow` retired as a separate component (was ❓). Home / Search / Saved all render the same card.


## 2026-07-16 — Minimal consumer profile edit (tap-header → EditProfileSheet)

**Decision:** The consolidated Profile shows identity read-only; the lightest v1 edit path is: **tap the name/location header (small pencil affordance) → compact `EditProfileSheet`**. No dedicated "Edit Profile" menu row (that's Pro-Profile editing, J22, deferred v1.1).

**Editable vs immutable:** Name + Location editable (Location reuses the shared `LocationAutocomplete`); **Phone is the immutable identity key** — shown locked (gray field + lock icon), never editable.

**Built:** Figma — `EditProfileSheet` `1723:233` (BottomSheet shell): NAME (editable pill), LOCATION (editable pill + caret → picker), PHONE (locked), "Save changes" + Cancel. Follow-up: add the header pencil affordance when assembling `ProfileScreen`.


## 2026-08-27 — Attribution rule: names are public, relationships are not

**The rule.** Reviews are public content, so **the reviewer's name shows to everyone — guests included**. What guests do not see is the relationship badges: `1ST CIRCLE` / `2ND CIRCLE` are derived from a contact graph the guest hasn't provided, so they're meaningless and leaky pre-sync. Names public, relationships private.

**Corollary — a save is not a review.** Where a card names someone who only *saved* a pro (no rating, no note), there's no authored content and nothing the person chose to publish. Those rows read as a count instead: "Saved by **3 neighbors**" / "Saved by **a neighbor**". Where the card carries a review quote, the name above it is the byline and it stays.

**What changed in Figma (`Components / V1`, `1055:2`):**
- `ProCard` (`1227:90`) — the two saved-only Neighborhood variants (`1567:399`, `1615:387`) now read as counts. The reviewed variants (`1227:86`, `1615:357`) keep named attribution. Two-tone DM Sans Medium/Bold and bound fills preserved throughout.
- `ProProfileHeader` (`2940:4340`) — the five pre-signup surfaces (`2313:2277`, `2980:11080`, `2311:2184`, `2311:2283`, `2311:2387`) now read "Reviewed by **Sarah J.** & 3 others". Only the false "in your circle" claim was removed; the name stayed. Member states (`1992:1262`, `1992:1317`, `2313:2336`) keep the circle framing.

**Verified:** no `1ST CIRCLE` or `2ND CIRCLE` badge appears on any guest surface — guest Home, guest Saved / My Home / Profile, the sign-up sheet stack, either locked Pro Profile, or `SearchResults · Sign up`. The only pre-sync appearances are the sync primer (`2867:7699`) and the iOS permission screen behind it (`2388:275`), where they are the explanatory legend.

**Net effect on screens is small:** the saved-only variants aren't currently instanced on any screen, so the visible change is the guest trust line losing "in your circle". The saved/reviewed rule is now set in the component for when those variants get used.

**Open follow-up:** `6 · Pro Profile (unlocked)` (`2313:2336`) is post-signup but pre-contact-sync, yet shows a `1ST CIRCLE` badge and "in your circle". The J·Guest → Sign up flow has no sync step between Name and the unlocked profile — most likely the diagram abbreviates past the sync sheet rather than a real bug. Needs resolving either way.

**Note:** supersedes the Aug 26 entry, which over-applied anonymization to reviewed rows. Repo docs are known stale as of this date and are being re-baselined separately — they were not treated as authoritative for this decision.

---

## 2026-09-01 — Error-state model, Button states, and the save schema (session batch)

*These decisions were made across the late-Aug / Sept 1 design sessions and existed only in conversation until now. Recorded together because they interlock.*

### Saves schema — a save and a review are one row

**Decision:** rename `vouches → saves`; `rating` becomes nullable; `trust_note → review_text`; add `reviewed_at`; drop `is_public`; add `UNIQUE (user_id, pro_id)`. Add `users.saves_visibility` (`circle | private`, default `circle`) and `users.contacts_reprompt_at`.

**Rationale:** the shipped table forced `rating NOT NULL`, so a save could not be represented at all, and had no uniqueness constraint, so a double-tapped heart wrote two rows. Saving creates the row; reviewing fills it in; un-saving deletes it. Visibility moved to the user because one setting governs the whole list, not each save. Full SQL in `arch.md` §Saves Schema. **Status: Active**

### Private saves count but are never named

**Decision:** private saves are included in anonymous aggregates but never attributed. v1 does not surface the aggregate count anywhere, and the visibility subline describes *name* visibility, not existence — final copy: "No one can see your saved contractors."

**Rationale:** disclosing counts exposes no PII but invites questions v1 has no UI to answer. **Status: Active**

### Toast carries tone; blocking failures do not use Toast

**Decision:** `Toast` gains `tone = success | error`. No `info` tone in v1. Error is **amber**, not red, and keeps the same dark ground as success. Blocking failures use `FormError` beneath the button instead.

**Rationale:** a checkmark hard-coded into the component meant the component could only say one thing. Error toasts only ever fire when the primary write *did* land and something secondary did not (review saved, photo dropped) — amber is the accurate register, and a red slab overstates a partial success. Keeping the ground constant keeps toasts one recognisable object. **Status: Active**

### Offline is not a toast

**Decision:** offline is an `EmptyState` with `tone = offline` — neutral grey tile rather than the mint one, always carrying an action. `icon=illustration, tone=offline` is deliberately not built.

**Rationale:** offline is a blocking condition, not a transient notification. A grey tile reads as failure; the mint tile reads as a calm empty collection. **Status: Active**

### Button state model

**Decision:** `state = default | loading` as the variant axis. `pressed` and `focus` are runtime overlays, not variants. **`disabled` is not a variant** — it lives in a standalone `Button / disabled` component with one appearance for every variant.

**Rationale:** loading is variant-dependent (the ground stays the variant's colour, the spinner sits on top), so it needs a cell per variant used. Disabled *discards* the variant's colour — a muted fill and label replace it — so primary-disabled and destructive-disabled are the same pixels. Keeping it as an axis would imply a distinction that does not exist. This also makes `destructive/disabled` and `tertiary/disabled` impossible by construction rather than merely undrawn. **Status: Active**

### Disabled must not fade the whole button

**Decision:** disabled is a muted fill (`gray-200`) with a legible label (`gray-500`) at full opacity — never a global opacity drop.

**Rationale:** at `opacity: 0.3` the white label fades with the fill and the button becomes unreadable rather than inert. A disabled control is exempt from contrast minimums but a user still has to be able to read what it says. **Status: Active**

### The disabled test

**Decision:** disabled is acceptable only when the thing that unlocks it is **visible without scrolling** *and* is **an input, not a decision made elsewhere**.

**Rationale:** stars above the button pass. A button disabled because a setting on another screen is off fails — the user cannot act on it from here, so the control is a dead end. **Status: Active**

### Loading semantics

**Decision:** 150ms delay before the spinner appears; 300ms minimum once shown; the label stays in the layout at zero opacity with the spinner overlaid absolutely, so width is preserved; 15s client timeout.

**Rationale:** delay-only gives a 10ms flicker when a response lands at 160ms; minimum-only makes every fast save flash a spinner. Both knobs are needed. The leading icon slot reserves no width when hidden (auto-layout excludes invisible children), so a spinner placed there would make the button *wider* — the overlay is what actually preserves width. **Status: Active**

### Loading has exactly three exits

**Decision:** success → Toast; failure → `FormError`; ✕ → abort and discard. "Failure" is defined by outcome, not cause — timeout, 4xx, 5xx and app-backgrounded all resolve there. Backdrop tap is **inert** during loading; ✕ stays live and aborts rather than closing-and-continuing.

**Rationale:** without a timeout, loading has no failure path and a hung request leaves ✕ as the only exit — which the user reads as "cancel my save" when they wanted "tell me what's happening". Close-and-continue would quietly turn a blocking write into an async one. A backdrop tap is too easy to do accidentally to carry "abandon my write". **Accepted race:** a server commit landing after abort/timeout produces a phantom save; the client ignores the late response and the next load is the truth. Idempotency keys are disproportionate for a millisecond window. **Status: Active**

### Photo upload gets its own timeout budget

**Decision:** the 15s timeout applies to the review write only. Photo upload has a separate, longer budget, and its failure surfaces as Toast · error after the sheet closes — never `FormError`. A rejected *or* slow photo does not block the review.

**Rationale:** sharing the timeout would show "Couldn't reach the server" for a review that saved fine. **Status: Active**

### Write queue scope

**Decision:** queue heart toggles, saves-visibility and mutual-connections toggles only. Collapse per key, last-write-wins. Persist across cold start, cap the length, clear on logout and deactivate, drop on dead reference. Add-a-Pro, reviews and ledger entries block instead.

**Rationale:** the queued writes are idempotent and target rows that already exist server-side, so there is no creation ordering to preserve — only a final value per key. Authored content must be confirmed synchronously. **Status: Active**

### A component must not assert what only the caller knows

**Decision:** general rule. Applied to three cases: the Toast checkmark (now `tone`), the `EmptyState` action's leading plus (**default flipped to off**, Action instance exposed so callers opt in), and `hasIcon` generally.

**Rationale:** a decoration baked into a default becomes a claim the component makes on the caller's behalf. This had already shipped a bug — the Saved empty state rendered "**＋ Find a contractor**", because finding is not adding. Turning it off per-instance treats the symptom; the default is the cause. **Status: Active**

### Opacity tokens are percentage-unit

**Decision:** all seven opacity tokens rescaled from 0–1 to 0–100 and renamed with a `-pct` suffix; `scopes` set to `["OPACITY"]`.

**Rationale:** Figma reads a bound numeric variable in the property's native unit, and opacity is a percentage in the UI even though the Plugin API exposes 0–1. Binding the stored `0.3` produced `0.003` — an invisible node. Applied to all seven rather than just `opacity-disabled`, because one `-pct` token beside six un-suffixed siblings makes the convention actively misleading. Verified zero existing bindings (node-level and paint-level) before rescaling. **Status: Active**

### `action-destructive` darkened

**Decision:** `red-500` changed `#D14B47 → #B0342A` (4.39:1 → 6.31:1 on white). Inherited by `text-destructive` and `action-destructive`; 50 nodes repainted. **Status: Active**

### ABOUT is out of v1

**Decision:** the Pro Profile ABOUT block is deferred. **Rationale:** it cannot be sourced from the Places API, and the v1 profile carries no Places attribution. **Status: Active**

### Contacts denied → centred alert, not a toast

**Decision:** land back on Home with the centred "Contacts access off" alert; re-prompt after 7 days via `contacts_reprompt_at`. **Rationale:** a denied permission is a state the user must acknowledge, not a transient notification. **Status: Active**

### Parked

The sync-step hole — `6 · Pro Profile (unlocked)` (`2313:2336`) is post-signup but pre-sync yet shows a `1ST CIRCLE` badge, and the J·Guest → Sign up flow has no sync step between Name and the unlocked profile. **Status: Parked by decision, revisit post-launch.**

---

## 2026-09-01 — Save visibility is list-level, not per-save (supersedes May 25)

**Decision:** save visibility is a single account-level setting — `users.saves_visibility` (`circle | private`, default `circle`) — governing the whole saved list. The May 25 per-save model (a `visibility` column on the save row with three levels: `Private / Circle / Neighborhood`) is superseded.

**Reverses:** the May 25 "Rationale for per-pro (not list-level)" entry in `v1-spec.md`, which argued for per-save agency to cover sensitive saves.

**Rationale:**
1. **The sensitivity case doesn't transfer.** Per-pro visibility was justified by the divorce-lawyer-style edge — a save the user wants hidden from everyone. Home Circle is home services. A plumber does not carry that weight, so the edge case the complexity was buying doesn't exist in this category.
2. **Three levels ask for a distinction users won't make.** "Circle" vs "Neighborhood" is not a line people reliably draw at the moment of saving, and getting it wrong is invisible to them.
3. **The copy was already list-level.** "No one can see your saved contractors" and "You and your friends can see each other's saved lists" are both statements about the list. Under the per-save model they were quietly inaccurate.
4. **Cheaper schema, less UI.** No per-row visibility state, no picker on every card, one setting to explain.

**Three rules replace the old two-axis model:**
- `saves_visibility` governs **named save attribution only**
- **Reviews are always public and always bylined**, regardless of the setting — writing a review is a different act from saving
- **Anonymous aggregates always count every save, private included**, but never name anyone — this is what keeps "Used by 8 neighbors" working without leaking who

**Does not affect the tier axis.** `ProCard`'s `tier = 1stCircle | 2ndCircle | Neighborhood` describes the *viewer's relationship to the saver*, not a visibility level. Dropping Neighborhood as a visibility value has no effect on Neighborhood as a tier.

**Figma change:** `showVisibility` property and all six `VisibilityControl` frames removed from `ProCard` (`1227:90`). Properties now `savedByViewer`, `tier`, `attribution`.

**Notable:** the nine instances that had `showVisibility=true` were all on the add-flow sheets — `3-alt · Search (sheet)`, `3b-alt · Add manually (sheet)`, `4-alt · Composer (sheet)` — **not** the Saved tab. The Jul 18 decision that introduced the control specified "Saved tab renders `showVisibility=on`", but that was never actually wired. The control was not load-bearing on the surface it was designed for, which supports removing it.

**Also removed:** the visibility selector from Step 1 of the Add-a-Pro flow (AddProComposer). The "Change" action on the save toast now opens the account-level setting rather than a per-pro picker.

### ⚠️ Flip-flop trail — this is the THIRD reversal

| When | Model | Who / why |
|---|---|---|
| May 7 2026 | **per-pro** | Chosen: single source of truth per save record |
| May 25 AM | list-level proposed | Chad: "contractor domain doesn't have the cross-domain privacy heterogeneity that justifies per-pro" |
| May 25 PM | **per-pro kept** | Milind weighed it and kept per-pro for the sensitive-save edge (divorce lawyer, marriage counselor — "rare in contractor domain but real"). Logged explicitly *"so future-us doesn't relitigate."* |
| Sept 1 2026 | **list-level** | This entry |

**The May 25 note was not honoured** — the Sept 1 recommendation repeated the May 25 argument without checking the log first. That is a process failure worth naming.

**What actually justifies the reversal (and it is not the simplicity argument already rejected):** per-pro was decided in May and failed to materialise across four months of building. Three workstreams drifted list-level independently, with no decision behind any of them:

1. `showVisibility` was built but **never wired to the Saved tab** — the single surface the Jul 18 decision specified. All nine live instances were on add-flow sheets.
2. The Aug schema work put visibility on `users`, not the save row.
3. The copy went list-level: "No one can see your saved contractors."

A decision that three independent workstreams quietly route around over four months is not being implemented, whatever the log says.

**The cost, stated plainly:** the sensitive-save edge case is not solved, only deferred. If it matters, per-pro override is a v1.1 addition — the list-level setting becomes the default and the per-save value overrides it. That path stays open and costs nothing now.

**Rule for next time:** grep the decision log before recommending a reversal. A locked decision with a "do not relitigate" note needs *new evidence* to reopen, not a better-argued version of the case that already lost.

**Status: Active.** Unblocks the Phase 0 D1 schema migration.
