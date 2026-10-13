# Open-source makerspace, hackerspace and community-media management software — landscape as of 2026-10-12

Prepared by a research agent (Opus) under Fable coordination. Repo numbers are from the GitHub API
(`gh api`) on 2026-10-12. "Last commit" = last commit on the default branch. "12-mo commits" = commits
since 2025-10-12, capped at 100 per query. Raw API output and READMEs are in `raw-oss-agent-notes/`.

Agent cost: 195,441 tokens, 63 tool calls, 10m28s (estimate was 100k–150k).

## TL;DR

- **No open-source system covers the whole checklist.** The two closest are both one-space or one-vendor projects:
  - **Fab-manager** (French, AGPL, active) covers memberships, trainings, machine and space reservations, paid events, invoicing, a wallet and a shop. **No door or keyfob integration**; the "100+ labs" figure is the vendor's own claim.
  - **MemberMatters** (Brisbane, MIT) is the best combination of membership, Stripe dues and door/tool access control, with 6 self-reported deployments. **No reservations and no classes.** Last release v3.8.0, March 2025. The dev branch had only 4 human commits in the last 12 months.
- Almost every other makerspace system is **built by one space for itself**: HMS2 (Nottingham), Spaceport (Calgary), Nomos (Vancouver), makeradmin (Stockholm), Conway (TheLab.ms, archived), Mulysa (Tampere), BBMS (Brighton), Arooo (Double Union). Several have no license file, which legally makes them "source-available", not open source.
- Hardware access control lives in separate projects: FabAccess (stalled), google/makerspace-auth, MakerPass, prismo (nu31).
- **PEG / community media:** the Drupal "Open Media Project / Community Media" stack (MERCI reservations, cm_cablecast, telvue modules) is effectively dead; it was Drupal 7, which lost support in January 2025. The only live open-source public-access-TV stack found is **Frikanalen** (Norway). Cablecast's producer-upload portal is proprietary.
- **Not found or not applicable:** Spacebook, HackerspaceOS (as a membership system), Tulip, Oblong, mkr.space, MakerLabs, and Milwaukee / Hack Manhattan / NYC Resistor / Noisebridge membership tooling. Fabman is proprietary. See section 6.

**Legend:** Y = confirmed (README or source tree) · y* = inferred from source file or model names, not run · P = partial · N = not found · ? = unknown

---

## 1. Summary table: makerspace-specific systems

Checklist columns: 1 Members/tiers/recurring dues · 2 Group memberships · 3 Digital waivers · 4 Orientation/attendance · 5 Certifications gating tools · 6 Space+equipment reservations with rules · 7 Classes/events with payment · 8 Unified ledger · 9 Door/keyfob · 10 Mobile self-service portal · 11 Staff reports · 12 Email/Slack

