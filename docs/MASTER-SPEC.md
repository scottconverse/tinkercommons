# MASTER SPEC — TinkerCommons

Open-source makerspace and media-center management system.

Canonical, living document. Started 2026-10-12. Owner: Scott Converse. Every settled decision lands here
the day it is made; the research reports in this folder are the evidence behind it, not the spec.

Name: **TinkerCommons** (decided by Scott 2026-10-12). GitHub organization: https://github.com/tinkercommons (Scott is admin; code lives here). An empty placeholder repo also exists at github.com/scottconverse/tinkercommons. Domains held: tinkercommons.org (primary), .com, .net, .info. Availability at decision time: GitHub org/user, PyPI, npm, and .org/.com/.net/.dev/.io/.app/.space all free; no product or project of that name found by web search.

---

## 1. Purpose

One free, open-source system that replaces the three to six SaaS tools a makerspace or public-access
media center uses today for members, dues, waivers, certifications, reservations, classes, payments,
door and tool access, and reporting. It runs on its own. It can connect to CivicCast through an API and
webhooks. It is a separate product from CivicCast.

Reference sites: TinkerMill (makerspace, ~15 shops, Longmont CO) and Longmont Public Media (media
makerspace and PEG station). Both are 501(c)(3). The product must serve both without forks.

## 2. Principles (do not violate without changing this file)

