# Makerspace / media-center management software — research synthesis and recommendation

Prepared 2026-10-12. This file answers the eight research questions in the needs brief (section 7) and
ends with a recommendation for how an open-source version should start. It draws on four reports in
this folder:

- `01-REA-proximity.md` — teardown of Proximity as deployed for Longmont Public Media (REA tools, evidence bundle attached)
- `02-commercial-saas-landscape.md` — 25 commercial tools, prices as published today, real makerspace stacks
- `03-open-source-landscape.md` — 25 open-source repos with health, licence, last commit, feature coverage
- `04-doors-payments-migration-hosting.md` — door/keyfob APIs, payment fees, data export, hosting

## TL;DR

1. **The need is real and nobody fills it.** No commercial or open-source product covers the shared core in section 4 of the brief. The coworking platforms (Proximity, Nexudus, Optix, Cobot) do doors and bookings but have no certifications, no metered billing and no classes. The makerspace tools (Fabman, Zipper, MakerOps, QReserve) do certifications and machine lockout but are closed, priced $64 to $960 a month, and only Fabman ships door access. The PEG tools (Facil, Media Center Manager) are a 2022 desktop product and an unpriced niche product.
2. **Open source today is a graveyard of one-space systems.** Twenty-plus repos, nearly all written by one hackerspace for itself, several with no licence file, most with one or two maintainers. The two worth learning from are **Fab-manager** (bookings, trainings, classes, invoicing; no doors; French, Rails, AGPL) and **MemberMatters** (dues plus door and tool access on ESP32; no bookings or classes; MIT; maintainer listed as "No one").
3. **Proximity is not a makerspace system and the vendor is leaving the small-operator market.** Its homepage now sells to commercial landlords. LPM uses it as a studio-booking calendar with Stripe dues. The one thing worth copying is its data model for per-resource, per-tier booking rules, which is already extracted to JSON in this folder.
4. **Recommendation: build new, narrow, Stripe-first, with an open door-controller protocol**, and do not fork any of the existing repos. Details in section 9.

## Answers to the brief's research questions

### a. What tools do makerspaces and PEG centers use, what do they cost, what do they do?

Full table in report 02. The short version, with what each does well and the monthly price for a space the size of TinkerMill (roughly 300 to 500 contacts) or LPM (under 100 active members):

| Tool | Used for | Price today | What it lacks for us |
|---|---|---|---|
| Wild Apricot | members, dues, events (TinkerMill, MakeICT, Milwaukee) | $154/mo at 500 contacts | no waivers, no certs, no reservations, no doors |
| Proximity | members, dues, studio bookings, doors (LPM) | $209–259/mo + $99 door add-on | no certs, no metered billing, no equipment rental, no classes checkout, no export docs |
| Booqable | equipment rental (LPM, Vidwest) | $35–87/mo, 20% off nonprofits | rental only |
| WaiverForever (waiver.fr) | waivers (TinkerMill, PS:One) | $19–39/mo + per signature | waivers only |
| Donorbox | donations (LPM, MNN) | $0 + 2.95–3.95% | donations only |
| Fabman | the closest all-in-one for makerspaces | $64–259/mo + $11–15 per wired machine + 0.5–2% | closed; no classes checkout; hardware price €249 per machine |
| Zipper / MakerOps / QReserve | makerspace-specific, certs gate tools | $118–424/mo; $3,600–24,000/yr; not published | closed; Zipper and QReserve have no native doors |
| Nexudus / Optix / Cobot | coworking, strong door integrations | $150/mo; $229–579/mo; custom (25% nonprofit) | no certs, no metered billing |
| Neon CRM + Skedda + Avigilon (Asmbly pattern) | the "three-tool" makerspace stack | $99+ and $249+ per month plus door licences | three logins, three member records |
| Facil | PEG stations (245 orgs) | $995–2,695 one-time | desktop, unchanged since 2022, no doors, no API |

Today TinkerMill pays for roughly six tools and LPM at least four. LPM's Proximity bill alone is probably $3,700 to $4,300 a year before card fees (inferred in report 01).

### b. Are there open-source tools already? State and adoption?

Yes, about 25, and none is a product. Report 03 has the table. Health in one line each:

- **Fab-manager** (sleede, AGPL, Rails + React): active, 91 commits in 12 months, vendor claims 100+ fab labs, cloud from €45/mo. Best booking, training, class and invoicing model in open source. **No door or tool access.** French VAT compliance baked in.
- **MemberMatters** (Brisbane, MIT, Django + Vue): dues on Stripe, access switched off when overdue, ESP32 door and tool firmware, kiosk, OIDC. Six deployments. Last release March 2025, four human commits in a year. **No reservations, no classes.**
- **HMS2** (Nottingham, MIT, Laravel), **Spaceport** (Protospace, AGPL, Django), **makeradmin** (Stockholm, no licence), **Mulysa** (Tampere, GPL): active, one space each, authors say "tightly coupled" to their own space.
- **Seltzer CRM, Nomos, Arooo, BBMS, Conway, MakerManager**: maintenance or dead.
- **Access hardware only:** FabAccess (stalled rewrite), google/makerspace-auth (Pi HAT), prismo (ESP32-C3 NFC controller, active, ~$10 BOM), MakerPass.
- **PEG:** the Drupal Open Media Project / MERCI stack is dead (Drupal 7). PhillyCAM still runs it. Frikanalen (Norway) is the only live open-source public-access TV stack.
- **Generic:** CiviCRM (used by La Patente makerspace), LibreBooking (resource booking, fork of Booked), Snipe-IT (asset checkout), pretix (ticketing). All active, none makerspace-aware.

Why prior "one system for all makerspaces" efforts stalled (report 03 §7): built for the home space and hard-coded to it; one volunteer maintainer who leaves; rewrites that never land; no money.

### c. Which section-4 needs are must-haves for a first release?

Based on what every space in the survey actually pays for and what no tool combines:

**Must-have (v1):**
1. Member records, tiers, recurring dues, group/bundle memberships (every space pays for this)
2. Digital waiver and agreement signing stored on the member record (every space pays a second vendor for this)
3. Certifications (badges) with expiry, and the rule that a badge gates a tool, a space, or a booking (the single feature no coworking platform has)
4. Reservations with a rule table per resource per tier, including included hours per month and booking window (copy Proximity's model)
5. Door and tool access that reads the same member/badge/paid-up state (Fabman and MemberMatters prove the demand)
6. One ledger: dues, bookings, metered use, consumables, class fees, donations, cash recorded by staff
7. Member self-service portal that works on a phone (a responsive web app, not native apps, for v1)
8. Staff reports: active members, revenue by shop/studio, utilization, overdue
9. Email notices with a Slack or Discord link

**Should-have (v1.x):** orientation scheduling and attendance (can be a class type with a "grants badge" flag, so it rides on items 3 and 10); classes and events with payment and member pricing; metered pricing per minute/gram and consumables (a charge type on the ledger plus a Stripe meter).

**Defer (modules):** equipment rental with check-out/check-in (keep Booqable or Snipe-IT and import charges); governance and voting; PEG content submission to CivicCast (a webhook and a file drop, build it on the CivicCast side first); volunteer and shop-captain scoped admin (role system from day one, UI later).

### d. Door and keyfob systems: what is common, do they have APIs?

Report 04 §Q1. Makerspaces almost never use the enterprise cloud vendors; they build on ESP32 or Raspberry Pi, or buy Fabman. For a system we control:

- **Open, documented, signed webhooks:** Kisi, Verkada, Genea. Kisi is the one coworking platforms integrate with most and is a sensible first commercial adapter.
- **No licence fees, local API:** UniFi Access (reader $139, hub $199); webhook support unconfirmed; API may disappear under Identity Enterprise.
- **Effectively closed to DIY:** Paxton (integrators only since July 2024), HID (partner program), Salto (SignalR streaming, no webhooks).
- **DIY firmware worth adopting rather than writing:** MemberMatters' ESP32 firmware (Wiegand, UART, strikes, interlocks, vending), prismo (ESP32-C3 + PN532, hardware-in-the-loop CI), ESPHome Wiegand. TinkerMill already runs its own "TinkerAccess" on Raspberry Pi.
- **Tool interlocks:** Fabman Bridge (€249, closed), RFID Interlock (no price, no API), DIY relays. Safety rule from the sources: e-stop and machine safety interlocks stay wired independently of the access relay.

### e. Which payment processors? Is a kiosk needed?

Report 04 §Q2. **Stripe**, for four reasons: Proximity, Donorbox, MemberMatters and Fabman are all on it, so card tokens migrate Stripe-to-Stripe without re-entry; Stripe Billing has usage meters for metered pricing; Stripe Terminal readers ($59 to $299) cover a semi-attended kiosk; Stripe Connect lets a hosted version onboard each nonprofit onto its own account. Fees 2.9% + 30¢ cards, 0.8% ACH, plus 0.7% if we use Stripe Billing. Nonprofit discounts are mostly irrelevant: Stripe's needs 80% donations, Square has none, PayPal's charity rate is for donations.

Kiosk: Dallas Makerspace runs Square kiosks for consumables; TinkerMill runs an iPad kiosk plus cash boxes. A kiosk is common for honor-system fees, **but metered billing through the tool interlock removes most of the reason for one.** v1 should support a semi-attended Stripe reader and a "record cash" staff action; a dedicated kiosk mode is v1.x.

### f. How do organizations move member and payment data out?

Report 04 §Q3. Wild Apricot: three unjoined CSVs plus a real API with webhooks; cards live at the processor. Proximity: no export documented, no public API; Stripe-to-Stripe card migration applies because the space owns its Stripe Connect account. Booqable: CSV. Nexudus: XLSX, full admin only. Donorbox: CSV, Stripe cards movable, PayPal recurring not. Airtable: CSV per view, rate-limited API. Rule for us: ship importers for Wild Apricot (API) and generic CSV first, document the Stripe PAN-migration request, and publish our own full export from day one so we never become the lock-in.

### g. Hosting: self-host or hosted?

Report 04 §Q4. Both, in this order: a one-command Docker self-host (MemberMatters' only supported path, and what every hackerspace with a volunteer sysadmin uses), plus an official managed option at $15 to $130 a month, which is what the CiviCRM, Discourse and Nextcloud markets converge on. Every failure story in the sources was volunteer upgrade capacity, not money, so upgrades and backups must be boring and automatic.

### h. Interviews

Not done; this was desk research. Candidate list drawn from the stacks observed (report 02 §3): TinkerMill and LPM (reference orgs), Asmbly (Neon + Skedda + Alta), MakeICT (Wild Apricot + Smartwaiver, built and abandoned MESSforMakers), Milwaukee Makerspace (Wild Apricot with door sync scripts), Decatur Makers (Neon + DIY ESP32 access), Artisan's Asylum (Nexudus), Dallas Makerspace (home-grown MakerManager, Square kiosks), Vidwest Studios and PhillyCAM (PEG: Booqable + MembershipWorks; Drupal Open Media), Brisbane Makerspace (MemberMatters author). Ten names, enough for the 5 to 10 the brief asks for.

## 9. Recommendation: how to build the open-source version

**Do not fork.** Fab-manager is the strongest codebase but it is Rails, French-regulation-shaped, and has no access control; MemberMatters is the strongest access stack but has no bookings and one part-time maintainer. Every other repo is one space's internal tool. Forking either means inheriting a foreign domain model and then rewriting half of it.

**Build new, with three borrowed pieces:**
1. **Proximity's booking-rule model** (per resource × tier: price, included hours, max days out, min/max length, allowed hours, approval, increment) and its membership-class field list. Both are in `proximity-lpm-space-config.json` and `proximity-lpm-resources.json`. Add a `required_badges` column and you have the makerspace version.
2. **An open controller protocol, not our own hardware.** Define a small HTTP+MQTT contract ("is card X allowed on device Y right now?", "device Y reports session start/stop with seconds and units") and ship adapters for MemberMatters/prismo-style ESP32 firmware, ESPHome, Kisi, and UniFi Access. TinkerMill's existing Raspberry Pi system becomes one more adapter.
3. **Stripe as the only v1 processor**, Connect-ready so a hosted version can onboard spaces onto their own accounts, with Billing meters for metered use and Terminal for the semi-attended reader.

**Shape of v1** (one product, one database, one login): members and tiers and bundles; dues on Stripe; waivers signed in-app and attached to the member; badges with expiry; resources with the rule table; door/tool access from the same state; one ledger with staff-recorded cash; responsive member portal; staff reports; email with Slack/Discord link; full CSV/JSON export and a Wild Apricot importer. Roles from day one (owner, staff, shop captain, member), scoped-admin UI later.

**Separate from CivicCast, connected by webhook:** the content-submission module is a PEG-only feature and belongs on the CivicCast side as "accept a file plus metadata from any membership system," with this product as the first client.

**What this costs the two reference orgs if it works:** LPM drops Proximity, Booqable, and the Airtable form (roughly $3,700 to $5,000 a year plus card fees); TinkerMill drops Wild Apricot, WaiverForever and the honor-system leakage its own shop-fees page describes. Both keep Slack, Donorbox or Givebutter, and their door hardware.

**Open decisions for Scott** (logged, with my pick in bold):
- Stack: **Python/Django** (MemberMatters, Spaceport, Mulysa, Leiden CRM, byro are all Django, so the contributor pool in this niche already knows it) vs Rails (Fab-manager) vs Laravel (Proximity, HMS2).
- Licence: **AGPL-3.0** (Fab-manager, Spaceport, byro, CiviCRM) so a hosted fork must publish changes; MIT if you want commercial hosts to adopt it freely.
- Name: not researched. The CivicCast collision memory suggests checking before committing.

## Cost of this research

| Item | Tokens | Estimate |
|---|---|---|
| SaaS landscape agent (Opus) | 222,610 | 100k–150k |
| Open-source landscape agent (Opus) | 195,441 | 100k–150k |
| Doors/payments/migration/hosting agent (Opus) | 170,829 | 100k–150k |
| Coordinator (Fable), REA and writing | not metered separately | — |
| **Agents total** | **588,880** | **300k–450k** |

All three agents ran about 40% over estimate, mostly on page fetches that returned 403 or truncated and had to be retried through the browser.