| Project | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | 10 | 11 | 12 | Payments | Health |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **MemberMatters** | Y | ? | N | P (LMS induction, kiosk sign-in) | Y | N | N | P (Memberbucks) | Y (ESP32 firmware) | Y | P (audit logs) | Y (email/SMS/Discord) | Stripe | Maintenance |
| **Fab-manager** | Y | P (pricing groups / orgs) | y* (supporting docs) | ? | y* | Y | Y | Y (invoices, accounting, wallet) | N | ? | Y | Y (email) | Stripe, PayZen | **Active** |
| **HMS2** (Nottingham) | Y | ? | ? | Y (induction booking) | Y | y* (tool + area booking) | ? | y* (Snackspace) | Y (Gatekeeper RFID) | ? | y* | Y | UK bank transfer reconciliation; Stripe (donations) | **Active** |
| **Spaceport** (Protospace) | Y | ? | N (paper form) | ? | Y (lockouts) | N | Y (courses, sessions) | Y (transactions) | Y (Airlock) | ? | Y (stats) | Y + Discourse/LDAP/wiki | PayPal | **Active** |
| **Seltzer CRM** | Y | N | N | N | N (mentors only) | N | N | P | P (key tracking) | ? | ? | P | PayPal | Maintenance |
| **Nomos** (VHS) | Y | ? | ? | ? | Y (privileges) | ? | ? | ? | Y (cards/PINs/keys) | ? | Y (metrics) | Y (webhooks) | Stripe, PayPal IPN | Maintenance |
| **makeradmin** (Stockholm) | Y | ? | ? | P (quizzes) | ? | N | P (webshop) | P | Via Accessy (commercial) | Y | Y | Y | Stripe | **Active** (no license file) |
| **Conway** (TheLab.ms) | Y | ? | ? | ? | ? | ? | ? | ? | Y (fob swipes) | ? | ? | Y | Stripe, legacy PayPal | **Archived** 2026 |
| **Mulysa** (Tampere) | Y | ? | P (terms) | ? | ? | N | N | N | Y (NFC / phone) | ? | ? | Y | Bank reference numbers | Maintenance |
| **BBMS** (Build Brighton) | Y | ? | ? | Y (inductions) | Y | N | N | Y (member credit) | Y (RFID door + equipment) | ? | ? | ? | GoCardless, PayPal | Dormant (2024-04) |
| **Arooo** (Double Union) | Y (apply/vote) | N | N | N | N | N | N | N | N | ? | P | Y | Stripe | Maintenance |
| **makerspaceleiden-crm** | P (no payments) | ? | ? | Y (instruction records) | Y | N | N | N | Y (RFID API) | Y | Y | Y | none | **Active** |
| **byro** (generic, DACH) | Y | ? | ? | N | N | N | N | Y (bookkeeping) | N | ? | ? | Y (+ mailman) | Bank import (FinTS) | **Active** |
| **FabAccess** (BFFH / V2) | N | N | N | N | Y | N | N | N | Y (Shelly, ESPHome) | Y (Borepin app) | P | N | — | **Stalled** |
| **MakerPass** | N | N | N | N | Y | N | N | N | Y (MQTT Wi-Fi relays) | Y (iOS) | N | N | — | Dormant |
| **MESSforMakers** (MakeICT) | planned | — | — | — | — | planned | planned | P | — | — | — | — | — | **Dead** (2020) |

## 2. Repo facts (GitHub API, 2026-10-12)