1. **One record per member, one ledger per space, one login.** No feature may create a second member list.
2. **Rules are data.** Pricing, included hours, booking windows, badge requirements and door schedules are rows in tables, never code paths. (Copied from Proximity's per-resource, per-tier rule table, see `01-REA-proximity.md` §4.)
3. **Access follows state.** A fob, phone or PIN works only while the member is active, paid up, waivered, and holds the badge the device requires. The device never holds its own list of who is allowed, beyond a short offline cache.
4. **No card numbers, ever.** Stripe holds payment methods. We store Stripe IDs.
5. **Export is a feature from v0.1.** Full CSV and JSON of every table, by the space's own staff, any time. We never become the lock-in we are replacing.
6. **Self-host first.** One Docker command installs it; one command upgrades it; backups are automatic and restorable. A managed hosting option is layered on top, never required.
7. **Hardware is a plug-in.** We publish the controller protocol and ship adapters. We do not sell hardware.
8. **Every version ships with proof.** A version is done when its acceptance tests pass on real data at a reference site, not when the code is merged. Critical areas (money, access control, deletion, migration) go through the proof gate.

## 3. Users and roles

| Role | Can |
|---|---|
| Member | see and manage own account, sign documents, book, pay, view own charges and logs |
| Volunteer / shop captain | everything a member can, plus: grant badges in assigned shops, view bookings and logs for assigned resources, record a cash charge in assigned shops |
| Staff | everything, except billing settings, role grants, and deletion |
| Owner / admin | everything, including Stripe connection, roles, data export, deletion |
| Board member | read-only reports; voting admin when governance module is on (v2.0) |

Roles are scoped: a captain's rights are per shop/resource group. The role model exists in v0.1 even
though the captain UI arrives in v2.0.

## 4. Architecture (settled unless marked open)

- **Backend:** Python. Decided by Scott 2026-10-13 after reviewing his own repos (68 of 118 are Python; house framework is FastAPI, used in CivicCast).
  - **Django** for the application: members, memberships, documents, badges, resources, bookings, ledger, classes, staff and member screens. Reason: the product is mostly forms, tables, permissions, migrations and admin, which Django ships; every open-source makerspace system still alive after five years is Django (MemberMatters, Spaceport, Mulysa, Leiden CRM, byro) or Rails (Fab-manager).
  - **FastAPI** for the one piece that is an API by nature: the access-controller protocol service that ESP32 readers, Kisi, UniFi and other adapters talk to (§7). A small service in the same repo, sharing the Django database models, so the part that most resembles CivicCast runs on the stack Scott already knows.
  - PostgreSQL, Redis for queues.
- **Frontend:** Django templates plus htmx for staff screens, so contributors stay in one language; a light TypeScript layer only where the phone portal needs it; no React build pipeline until something proves it is needed. Responsive member portal (installable web app). No native app in v1.0.
- **Payments:** Stripe only. Stripe Billing for dues, Billing meters for metered use, Terminal for an in-building card reader, Connect so a hosted operator can onboard each space onto the space's own Stripe account.
- **Email:** any SMTP; Postmark/SendGrid adapters. Chat: a link to Slack or Discord plus webhook posts; we do not host chat.
- **Access protocol:** HTTPS plus MQTT. Specification in §7.
- **Install:** Docker Compose, one command. Upgrades migrate the database automatically. Nightly encrypted backup to a configurable target with a tested restore command.
- **API:** every screen is backed by a JSON API with personal access tokens and scoped service tokens. Outbound webhooks for member, membership, badge, booking, charge, access events. Public from v1.0.
- **Licence:** Apache-2.0 for everything, including the access-controller protocol and reference firmware. Decided by Scott 2026-10-13; AGPL was offered and rejected as too restrictive. Consequence accepted: hosted forks owe nothing back; adoption and contribution are open to anyone, including commercial hosts.

## 5. Core data model

Entity list. Fields in `proximity-lpm-space-config.json` and `proximity-lpm-resources.json` seed the
membership-class and booking-rule tables; add the columns marked **new**.

- **Space** — one installation can hold several spaces (multi-location arrives v2.0; the column exists from v0.1).
- **Person** — one row per human. Email, phone, emergency contact, photo, custom fields.
- **Membership class** — name, price, period, recurring, contract length, signup fee, deposit, approval required, max shared members (bundle size), punch-card options, **required documents (new)**, **grants badges (new)**, status, sort order.
- **Membership** — person × class, start, end, status, Stripe subscription ID, shared/master link.
- **Document** — waiver or agreement, versioned; **Signature** — person × document version, timestamp, IP, stored PDF.
- **Badge** — certification; name, shop, expiry rule, who may grant it. **Badge grant** — person × badge, granted by, date, expiry, evidence (class attended, quiz, instructor).
- **Resource** — a studio, shop, machine, or edit bay; type, seats, colour, photo, shop group, **access device link (new)**.
- **Booking rule** — resource × membership class (null = default): price per period, included minutes per month, max days out, min and max length, allowed hours, approval, increment, **required badges (new)**.
- **Booking** — person × resource × time; recurring pattern; approval state; charge link.
- **Charge** — the ledger line: person, type (dues, booking, metered, consumable, class, donation, deposit, fee, credit), amount, tax, source (Stripe, cash, check, kiosk, device), shop/resource, who recorded it, invoice link.
- **Invoice / payment** — Stripe mirrors plus offline payments.
- **Class / event** — title, instructor, capacity, price per class of membership, **grants badge (new)**, sessions; **Enrollment** — person × class, paid, attended.
- **Access device** — door or tool controller: adapter type, resource link, schedule, required badges, offline cache TTL. **Access event** — device × credential × result × time; **Tool session** — device × person × start × end × units.
- **Credential** — fob, card, PIN, phone key; person link; active flag.
- **Audit log** — every write by staff, who, what, before/after.

## 6. Money rules

- Dues: Stripe subscription per membership. Overdue past a grace period flips the member to inactive, which revokes access automatically (MemberMatters behaviour).
- Bookings: the rule table decides price and included time. Included time is tracked per resource per month. Overage becomes a charge at the discounted rate for that class.
- Metered use: tool sessions from devices create charges by minute, hour, gram or unit. Pushed to Stripe meters and billed on the dues invoice, or charged immediately if the space prefers.
- Consumables: staff or captain records a charge; a kiosk mode (v1.x) lets the member self-record.
- Cash, check, Venmo: recorded against the ledger by staff; marked as offline.
- Taxes: per charge type and per resource.
- Refunds, credits, deposits, gift cards: credits and deposits in v1.0; gift cards v2.0.

## 7. Access-controller protocol (open specification, published with v0.5)

Two channels, same vocabulary:

- **Pull:** device asks `GET /access/check?device=Y&credential=X` and receives allow/deny, reason, max session length, and a cache token. Devices may cache the last N allowed credentials for a configurable minutes-offline window.
- **Push:** server publishes credential revocations and device config over MQTT; device publishes `session_start`, `session_end {seconds, units}`, `door_opened`, `tamper`, `heartbeat`.
- **Adapters** translate for hardware that cannot speak this directly: Kisi (webhooks and API), UniFi Access (local API), and a reference ESP32 firmware derived from MemberMatters/prismo-style designs. Brivo and SALTO only when a pilot site needs them.
- **Safety:** the protocol controls power-enable and door-release only. Emergency stops and machine safety interlocks are never routed through it. Documented in the adapter guide.

## 8. Integrations

- Stripe (required). QuickBooks Online export (v1.x). Mailchimp sync (v1.x). Slack/Discord webhook (v0.7).
- CivicCast: v1.0 emits a "media submitted" webhook with a file reference and metadata; v2.0 ships the intake module on both sides.
- Importers: Wild Apricot via its API, generic CSV (v0.7). Proximity has no export; a CSV template and a Stripe card-migration guide instead.

## 9. Version ladder

Each version lists scope and the acceptance evidence that closes it. Versions are sequential; a version
is not started until the previous one's evidence is recorded in this file's change log.

### v0.1 — Foundation
Members, membership classes, Stripe dues, roles, audit log, full export, Docker install.
*Done when:* installed from scratch on a clean machine in one command; 50 test members on recurring dues cycle through a month on Stripe test clock; export produces every table; audit log shows every staff write.

### v0.2 — Documents and badges
Versioned documents, in-app signing with stored PDF, badges with expiry and grant evidence, "required documents" and "grants badges" on membership classes.
*Done when:* a new member cannot activate without signing; an expired badge is visibly expired; signed PDFs survive export and reimport.

### v0.3 — Resources and bookings
Resources, booking-rule table, member calendar on a phone, recurring bookings, approval flow, included hours per month.
*Done when:* LPM's seven studios and seventeen classes load from the JSON in this folder and the calendar enforces every rule in them; a Creator member gets exactly 10 free hours per studio then the discounted rate.

### v0.4 — Ledger and payments
Charge types, invoices, offline payments, taxes, credits and deposits, Stripe meters, Stripe Terminal reader.
*Done when:* a month of mixed dues, bookings, consumables and cash reconciles to the cent against Stripe; a staff member records a cash laser fee in under 10 seconds on a phone.

### v0.5 — Access
Protocol published; reference ESP32 firmware; Kisi and UniFi adapters; devices, credentials, access events, tool sessions billed by the minute; overdue revokes access.
*Done when:* a real door and a real tool at a reference site run for 30 days off this system with zero unexplained denials, and a lapsed member is locked out within one minute. Proof gate.

### v0.6 — Classes and events
Classes with sessions, capacity, member pricing, payment, attendance, "grants badge". Orientation is a class that grants the entry badge.
*Done when:* a TinkerMill-style orientation → badge → keyfob flow runs end to end with no staff database edits.

### v0.7 — Reports, notices, importers
Staff reports (active, overdue, revenue by shop, utilization), email notices, Slack/Discord hook, Wild Apricot and CSV importers, public JSON API and webhooks.
*Done when:* TinkerMill's Wild Apricot export imports with every member, tier and payment history intact and a diff report of anything dropped.

### v0.8 — Hardening
Privacy controls (data minimisation, member self-delete, retention), backup and tested restore, one-command upgrade across three versions, rate limits, 2FA for staff, security review.
*Done when:* restore from last night's backup on a fresh machine takes under 15 minutes; a cold security audit finds no Critical or Major.

### v0.9 — Pilot
Live at LPM and TinkerMill in parallel with their current tools for one full billing cycle. Newcomer walkthrough on every screen. Defects fixed, no new features.
*Done when:* both sites run a month with the old tools read-only; every section-4 need in the brief is demonstrated on real data; Scott signs the pilot report.

### v1.0 — Release
Everything above, documentation, installer, migration guide, licence, public repo.
*What it does not include:* equipment rental, governance, CivicCast intake module, captain UI, native apps, hosted service, multi-location, gift cards. See v2.0.

### v1.x — Operations
Hosted service (managed option) · kiosk mode for self-recorded consumables · QuickBooks export · Mailchimp sync · Brivo and SALTO adapters as needed · performance and accessibility passes.

### v2.0 — Full brief
Completes every item in the needs brief, sections 4 and 5:
- **Equipment rental** with check-out, check-in, due dates, condition notes, late fees, kits, barcode scan. Replaces Booqable.
- **Full classes and events**: waitlists, instructor assignment and payout tracking, public class pages, series, materials fees.
- **Governance**: member voting rights by tier, ballots, board elections, quorum, minutes attached to the record.
- **Shop-captain admin UI**: scoped screens for badge grants, shop ledger, shop calendar, shop announcements.
- **CivicCast content intake**: submission form in the portal, file upload, metadata, approval queue, delivery to CivicCast by API on both sides; CivicCast returns air dates to the member.
- **Multi-location** for a space with more than one building.
- **Gift cards, punch cards, sponsorships** as first-class ledger objects.
- **Member directory and companies** with privacy levels.
*Done when:* LPM runs with Booqable and Airtable cancelled; TinkerMill runs its annual election in it; a second PEG station submits a program through to CivicCast air.

### v3.0 — Beyond the brief (optional, decide after v2.0)
Donations and donor records (replace Donorbox), inventory and consumable stock levels tied to charges, volunteer hours and scheduling, reciprocity between spaces (one credential, many spaces), analytics across spaces, native apps if the installable web app proves insufficient, plug-in marketplace for adapters and modules.

## 10. Non-goals (all versions)

Hosting chat or forums. Selling hardware. Website builder or CMS. Accounting ledger beyond what reconciles to Stripe and exports to QuickBooks. Replacing CivicCast's broadcast functions.

## 11. Proof and rigour

Lane by version: v0.4, v0.5, v0.8, v0.9, v1.0 and the v2.0 rental and governance units are **Critical**
(money, access control, persisted data, migration). Everything else is Standard. Audit after every
slice; walkthrough before any "proven" claim; a logic test never seen failing is not evidence.

## 12. Open decisions (owner)

| # | Decision | Fable's recommendation | Status |
|---|---|---|---|
| D1 | Backend stack | Django app + FastAPI access service, PostgreSQL, Redis | **decided 2026-10-13** |
| D2 | Licence | Apache-2.0 | **decided 2026-10-13** (AGPL rejected as too restrictive) |
| D3 | Name | TinkerCommons | **decided 2026-10-12** |
| D4 | First pilot site order | LPM first (fewer shops, one vendor to replace), TinkerMill second | open |
| D5 | Who builds | coordinator plus cheaper coder workers, same pattern as CivicCast beta.10 | open |

## 13. Change log

- 2026-10-13 — Stack decided by Scott: Python; Django for the application, FastAPI for the access-controller service, shared models, same repo; htmx front end, TypeScript only where the portal needs it.
- 2026-10-13 — Licence decided: **Apache-2.0**. Fable recommended AGPL-3.0 (hosted-fork clause); Scott: "too restrictive."
- 2026-10-13 — Scott registered tinkercommons.org / .com / .net / .info (Hostinger parking DNS at registration) created an empty public repo github.com/scottconverse/tinkercommons, then also secured the GitHub organization **github.com/tinkercommons** (admin, no repos yet). Code goes in the org.
- 2026-10-12 — Name decided: **TinkerCommons**. Candidates checked and rejected: Shopwright (empty GitHub org squatting it, .org/.com live), Sawhorse (.org parked for sale), Spokeshave, Membench, Fobbit, Offcut, MakerWright (live competitor at makerwright.com), Commonswright (.org taken July 2026, empty page).
- 2026-10-12 — file created from the research in this folder. Scott: "I'll call this v1.0 when it's done, we start at v0.1." Version ladder v0.1 → v3.0 drafted; v2.0 defined as "full brief complete."
