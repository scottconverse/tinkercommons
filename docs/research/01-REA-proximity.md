# REA: Proximity (proximity.space) — what it is, how it is built, what it does

Prepared 2026-10-12. Target: the Proximity member portal as deployed for Longmont Public Media
(members.longmontpublicmedia.org, Proximity build v2026.26.4). Method: REA static bundle analysis over
Chrome DevTools Protocol (no JavaScript executed, no login, no writes), plus read-only GETs of the
public endpoints the bundle names, plus Proximity's public marketing and docs sites.

Evidence files in this folder:
- `rea-proximity-evidence.json` — REA evidence bundle (2 records, evidence IDs ev_cf4fc9… and ev_6dffe4…)
- `proximity-lpm-space-config.json` — the `window.space` / `window.partner` / `window.platform` objects embedded in the portal HTML
- `proximity-lpm-resources.json` — the public `/api/2.0/conference-rooms` response (LPM's seven bookable resources with pricing rules)

Every statement below is tagged **[observed]** (seen in the bundle, HTML, or an HTTP response),
**[inferred]** (a reasonable reading of what was observed), or **[unknown]**.

## 1. TL;DR

- Proximity is a **coworking-space platform that has pivoted to commercial real estate**. Its homepage no longer mentions makerspaces or media centers; the coworking product is one of three "solutions" (CRE, Flex Space, Coworking). [observed]
- LPM runs on it in `mode: "coworking"`. Studios and edit bays are modeled as **"conference rooms"**. There is no equipment-checkout, no certification/badge gate, no metered consumables, no class/ticketing object in the member bundle. [observed]
- Pricing: **$209/month** (up to 40 active members) to $679+/month, per location, plus **$99/month** for door access, plus Stripe fees 2.9% + 30¢. Support above "basic" is $69 or $199/month extra. [observed, proximity.space/coworking and /support]
- Stack: **Laravel (PHP) backend with Laravel Passport OAuth, Vue 2 single-page front end built with webpack**, Stripe for payments, Postmark for email, Bugsnag for error reporting, imgix for images, Mapbox for maps, Cisco Meraki for Wi-Fi gating. [observed/inferred, see §3]
- The member bundle calls a versioned JSON API (`/api/2.0`, `/api/2.2`, plus `/api/compliance`, `/api/shop`, `/oauth`). There is **no published public API or webhook documentation**; the integration story is Zapier, QuickBooks, HubSpot, Mailchimp, ActiveCampaign, Google Calendar, Brivo, SALTO KS, Kisi. [observed]
- The data model is good and worth borrowing: per-resource, per-membership-tier **booking parameters** (price per hour, monthly free time, max days out, min/max booking length, allowed hours, approval, increment). This is exactly the "included hours per tier" rule in the needs brief. [observed, §4]

## 2. What Proximity sells today

Marketing site (proximity.space, read 2026-10-12):
- Positioning: "The Operating Platform for Commercial Real Estate & Coworking." Three solution pages: Commercial Real Estate, Flex Space, Coworking.
- Coworking feature list: membership management, digital door access (iOS/Android keys, door schedules), Wi-Fi access control, "greeting management" (guest/package notices), reservation management (conference rooms, phone booths), billing via Stripe, occupancy and air-quality sensors, visitor check-in, branded mobile app (white-label App Store listing as a paid add-on).
- Pricing table (coworking, per location, USD):

| Active members | Monthly |
|---|---|
| up to 40 | $209 |
| 41–85 | $259 |
| 86–150 | $329 |
| 151–250 | $449 |
| 251–350 | $559 |
| Enterprise | $679+ |

  Add-ons: Digital Door Access $99/month per location (Proximity Open hardware or Brivo/SALTO/Kisi integration; hardware extra). Digital floor plans, expanded visitor management, CO2/occupancy sensors, enhanced onboarding — priced on request. Card processing 2.9% + 30¢, ACH 1%. Available in US, Canada, Australia, New Zealand only.
- Support: Basic free (4-hour response, docs + Intercom chat); Extended $69/month; Premium $199/month (includes 2 hours/month of IT help for doors and Wi-Fi).
- Integrations page: Stripe, QuickBooks Online, Google Calendar / Microsoft 365, HubSpot, Mailchimp, ActiveCampaign, Brivo, SALTO, Kisi, Cisco Meraki, Zapier ("7,000+ apps"). No public API, no webhooks mentioned.
- Docs (docs.proximity.space): sections for coworking operators, workplace platform, Proximity Open door hardware, training videos. No API or data-export article found by title. Member-management docs describe custom profile fields, staff-only document uploads, punch passes, join links, referral links, and in-app support requests.

What LPM actually pays [inferred]: LPM shows "active members this month" in the UI but the count is not public. At LPM's size the plan is almost certainly the $209 or $259 tier, plus $99 door access if they use Proximity keys (the bundle has door/key code and LPM is 24/7 keyed, so likely), so roughly **$300–$360/month before Stripe fees**, or $3,700–$4,300/year. Booqable, Donorbox, Airtable, LaunchPass are on top of that.

## 3. How it is built (from the bundle)

| Layer | Finding | Tag |
|---|---|---|
| Front end | One 3.6 MB `js/app.js` (sha256 574300e0…), webpack runtime, Vue 2 components (`e._v`, `e._l`, `$http`, Vuex). FullCalendar 3.10 for the calendar, Moment.js, Mapbox GL 1.8.1, bootstrap-datepicker. | observed |
| Back end | `window.Laravel = {csrfToken}`; `/oauth/token`, `/oauth/clients`, `/oauth/personal-access-tokens`, `/oauth/scopes` = Laravel Passport. `amenityable_type: "App\\Models\\Space"` in the embedded JSON is Eloquent polymorphic naming. | observed → inferred: Laravel/PHP |
| API | Versioned REST: `/api/2.0/*` (older), `/api/2.2/*` (newer: members, subscriptions, inventory-resources, recurring-reservation, legal-documents, jobs, teams), `/api/compliance/*` (agreements, documents/sign, membership-types), `/api/shop/*` (gift cards), `/api/2fa/*`, `/api/keys/*`, `/api/reservations/*`, `/api/payment-methods/setup`, `xapi/validate-login`, `xapi/validate-subdomain`. 61 distinct endpoint strings. | observed |
| Payments | Stripe only. Two publishable keys: a platform key and a per-space key; `has_connected_stripe_account: true` → Stripe Connect, one connected account per space. `enable_offline_payments: 1` → staff can record cash/check. | observed / inferred |
| Email | `postmark_domain_verified: 1` → Postmark. | observed |
| Error reporting | Bugsnag (console prints "[bugsnag] Loaded!"). | observed |
| Images | imgix CDN (`prod-proximity-imgix-media.imgix.net`). | observed |
| Wi-Fi | `meraki_group_policy` on membership classes; docs compare Meraki vs RADIUS. | observed |
| Doors | `doors`, `keys`, `/doors?include=legalDocuments.currentContext`, `/manage/door-schedules/{doorId}`, `/salto-ks/iq/`, `hardware-devices` POST, `doorDriver`, `doorType`, `emergency_lockdown`, `emergency_pause`. Door access can require signed legal documents first. | observed |
| Multi-tenant | `space_id` on everything; `reseller_id`/`partner` object for white-label resellers; `space_domains` with per-space TLS certificates (three domains for LPM). | observed |
| Analytics | Google Analytics id on the space; `dataLayer`; a dynamic import from `ase.proximity.app/web/v4/<id>` (a Proximity-hosted script, purpose unknown). | observed / unknown |
| Auth | Password strength meter (zxcvbn "crack time"), 2FA challenge/redeem, password confirm for sensitive actions. | observed |
| Extra products | "Wave" virtual front desk (`/wave/check-in/{member,nomad,visitor}`), "Protect" insurance (`protect/request-quote`, `building_protection`), "Nomad" roaming credits across Proximity spaces (`nomad_credits`, `is_nomad`, `provides_roaming`). | observed |

Tenant security note [observed]: `/api/2.0/space` answers without authentication and includes `space_info.wifi_ssid` and `wifi_password` for the guest network. For LPM that is the guest Wi-Fi, so low impact, but it is a design smell worth not repeating.

## 4. The data model (the useful part)

### Membership class (tier) — 46 fields [observed, from LPM's 17 classes]
`name, type (external/managed), price (cents), period, period_type (monthly…), recurring, contract_length, require_contract_at_renewal, signup_fee_amount, deposit_amount, deposit_policy, requires_approval, max_quantity, max_shared_memberships (group/bundle size), is_punch_card, min_punches, max_punches, price_per_punch, punch_exp_period(+type), auto_refill_policy, auto_refill_type ("top off" / add), internet_access, meraki_group_policy, insurance_required, insurance_requirements, insurance_restricts_access, is_nomad, provides_roaming, default, status, sort_order, excerpt, description`.

LPM's live tiers (prices in cents → dollars): Community/Pay-as-you-go $0 default; Creator $40/mo or $400/yr; Non-Profit $80/mo or $800/yr (up to 4–5 shared members); Business $150/mo or $1,500/yr; SVVSD Student $0; plus seven legacy "LPM25" tiers at $30–$1,200. Matches the public membership page exactly.

### Resource ("conference room") [observed, 7 at LPM]
`name, description, seats, buffer, display_color, future_bookings, reservation_cta, booking_parameters[]`.

Each `booking_parameters` row is **one rule per (resource, membership class)**, with a `null` class as the default:
`price (cents/hour), pricing[] (period name/type/length), monthly_free_time, max_days_out, min_booking_time, max_booking_time, min_start_time, max_end_time, require_approval, increment`.

Example, Big TV Studio: default rule 9:00–23:30, 60–480 min, 60 days out; Pay-as-you-go rule $25/hr, 30 days out. This is the "Creator gets 10 free hours per studio per month then a discounted rate, and can book 90 days out" feature, implemented as data, not code.

### Other objects named by the bundle [observed]
- `users`, `members`, `teams` (maintainers/non-maintainers), `companies` (business directory), `memberships` with `sharedMemberships`, `masterMembership`, `brandMasterMembership`, `membershipsPendingStart`, `latestPendingRequest`.
- `subscriptions`, `continuous-reservations` (dedicated desks/offices with `contract_ends_at`), `recurring-reservation` patterns with occurrence cancel.
- `inventory-resources` with `pending-approvals` and `subscriptions` (a newer generic "thing you can subscribe to" model; `inventory_opt_in` flag on the space).
- `invoices`, `taxes` (on membership types, resources, add-ons, custom charges), `discounts` (code validated per membership class), `addons` (one-time and recurring), `credits`, `punches` (individual and team pools), `gift cards`.
- `legal-documents` / `compliance/agreements` with per-context requirement (doors can require them), "Checking for required agreements…" flow before actions.
- `events` (with public RSS at `/events/rss`), `jobs` board, `member directory` with view level, `benefits` restricted per membership, referral links, join links.
- `doors`, `keys` (granted via membership or directly, 24/7 or scheduled), `door-schedules`, hardware devices, SALTO KS IQ, emergency lockdown/pause, access logs filterable "by door, member, method or message".
- Wave check-in (member / nomad / visitor), mailing-list capture.

## 5. Gaps against the needs brief (section 4 of the brief)

| Need | Proximity | Tag |
|---|---|---|
| Member records, tiers, recurring dues, group memberships | Yes (shared memberships, teams, companies) | observed |
| Digital waivers stored with member | Partial: legal documents/agreements exist and can gate door access; no e-signature vendor; staff-uploaded docs are hidden from members | observed |
| Orientation scheduling + attendance | No object. "orientation" in the bundle is only a date-picker CSS property | observed |
| Certifications / badges gating tools | **No.** Nothing in 3.6 MB of front-end code. Door keys per membership are the only access gate | observed |
| Reservations with tier and cert rules | Tier rules yes (strong); cert rules no | observed |
| Classes/events with payment, member price | Events exist (RSS); ticketing/payment for events: 2 "ticket" strings only, no checkout flow found | observed / unknown |
| One ledger | Invoices + Stripe; offline payments; QuickBooks sync | observed |
| Door access | Proximity Open hardware, Brivo, SALTO KS, Kisi; Meraki Wi-Fi gating | observed |
| Member self-service mobile portal | Yes, web + iOS/Android + white-label app | observed |
| Staff reports | "active members this month", utilization reporting, transaction log filters | observed (names only) |
| Email / Slack | Postmark email; `enable_prox_slack` flag (off at LPM); Mailchimp/ActiveCampaign sync | observed |
| Equipment rental out of building | **No.** LPM uses Booqable for this | observed |
| Metered use (per minute/gram) + consumables | No. Hourly resource pricing only; add-ons and custom charges could approximate consumables | observed / inferred |
| Governance (voting) | No | observed |
| Content submission to a PEG system | No | observed |
| Volunteer / shop-captain limited admin | "member-level permissions" and "membership-level permissions" strings exist; manager/owner roles; granularity unknown | observed / unknown |
| Data export | Not documented anywhere found. Passport personal-access-token UI exists in the bundle (`/oauth/personal-access-tokens`), so a logged-in user may be able to mint a token and read their own data via the same JSON API the portal uses. Whether staff can bulk-export members is **unknown** | observed / unknown |

## 6. Verdict for the open-source project

1. Proximity is a reasonable **booking + membership + door** engine for a coworking space and LPM is using maybe 40% of it. It is not a makerspace system and the vendor is moving away from small operators toward landlords.
2. The thing worth copying is the **rule-per-(resource, tier) booking parameter table** and the **membership class** field list. Both are in this folder as JSON and can seed our schema directly.
3. The things Proximity lacks are exactly the makerspace differentiators in the brief: certifications gating tools, metered/consumable billing, orientation tracking, equipment checkout, classes with tickets, governance, PEG content hand-off.
4. Lock-in is real: no documented export, no public API, Stripe Connect account owned by the space (good: the Stripe customer and payment-method records belong to LPM's own Stripe account and can be migrated to a new platform with Stripe's standard account-to-account copy, which does not require re-entering cards). [inferred from `has_connected_stripe_account`; migration mechanics are in the door/payments research report]

## 7. Limits of this REA

- Static analysis only; nothing was executed, no login, no POSTs. Admin/manager screens are served by the same bundle but were not exercised, so admin features are known by name only.
- Source maps were not available (`source_maps: unavailable`), so component names come from strings and endpoint paths, not original file names.
- Mobile apps were not analyzed.
- Unauthenticated endpoint probing was limited to ten read-only GETs; four answered (space, conference-rooms, events RSS, and the HTML pages).
