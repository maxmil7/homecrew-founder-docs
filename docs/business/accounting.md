# Home Circle — Accounting

*Last updated: May 12, 2026*

## Monthly Budget

**Total Monthly Budget:** $2,000

This budget covers personal/business tooling subscriptions + Home Circle infrastructure + service fees.

---

## Current Monthly Subscriptions (Dev Tools)

| # | Service | Cost | Billing Date | Link |
|---|---------|------|-------------|------|
| 1 | Claude Max Plan | $100 | 15th of every month | [claude.ai/billing](https://claude.ai/settings/billing) |
| 2 | Sleek.design Team Plan | $70 | 5th of every month | [sleek.design/subscription](https://sleek.design/dashboard/subscription) |
| 3 | Figma Developer Plan | $20 | 4th of every month | [figma.com/billing](https://www.figma.com/files/team/1108579346075906965/team-admin-console/billing?fuid=1108579338011044681) |
| 4 | Midjourney | $10 | 26th of every month | [midjourney.com/account](https://www.midjourney.com/account) |
| 5 | Gemini Pro | $8 | 15th of every month | [myaccount.google.com](https://myaccount.google.com/subscriptions) |

**Dev Tools Subtotal:** $208/mo

---

## V1 Project Costs — One-Time (Pre-Launch, May 11 – Jun 30)

| # | Item | Cost | Recurrence | Notes |
|---|------|------|------------|-------|
| 1 | Apple Developer Program (Individual) | $99 | Annual | Already active under personal account. Used for v1 launch + TestFlight. Plan is to transfer app to Org account ~Sept 1 (after 60-day post-release block). Org account requires LLC + D-U-N-S; will be a second $99/yr account post-transfer. See task #85. |
| 2 | Google Play Developer | $25 | One-time, lifetime | Required for Play Store submission. |
| 3 | Domain (`homecircle.app` or similar) | $15–25 | Annual | Purchase via Cloudflare Registrar or Namecheap. `.app` TLD requires HTTPS — fine, we have it. |
| 4 | Privacy Policy legal review | $300–500 | One-time | Lawyer review of Termly-generated first draft. Per `v1-day-by-day-plan.md` Day 20. |
| 5 | Cloudflare (DNS) | $0 | Free | Optional Pro upgrade at $20/mo skipped — Supabase already provides Cloudflare CDN. |
| 6 | LLC formation (CA) | $90–$400 | One-time | $70 Articles of Organization + $20 Statement of Information; +$100–300 if using formation service (Northwest, ZenBusiness). $800 CA franchise tax waived first year. **Practical deadline May 30 (Day 19)** — required for Privacy Policy entity name (Jun 5), Apple Developer Organization D-U-N-S (Jun 6 to allow ~2 wk processing before Day 30 assets prep). See task #85. |

**Pre-Launch One-Time Total:** $530 – $1,050 (range widened to include LLC + optional formation service)

---

## V1 Infrastructure + Services — Recurring Monthly

Cost scales with active users (AU). Three projected stages below.

### Stage 1 — Pre-Launch Build (May 11 – Jun 30)
Development period. Only Milind + ~5 beta testers hitting the API.

| Service | Cost | Notes |
|---------|------|-------|
| Supabase Pro | $25 | DB + Storage + Edge Functions + Realtime. Provision Day 4. |
| Railway (Bun API) | $5 | Bun + Hono main API. Free $5/mo credit covers it during dev. |
| Twilio Verify | $5–15 | OTP testing — your own number + beta testers. ~$0.05 per verification. |
| AWS Rekognition | $0 | Free tier 5,000 images/month for first 12 months. Won't approach during build. |
| PostHog | $0 | Free tier 1M events/mo — dev volume nowhere near. |
| Sentry | $0 | Free tier 5K errors/mo + 10K perf events. |
| Google Places | $0 | $200/mo free credit covers all dev usage. |
| **Subtotal** | **$35–45/mo** | |

**Pre-Launch (May 12 – Jun 30, ~1.5 months) Infra/Services Total:** ~$50–70

### Stage 2 — Early Launch (Jul – Sep, Months 1–3)
Public launch in San Jose. Target: 100–500 active users.

| Service | Cost | Notes |
|---------|------|-------|
| Supabase Pro | $25 | Within all included limits. |
| Railway (Bun API) | $5–15 | Light traffic (5–30 req/s peak). |
| Twilio Verify | $10–25 | ~200–500 new signups/month × 1.3 attempt ratio × $0.05 + monthly re-verifies. |
| AWS Rekognition | $0–5 | ~5,000 vouch photos/month — still within free tier first year. |
| PostHog | $0 | Free tier 1M events/mo covers ~10K MAU at typical event density. |
| Sentry | $0 | Free tier sufficient. |
| Google Places | $0 | Under $200 free credit. |
| **Subtotal** | **$40–70/mo** | |

### Stage 3 — Growth (Oct – Dec, Months 4–6)
Target: 500–1500 active users, post-launch traction phase.

| Service | Cost | Notes |
|---------|------|-------|
| Supabase Pro | $25 | Still within limits unless heavy photo traffic. |
| Railway (Bun API) | $15–30 | Moderate traffic. |
| Twilio Verify | $30–80 | More signups + re-verifies at 30-day expiry. |
| AWS Rekognition | $5–15 | Possibly cross free tier mid-period. ~$1 per 1000 images. |
| PostHog | $0 | Still under free 1M events. |
| Sentry | $0–26 | May upgrade to Team if error volume warrants. |
| Google Places | $0–30 | May approach $200 credit threshold. |
| **Subtotal** | **$75–185/mo** | |

### Stage 4 — Scale (Year 2 hypothetical, ~5,000–10,000 AU)
For planning only. Not v1 territory.

| Service | Cost |
|---------|------|
| Supabase Pro + overages | $50–100 |
| Railway (scaled) | $50–150 |
| Twilio | $200–500 |
| AWS Rekognition | $50–150 |
| PostHog (paid tier) | $50–150 |
| Sentry Team | $26–80 |
| Google Places | $50–150 |
| **Subtotal** | **$475–1,280/mo** |

---

## Year 1 Cost Projection (May 2026 – May 2027)

Conservative ramp scenario assuming successful launch.

| Period | Months | Dev Tools | App Infra/Services | One-time | Total |
|--------|--------|-----------|--------------------|----------|-------|
| Pre-launch (May–Jun) | 2 | $416 | $80 | $500 | **$996** |
| Early launch (Jul–Sep) | 3 | $624 | $165 | — | **$789** |
| Growth (Oct–Dec) | 3 | $624 | $375 | — | **$999** |
| Steady-state (Jan–May) | 5 | $1,040 | $1,000 (~$200/mo avg) | — | **$2,040** |
| **Year 1 Total** | **13** | **$2,704** | **$1,620** | **$500** | **~$4,824** |

**Average burn rate: ~$370/mo.** Well within $2,000/mo budget. Headroom: ~$1,630/mo for unexpected costs, contractors, or expanded tooling.

---

## Budget Headroom — What's NOT Currently Allocated

The $2,000/mo budget at average $370/mo burn means we have substantial room for:
- TestFlight beta tester incentives (e.g., $25 Amazon gift cards × 30 testers = $750 one-time)
- Lawyer review of pricing model / pro contract Q3 (~$500)
- Contractor / freelance help (e.g., designer for App Store screenshots) (~$500–1,500)
- Pre-launch $1K Gift Card seeding campaign (per strategic-plan.md) — explicitly budgeted, runs Q3
- Insurance / business formation (~$500–800 one-time)
- Server cost spikes if we go viral early (Railway auto-scales)

---

## Key Cost Assumptions & Sensitivities

**Twilio is the dominant scaling cost.** Every signup is one verification ($0.05) and every 30 days a re-verify is forced per the OTP design (which we may want to lengthen to 90 days post-launch — Decision 39 in design log was 30d). Switching to 90-day OTP expiry roughly cuts the Twilio re-verify cost by 66%. Worth revisiting Q3.

**AWS Rekognition free tier expires after 12 months.** Year 2 budget needs to account for ~$50–150/mo moderation cost.

**Supabase egress is the second scaling risk.** 200GB bandwidth is generous, but if photos go viral via social shares or external embeds, we could blow through it. Mitigation: enforce auth on all photo URLs (signed) so external embedding requires app context. Already in the design.

**PostHog free tier is generous — but watch event volume.** Easy to bloat events on debug logging. Establish a "billable event" policy: every `analytics.capture()` call must be tied to a product-tracked event in the funnel, no debug events shipped to PostHog cloud. Use Sentry for diagnostic logs instead.

---

## Existing Service Accounts to Provision Day 4

- [ ] Supabase project (sign up + create homecircle-prod project) — $25/mo Pro tier
- [ ] Railway account + project — $5 credit + usage
- [ ] Twilio account + Verify service ID + API keys — pay-as-you-go
- [ ] AWS account + IAM user with `rekognition:DetectModerationLabels` scope — free tier first year
- [ ] PostHog Cloud account (already provisioned per `app.json` key) — free tier
- [ ] Sentry project — free tier
- [ ] Google Cloud project + Places API key (with HTTP referer restriction for prod) — $200 free credit
- [ ] Apple Developer account — $99/year (Week 6)
- [ ] Google Play Developer account — $25 one-time (Week 6)
- [ ] Domain registration (Cloudflare or Namecheap) — Week 6