| Repo | License | Stack | Last commit (default) | 12-mo commits | Stars / forks | Self-reported deployments |
|---|---|---|---|---|---|---|
| [membermatters/MemberMatters](https://github.com/membermatters/MemberMatters) | MIT | Django + Vue/Quasar, Docker | main 2025-03-08 (v3.8.0); dev 2026-04-17 | 0 main; 4 dev | 93 / 51 | 6 in README + HSBNE |
| [sleede/fab-manager](https://github.com/sleede/fab-manager) | AGPL-3.0 per README | Rails + React, Docker | 2026-07-14 (v6.5.10) | 91 | 254 / 127 | ">100 Fablabs" (vendor) |
| [NottingHack/hms2](https://github.com/NottingHack/hms2) | MIT | Laravel/PHP + Doctrine | 2026-07-29 | 53 (6 people) | 15 / 19 | 1 |
| [Protospace/spaceport](https://github.com/Protospace/spaceport) | AGPL-3.0 | Django + React, SQLite | 2026-10-06 | 100+ | 18 / 19 | 1 |
| [elplatt/seltzer](https://github.com/elplatt/seltzer) | GPL-3.0 | PHP | 2026-05-31 (v0.8.11) | 14 (1 person) | 103 / 50 | i3 Detroit since 2010; forks at Red Mountain Makers, TVCOG |
| [vhs/nomos](https://github.com/vhs/nomos) | **none detected** | PHP 8.2, Docker, RabbitMQ | 2025-11-19 (dependabot) | 7 | 27 / 8 | 1 |
| [makerspace/makeradmin](https://github.com/makerspace/makeradmin) | **no LICENSE file** | Python 3.11, Docker | 2026-08-29 | 100+ | 28 / 19 | 1 |
| [TheLab-ms/conway](https://github.com/TheLab-ms/conway) | MIT | Go | 2026-07-22, **archived** | 100+ | 2 / 0 | 1 |
| [TampereHacklab/mulysa](https://github.com/TampereHacklab/mulysa) | GPL-3.0 | Django | 2026-09-10 | ~9 human | 12 / 22 | 1 |
| [ArthurGuy/BBMembershipSystem](https://github.com/ArthurGuy/BBMembershipSystem) | GPL-3.0 | Laravel 5 | 2024-04-01 | 0 | 20 / 15 | 1 |
| [doubleunion/arooo](https://github.com/doubleunion/arooo) | GPL-3.0 | Rails, Heroku | 2026-06-05 | 10 | 71 / 53 | 1 |
| [MakerSpaceLeiden/makerspaceleiden-crm](https://github.com/MakerSpaceLeiden/makerspaceleiden-crm) | Apache-2.0 | Django | 2025-11-21 | 35 | 8 / 9 | 1 |
| [byro/byro](https://github.com/byro/byro) | AGPL-3.0-only | Django, plugins, Docker | 2026-10-10 | 85 human | 170 / 52 | "several communities" |
| [FabInfra/FabAccess-V2](https://github.com/FabInfra/FabAccess-V2) + [gitlab bffh](https://gitlab.com/fabinfra/fabaccess/bffh) | MIT (V2) / GPL (V1) | TypeScript (V2) / Rust (BFFH) | V2 2025-07-16; BFFH 2025-03-05 | 0 | 8 / 5 | unknown |
| [google/makerspace-auth](https://github.com/google/makerspace-auth) | Apache-2.0 | Python on Raspberry Pi + custom HAT | 2026-06-16 | 9 | 69 / 30 | Google internal |
| [bennetthermanoff/maker-pass-server](https://github.com/bennetthermanoff/maker-pass-server) | GPL-3.0 | Node/TS, SQLite, MQTT | 2025-10-05 | 0 | 1 / 0 | Tulane |
| [nu31hackerspace/prismo](https://github.com/nu31hackerspace/prismo) | MIT | ESP32-C3 + PN532 firmware, MQTT | 2026-10-08 | 100+ | 38 / 1 | unknown |
| [Dallas-Makerspace/makermanager](https://github.com/Dallas-Makerspace/makermanager) | BSD-2 | CakePHP glue: WHMCS ↔ Active Directory | 2026-05-09 | 4 | 4 / 7 | 1 |
| [MakeICT/MESSforMakers](https://github.com/MakeICT/MESSforMakers) | GPL-3.0 | Go + Postgres | 2020-01-02 | 0 | 13 / 9 | 0 |
| [southlondonmakerspace/membership-system](https://github.com/southlondonmakerspace/membership-system) | non-standard | Node | 2020-02-02 | 0 | 48 / 21 | 1 |
| [glowingkitty/HackspaceOS](https://github.com/glowingkitty/HackspaceOS) | MIT | Django (website, not membership) | 2020-06-03 | 0 | 19 / 8 | — |
| [londonhackspace/hackspace-foundation-sites](https://github.com/londonhackspace/hackspace-foundation-sites) | **none detected** | PHP + Python, GoCardless | 2026-09-04 | 5 | 32 / 33 | 1 |
| [uid0/openmakersuite](https://github.com/uid0/openmakersuite) | AGPL-3.0 | Django + React, Postgres | 2026-10-01 | 100+ (1 person + bots) | 1 / 0 | inventory only |
| [wyemake/mventory](https://github.com/wyemake/mventory) | MIT | Python | 2025-02-10 | 0 | 120 / 6 | inventory only |
| [HMC-Makerspace/MAKE](https://github.com/HMC-Makerspace/MAKE) | GPL-3.0 | Express + Mongo + React | 2026-10-10 | 100+ | 11 / 9 | 1 (university: workshops, tool checkout, shifts) |

## 3. Per-project detail

**MemberMatters.** Stripe recurring dues with self-service billing. Swipe access is switched off automatically when payment is overdue and back on when the member returns. Mandatory induction through Canvas or Moodle at signup. Acts as an OIDC and Discourse SSO provider. Electron kiosk with site sign-in/out. ESP32 firmware supports Wiegand and UART readers, door strikes, tool interlocks and vending. Email via Sendgrid/Postmark, SMS via Twilio, Discord posts. Hosting: officially only the Docker Hub image on Ubuntu LTS. The HSBNE wiki lists its maintainer as "No one" (page ~3 years old). [README](https://github.com/membermatters/MemberMatters) · [HSBNE wiki](https://wiki.hsbne.org/_export/raw/infrastructure/services/memberportal)

**Fab-manager.** Source tree confirms: plans and subscriptions, trainings with a trainings-to-machine link, reservations for machines/spaces/trainings, events with price categories, invoices and accounting periods, wallet, store, prepaid packs, supporting documents, statistics, OIDC/SAML sign-in. Payments: Stripe and PayZen. Hosting: self-host, or vendor cloud from €45 excl. VAT per month. Gaps: no door/keyfob integration; FR-centric (French VAT anti-fraud compliance). [fab-manager.com](https://www.fab-manager.com/) · [repo](https://github.com/sleede/fab-manager)

**HMS2 (Nottingham Hackspace).** Members' guide confirms registration, induction booking, payments. Source names suggest tool bookings, bookable areas, Snackspace accounts, governance/voting, Stripe donations. Gatekeeper (RFID door) and nh-tool (RFID tool access) are separate "Instrumentation" projects. [Guide](https://guide.nottinghack.org.uk/) · [wiki](https://wiki.nottinghack.org.uk/wiki/HMS_and_Instrumentation)

**Spaceport (Protospace).** Models: Member, Transaction, Card, Course, Session, Training, StorageSpace, PayPal IPN. Integrations: LDAP/AD, Discourse, MediaWiki, printer billing, Bambu and Prusa printers. Syncs cards to the "Airlock" door controller; training certifications control tool lockouts. Author: "tightly coupled to Protospace." [repo](https://github.com/Protospace/spaceport) · [write-up 2022-05-16](https://tanner.vc/spaceport/)

**Seltzer CRM.** Modules: member, plan, billing, payment, paypal_payment, key, mentor, email_list. Deliberately minimal; one contributor. [README](https://github.com/elplatt/seltzer)

**Nomos (Vancouver Hack Space).** Services: Membership, MemberCard, Key, Pin, Privilege, Payment, StripeEvent, IPN, WebHook, Metric. No license detected. [repo](https://github.com/vhs/nomos)

**makeradmin (Stockholm Makerspace).** Member admin, access groups, member portal, Stripe subscriptions plus webshop, quizzes, statistics, Accessy integration. No LICENSE file. [README](https://github.com/makerspace/makeradmin)

**Others.** Conway: Stripe dues + door access, archived despite July 2026 commits, reason unknown. Mulysa: bank-reference dues, NFC or phone-number door access, LDAP. BBMS: GoCardless, inductions, RFID door and equipment with auto-billing, voting, storage. Arooo: applications, voting, Stripe dues, scholarships. makerspaceleiden-crm: RFID permission API, instruction tracking, storage, self-service. byro: plugin-based membership admin, FinTS bank import, German donation receipts.

**Access-control hardware projects.** FabAccess: Shelly-based actors, V2 rewrite stalled ([docs mirror](https://gitea.fablabchemnitz.de/FabInfra-Mirror/bffh)). google/makerspace-auth: Raspberry Pi HAT for gating tools. MakerPass: iOS app + MQTT Wi-Fi relays. prismo (nu31): ~$10 ESP32-C3 + PN532 NFC controller with hardware-in-the-loop CI. SpaceAPI: only an open/closed-status JSON spec, not management software ([directory](https://github.com/SpaceApi/directory)).

## 4. Generic open-source platforms people adapt

Repo activity confirmed; feature notes mostly from general knowledge, marked *inferred* unless stated.

| Project | License | Last commit | Fit |
|---|---|---|---|
| [CiviCRM](https://github.com/civicrm/civicrm-core) | AGPL-3.0 | 2026-10-12 | Memberships, contributions, paid events, many processors via extensions. No tool or door features. Makerspace use confirmed at [La Patente, CiviCamp 2024](https://civicrm.org/sites/civicrm.org/files/inline-images/Thomas%20Guignard%20-%20La%20Patente%20-%20civiCamp%202024_0.pdf). i3Detroit's `i3detroit-civicrm` repo archived 2021. |
| [hitobito](https://github.com/hitobito/hitobito) | AGPL-3.0 | 2026-10-12 | Group hierarchies, event registration (Swiss associations) |
| [Admidio](https://github.com/Admidio/admidio) / [Galette](https://github.com/galette/galette) | GPL-2 / GPL-3 | 2026-10 | Member and group admin, dues tracking |
| [LibreBooking](https://github.com/LibreBooking/librebooking) | GPL-3.0 | 2026-10-10 | Resource reservations with group permissions and quotas. Fork of Booked Scheduler, which went SaaS-only Nov 2020 ([history](https://wiki.linux-server-admin.com/web-apps/online-appointment-scheduling/booked-scheduler/history)) |
| [Snipe-IT](https://github.com/grokability/snipe-it) | AGPL-3.0 | 2026-10-12 | Asset checkout to people; not a reservation calendar |
| [Easy!Appointments](https://github.com/alextselegidis/easyappointments) | GPL-3.0 | 2026-10-06 | Appointment booking; no certification gating |
| [pretix](https://github.com/pretix/pretix) | AGPL + additional terms | 2026-10-10 | Ticketing and paid classes; Stripe, PayPal and others |
| Mobilizon | AGPL | unknown (Framagit API blocked) | Federated events, no payments; handed from Framasoft to Kaihuri in 2024; latest confirmed release 5.1.0 (Dec 2024) |
| [OCA/vertical-association](https://github.com/OCA/vertical-association) | AGPL-3.0 | 2026-09-28 | Odoo membership add-ons; branches up to 20.0 |
| [frappe/non_profit](https://github.com/frappe/non_profit) (ERPNext) | GPL-3.0 | 2024-06-12, **archived** | No longer maintained |
| [Twenty](https://github.com/twentyhq/twenty) | AGPL + some commercial-licensed files | 2026-10-12 | Generic CRM |
| [Monica](https://github.com/monicahq/monica) | AGPL-3.0 | 2026-09-24 | Personal CRM; not a fit |

OpenACS/.LRN and Grav/Drupal membership modules were not evaluated.

## 5. PEG / community-media tools

- **Open Media Project (Drupal):** started by Denver Open Media in 2008; drupal.org says "No longer developed by its maintainers", last update 2014. [drupal.org/project/openmedia](https://www.drupal.org/project/openmedia)
- **MERCI** (equipment reservations): 78 sites report using it; "no supported stable releases." [drupal.org/project/merci](https://www.drupal.org/project/merci)
- **[cm-advanced GitHub org](https://github.com/cm-advanced):** Drupal 7 modules incl. cm_cablecast, telvue, telvue_airing_import, reservations_*, internet_archive, civicrm_multiday_event. Last push 2019. Dead.
- **Cablecast-specific:** [raytiley/Cablecast-Drupal-Module](https://github.com/raytiley/Cablecast-Drupal-Module) (2021); [trms/wp-cablecast](https://github.com/trms/wp-cablecast), vendor GPL-3 WordPress plugin, last push 2026-01, displays schedules only. Cablecast's **Content Submission Portal** (7.10) is proprietary ([Creative COW](https://creativecow.net/tightrope-media-systems-unveils-cablecast-version-7-10-software/)).
- **[Frikanalen](https://github.com/Frikanalen/frikanalen)** (Norway): LGPL-3.0, last commit 2025-07-07, 32 stars. User video upload, processing, playout, ATEM switcher control. The only live open-source public-access-TV stack found.
- **Generic options for equipment checkout:** Snipe-IT and LibreBooking. No source confirms a PEG station using either.

## 6. Candidates not found or not applicable

- Not open source: Fabman (SaaS, open API), Tulip (commercial manufacturing-app platform).
- No matching software found: Spacebook, Oblong, mkr.space, Makerspace.com, MakerLabs, Milwaukee Makerspace, Hack Manhattan, NYC Resistor, Noisebridge.
- "HackerspaceOS" is HackspaceOS, a dead website project (2020).
- Leeds Hackspace `lhm-mothership` (2019). Bodgery `bodgery-member-api` (2020). Open Source Ecology: not management software.

## 7. Prior "one system for makerspaces" attempts and why they stalled

No direct Reddit post-mortem found. Patterns from READMEs, forums and talks:

- **MESSforMakers (MakeICT):** built because of "lack of features, poor support, and broken APIs" in their existing system. README still says "very first stages… very little written in the way of code." Dead since 2020.
- **Coupling to the home space:** Spaceport "tightly coupled to Protospace"; BBMS "Build Brighton naming is hardcoded"; Kasownik (Warsaw) README says it is "a mess" and you'll "have to hack on it".
- **Volunteer turnover:** HSBNE wiki lists MemberMatters' maintainer as "No one"; La Patente's CiviCamp 2024 talk describes volunteer sysadmins with high turnover and cloud accounts tied to individuals.
- **Sustainability:** Booked Scheduler's author closed the open-source edition after ~20 years ("no longer sustainable"); Framasoft handed Mobilizon to Kaihuri; Open Media Project stopped.
- **Rewrites that stall:** FabAccess Rust V1 → TypeScript V2, no commits since July 2025; Nomos split into old and new React frontends.

---

**Confirmed:** repo metadata, last commits, licenses, README feature lists, deployment lists, drupal.org status, Booked and Mobilizon history.
**Inferred:** features marked y*, and feature notes on generic platforms in section 4.
**Not verified:** Fab-manager's lab count (vendor claim); makeradmin's legal status (no license file).
