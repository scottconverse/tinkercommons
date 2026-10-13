# Door systems, payments, data migration, hosting — research report

Prepared 2026-10-12 by a research agent (Opus) under Fable coordination. Official vendor docs first, then
forum evidence. Reddit never surfaced in search results, so no Reddit threads are cited. Three product
names from the brief ("MakerAccess", "MachineTag", "NexusLock") returned nothing and may not exist.
"Not published" and "unknown" are used where that is the truth.

Agent cost: 170,829 tokens, 75 tool calls, 4m56s (estimate was 100k–150k).

---

## Q1. Door, keyfob and tool-lockout systems

### Door access platforms

| System | API? (docs) | Webhooks | Published pricing | Makerspace use found? |
|---|---|---|---|---|
| **Kisi** | Yes, open API: https://docs.kisi.io/platform/apis/webhooks/ | **Yes.** Event webhooks such as `lock.unlock`, HMAC-SHA256 signed in an `X-Signature` header; events may arrive more than once; filter by type. https://docs.kisi.io/api/how_to_guides/set_up_webhooks/track_unlocks/ · https://www.getkisi.com/updates/filters-for-event-webhooks | "Starting at $99" (unit unclear). Controller Pro 2 $850, Reader Pro 2 $475, Wiegand board $150. Door licence prices not shown, no nonprofit discount listed: https://www.getkisi.com/pricing. Vendr *estimates* $50–80 per door per month: https://www.vendr.com/buyer-guides/kisi | Not found for makerspaces. Coworking platforms integrate it (Spacebring, andcards, Deskworks): https://help.andcards.com/en/articles/6861665-integrate-with-kisi |
| **Brivo** | Yes; register at developer.brivo.com for API key and sandbox. Third-party overview: https://help.maptician.com/presence/brivo-technical-overview | **Yes**, event subscriptions with a callback URL (community example): https://github.com/christophertino/mindbody-brivo/blob/main/README.md | Not published | Not found |
| **ProdataKey (PDK)** | Yes, the pdk io API | **Yes**, since Nov 2020 (door open, user added/deactivated…): https://www.sdmmag.com/articles/98885-prodatakey-introduces-webhooks | Not published | Not found |
| **Avigilon Alta (ex-Openpath)** | Yes, "Open API": https://www.avigilon.com/fs/documents/Alta-Access-Fact-Sheet.pdf. HID integration needs Premium/Enterprise licence: https://docs.his.hidglobal.com/user-documentation/his-agents/his-avigilon-alta-agent | Yes, "Event Forwarder" with `X-Hook-Secret` handshake, but only source is an old Confluence page: https://openpath.atlassian.net/wiki/x/EYCwo | Not published | Not found |
| **Verkada Access** | Yes, public: https://apidocs.verkada.com/reference/access-api-101 | **Yes**: https://apidocs.verkada.com/reference/access-events-guide · https://apidocs.verkada.com/reference/securing-webhooks | Reseller datasheet: AC41 4-door controller $1,599; door licences $249/door/yr or $999/door/5 yr: https://cssi.com/wp-content/uploads/cssi_Verkada_Access_Control_Datasheet.pdf | Not found |
| **UniFi Access (Ubiquiti)** | Yes. API token in Access settings (v1.9.1+), local on port 12445. A vendor guide says the API **is not available after upgrading to Identity Enterprise**: https://learn.nexudus.com/platform/access-control/integrations/unifi-access. Official: developer.ui.com and https://help.ui.com/hc/en-us/articles/30076656117655 | **Unclear.** openHAB forum disagrees: https://community.openhab.org/t/unifi-access-api/149584/14. openHAB binding uses internal API with WebSocket events plus polling: https://www.openhab.org/addons/bindings/unifi/doc/access | Hardware only: G2 reader $139, Access Hub $199 (https://store.ui.com/us/en/category/door-access-readers/products/ua-g2). "No subscription" claim comes from resellers/competitors, not Ubiquiti: https://www.getkisi.com/access-control-systems-comparison/kisi-vs-ubiquity-unifi | **No makerspace write-up found** |
| **Paxton Net2** | Local REST Web API, needs licence and "Enable LocalAPI". **Since 1 July 2024 Paxton only gives API access to commercial integrators**: https://www.paxton-access.com/integrating-with-paxton/how-to-integrate-with-net2/ · https://help.kinnovis.com/en/articles/10117985-step-1-enable-the-net2-api | None found | Not published | Not found |
| **HID (Origo)** | Partner-program members only: https://doc.origo.hidglobal.com/api/ | Callback events for users/devices/credentials: https://doc.origo.hidglobal.com/api/mobile-identities/ | Not published | Not found |
| **Salto KS** | Connect API; client ID/secret from local Salto Business Unit: https://developer.saltosystems.com/ks/connect-api/index.md | **No HTTP webhooks.** Real-time events over SignalR: https://developer.saltosystems.com/ks/guides/event-streaming-ks/ | Not published | Not found |
| **Genea** | API keys exist, no public REST reference found. SCIM via Okta: https://www.okta.com/en-ca/integrations/genea-access-control/ | **Yes**, access and audit events: https://help.getgenea.com/en/articles/1292571-genea-events-webhook | Not published | Not found |

### Makerspace-specific and DIY systems

| System | What it is | Evidence |
|---|---|---|
| **Fabman** (software + its own Bridge hardware) | Membership, billing and machine lockout in one. REST API on every plan. Webhooks require HTTPS; any 2xx = success. Plans €49 (100-member cap), €99, €199 per month, plus €10–14/month per connected machine, plus 0.5–2% transaction fee. **Bridge hardware price not published**; a 2020 paper cited €199 each. | https://fabman.io/pricing · https://github.com/FabmanHQ/fabman-api/blob/master/sections/webhooks.md · https://www.scitepress.org/Papers/2020/89119/89119.pdf. Users: Happylab Vienna (1,700+ members) https://fabman.io/blog/2020-01-28-fabman-story/; HPI Hardware Studio https://maker.hpi.de/studios/hardware-studio.html. Bridge can force periodic re-swipe and cut power: https://help.fabman.io/article/37-fabman-bridge |
| **MemberMatters** (MIT) | Self-hosted membership, Stripe billing, door and tool access on ESP32. Cards auto-disabled on overdue payment. Docker on Ubuntu LTS is the only supported install. | https://github.com/membermatters/MemberMatters. Listed users: Brisbane Makerspace, Make Monmouth, SparkCC, Pawprint Prototyping. HSBNE's older interlock: Sonoff relay + contactor + RFID: https://wiki.hsbne.org/infrastructure/services/accesscontrol |
| **FabAccess** (BFFH; newer Attraccess/V2) | Open-source machine access from Germany; README calls it "prototype-grade". Shelly relays over MQTT. V2 adds ESPHome components. | https://archive.fosdem.org/2023/schedule/event/fabaccess/ · https://fab-access.readthedocs.io/en/v0.3/usage/setup_steps.html · https://hub.docker.com/u/fabaccess · ~10 German pilot workshops: https://offene-werkstaetten.org/en/page/an-access-system-for-open-workshops |
| **Makerspace Auth / authbox** (Google) | Raspberry Pi tool-lockout client with OSHWA-certified "Authboard" and a simple HTTP protocol. | https://pypi.org/project/authbox/ · https://certification.oshwa.org/us000093.html. GitHub copy found was tylerl/makerspace-auth; check which repo is canonical. |
| **ESPHome Wiegand** | Native component for Wiegand 26/34 readers. | https://esphome.io/components/wiegand/ |
| **Other DIY builds** | Decatur Makers: ESP32/ESPHome + Flask + Neon CRM, "concept" stage (https://pypi.org/project/machine-access-control). Makerspace Leiden ACNode, ESP32 with PoE (https://www.arduinolibraries.info/libraries/ac-node). **TinkerMill TinkerAccess on Raspberry Pi** (https://news.sparkfun.com/7764). MakerBarn MACS (https://hackaday.com/tag/hacerspaces). CarontePass (https://hackaday.com/2016/04/13/). HackRVA memberserver: pulls PayPal payments, pushes lists to door controllers that cache locally (https://hub.docker.com/r/hackrva/memberserver). Maker Nexus via EZ Facility API (https://www.hackster.io/PracticalProjects/particle-based-rfid-access-control-system-9373c4). Cheap TCP RFID controllers with a Python library, $30–85: https://pypi.python.org/pypi/Chinese-RFID-Access-Control-Library/0.0.5 |
| **Wild Apricot** | No native door integration; open wishlist item: https://forums.wildapricot.com/forums/308932-wishlist/suggestions/8828131-card-readers | — |

### Commercial tool lockouts
- **Fabman Bridge**: above.
- **RFID Interlock** (rfidinterlock.com): current-sensing lockout, Active Directory integration. **No published price, no published API.** No named makerspace customers (a team member was a Dallas Makerspace director).
- **iNTELLILOCK** (Laser Optical, UK): bespoke laser-safety RFID interlock, no list price: https://laseroptical.co.uk/laser-safety/
- **Shelly relays** appear only inside DIY/FabAccess setups. **No Fabman–Shelly integration found.**
- **Safety note:** keep the machine's e-stop and safety interlocks wired independently of the access relay: https://esp32.co.uk/esp32-smart-relay-for-home-assistant-esphome/

**Q1 takeaway.** Hobbyist makerspaces mostly build their own on ESP32/Raspberry Pi, or buy Fabman. **No public makerspace evidence** for any enterprise cloud vendor. Cleanest APIs to build against: **Kisi, Verkada, Genea** (documented, signed webhooks) and **UniFi Access** (no licence fees, local API, webhooks unconfirmed). **Paxton, HID, Salto** are effectively closed to DIY integrators.

---

## Q2. Payments and point of sale (US 501(c)(3))

| | Online card | In person | ACH | Recurring / metered | Nonprofit rate |
|---|---|---|---|---|---|
| **Stripe** (https://stripe.com/pricing) | 2.9% + 30¢ (+0.5% keyed, +1.5% international) | Terminal 2.7% + 5¢; Tap to Pay +10¢/auth | 0.8%, cap $5 | Billing adds **0.7% of billing volume** (pay-as-you-go); Invoicing 0.4%. Usage-based "meters" handle metered usage: https://docs.stripe.com/billing/subscriptions/usage-based/how-it-works | **Not on pricing page.** Third parties report 2.2% + 30¢ (Amex 3.5%) via nonprofit@stripe.com, **only if ≥80% of volume is tax-deductible donations.** Dues/tickets/memberships stay standard; not retroactive: https://guides.kindkiosk.com/article/stripe-nonprofit-rate · https://givebutter.com/blog/stripe-for-nonprofits · https://help.goharness.com/en/articles/3159903-nonprofit-discount-for-donation-processing-on-stripe-2-2-0-30 |
| **Square** (https://squareup.com/help/us/en/article/5068-what-are-square-s-fees) | 3.3% + 30¢ Free plan; 2.9% + 30¢ Plus/Premium or API | 2.6% + 15¢ Free, 2.5% Plus, 2.4% Premium | 1%, $1 min, $5–10 cap | Card on file 3.5% + 15¢ | **None.** Fee "applies to all businesses, including non-profit organizations." Custom rates above $250k/yr. |
| **PayPal** (https://www.paypal.com/us/business/paypal-business-fees) | 2.99% + 49¢; Advanced cards 2.89% + 29¢ | — | 0.80%, cap $5 | — | Charity rate **1.99% + 49¢** (Advanced 2.19% + fixed), pre-approval required |
| **Braintree** (https://www.paypal.com/us/enterprise/paypal-braintree-fees) | 2.89% + 29¢ | — | 0.75%, cap $5 | — | **2.19% + 29¢** for verified 501(c)(3)s, pre-approval |

**For this revenue mix the nonprofit discounts mostly do not apply.** Stripe's needs ≥80% donations; Square has none.

**Kiosks and hardware**
- Stripe readers: M2 $59, S700/S710 $299: https://stripe.com/terminal
- Truly unattended Stripe kiosks are **private preview, Verifone UX700 only**. Staff-nearby "semi-attended" works with standard readers: https://docs.stripe.com/terminal/unattended-readers · https://support.stripe.com/questions/using-terminal-in-attended-semi-attended-and-unattended-retail-environments
- Square Kiosk price: not retrieved (page truncated).
- **Do makerspaces need a POS kiosk?** The one concrete example: **Dallas Makerspace** uses on-site **Square kiosks** plus a PayPal link for consumables and tool fees (laser $1 per 5 min): https://wiki.dallasmakerspace.org/wiki/DMS_Additional_Fees
- Library makerspaces: self-reporting with suspension for non-payment (Rodman Library) or pay at a staffed desk (UNLV, Mentor PL).
- Square community warning: self-serve iPad POS exposes discount and "More" buttons to customers: https://community.squareup.com/t5/Orders-Menu-Items-Catalog/Non-Restaurant-Self-Service-with-a-Handheld-Scanner/m-p/713344
- **Conclusion:** a kiosk for honor-system fees is common but not universal. Usage metered by a tool lockout (Fabman bills machine time directly) can replace it.

---

## Q3. Data migration

| Source | Export formats | API | Can saved cards move? | Known pain points |
|---|---|---|---|---|
| **Wild Apricot** | CSV for contacts, invoices, payments, each separately | Yes, v2.2; contacts via async result ID; Payments and Refunds endpoints. https://forums.wildapricot.com/forums/309658-developers/suggestions/15927271-built-in-export-reports-data-via-api | WA never stores card data; it sits with AffiniPay/Personify, Stripe or PayPal: https://supportcenter.affinipay.com/en/articles/9458067-payment-processing-in-wild-apricot. Card moves depend on that processor. | Three exports share no common fields; you join them yourself. Group memberships come out as one comma-joined column: https://forums.wildapricot.com/forums/308932-wishlist/suggestions/10683792-data-exports. Ownership changed Jan 2026 (Momentive), vendor-biased source: https://blog.nextbee.com/2025/12/13/from-wild-apricot-to-your-ams-real-migration-stories/ |
| **Proximity** | **No export documentation found** | **No public API found.** Integrations page lists Stripe, QuickBooks, **Zapier** (https://proximity.space/integrations); Zapier app has triggers like "Door Locked" (https://zapier.com/apps/nexkey/integrations/proximity). "Prox Docs" mentioned, nothing public linked; hello@proximity.space: https://proximity.space/support | Runs on Stripe, so the Stripe route below likely applies (inference) | Unknown. Now positions itself as a CRE/coworking platform. *(Cross-reference: the REA report found the member bundle has a Laravel Passport personal-access-token UI, so a per-user token against the portal's own JSON API may exist. Unverified.)* |
| **Booqable** | CSV export of orders, customers, inventory, reports, with filters and column choice: https://help.booqable.com/en/articles/5603655 | API docs not found | Unknown | Unknown |
| **Nexudus** | **XLS/XLSX** from any admin list; needs full unrestricted admin. Customer export includes Account and Payments tabs and custom fields: https://help.nexudus.com/docs/data-export · https://help.nexudus.com/docs/exporting-customers | REST API exists (third-party list: https://docs.celigo.com/hc/en-us/articles/34705647296667-Available-Nexudus-APIs) | Unknown, depends on gateway | No CSV option |
| **Donorbox** | CSV ("All Columns": donations, supporters, recurring plans), per third-party plugin docs: https://www.wpcharitable.com/documentation/donorbox-importer/ | Not confirmed | On Stripe, so Stripe-to-Stripe transfer possible. **PayPal recurring gifts cannot migrate**; donors re-enrol. Migrating *into* Donorbox costs $195: https://donorbox.org/migrations | Imported recurring plans may arrive cancelled: https://help.givebutter.com/en/articles/5489025-how-to-migrate-recurring-plans |
| **Airtable** | CSV per view/table, not per base | Yes; 5 req/s per base, 50/s per token; 429 = 30-second wait: https://airtable.com/developers/web/api/rate-limits | n/a | Attachment URLs in exports expire; re-fetch via API |

**How saved cards actually move**
- **Into Stripe:** request through Dashboard; data goes processor-to-Stripe, days to weeks; **subscriptions are not migrated**, rebuild with the Billing Migration Toolkit: https://docs.stripe.com/get-started/data-migrations/pan-import
- **Out of Stripe:** only to a PCI DSS Level 1 processor, 4096-bit PGP key; Link-saved cards excluded: https://docs.stripe.com/get-started/data-migrations/pan-export
- **Out of Square:** owner emails card-export-requests@squareup.com, verification call, up to 2 weeks, PCI Level 1 destination only, "not intended to support bulk migration requests": https://squareup.com/help/us/en/article/7871-export-card-on-file-to-third-party-payment-processors
- **Implication:** if the new system uses Stripe, orgs leaving other Stripe-based tools (Proximity, Donorbox) have the easiest path, because card tokens move between Stripe accounts.

---

## Q4. Hosting: self-host or managed?

| Evidence | What it shows |
|---|---|
| MemberMatters supports **only** self-hosting via Docker on Ubuntu; no hosted offering: https://github.com/membermatters/MemberMatters | Hackerspaces with technical volunteers do self-host. Only 4 public deployments listed. |
| CiviCRM case study: nonprofit self-hosted for years, volunteer IT could not keep up with upgrades, moved to a host (Cividesk): https://civicrm.org/node/5645 (vendor testimonial) | Volunteer maintenance is the failure point, not server cost. |
| OSMF: self-hosting cost "essentially nil" because servers and staff already exist: https://lists.openstreetmap.org/pipermail/osmf-membership/2015-January/000013.html | Works when ops people already exist. |
| Bristol Hackspace split its portal into smaller services to stay maintainable: https://wiki.bristolhackspace.org/projects/services | Same theme. |
| CiviCRM managed hosting: Symbiotic $35/$65/$130 per month (https://www.symbiotic.coop/en/civicrm-hosting); CiviHosting $16–22 shared, $249–599 VPS (aggregator: https://www.whtop.com/plans/civihosting.com/152690); CiviCRM Spark $15/$30/$50 (unverified: https://us.fitgap.com/products/024069/civicrm) | **Managed hosting ≈ $15–130/month for small orgs.** |
| Discourse hosted: Pro $100/mo, Business $500/mo, **50% off nonprofits**; self-host free, 1–2 GB VPS ≈ $10–20/mo (undated): https://www.discourse.org/pricing · https://meta.discourse.org/t/which-do-plan-should-i-use-for-the-pre-configured-discourse-droplet/38613 | Hosted ≈ 5–10× raw VPS cost. |
| Nextcloud: managed for nonprofits from $11/mo (https://landing.elest.io/for-nonprofits); €61/mo up to 25 users (https://tab.digital/en/nextcloud-for-nonprofits); VPS from €4.50/mo; hidden costs are backups, upgrades, email: https://deployhandbook.com/compare/hosted-vs-self-hosted-nextcloud-small-teams | Same pattern. |

**Q4 takeaway.** Raw self-hosting ≈ $5–20/month. Managed ≈ $15–130/month. Every failure story was volunteer upgrade/maintenance capacity, not money. **No survey data** on makerspace self-host vs SaaS. Design: self-hostable, plus an official managed option, one-command upgrades, tested backups.

---

**Gaps that remain:** no Reddit evidence; no per-door prices from Brivo, PDK, Alta, Genea, Salto, HID; no Fabman Bridge or Square Kiosk price; no public Proximity API or export docs; UniFi Access webhooks unconfirmed; no Booqable or Donorbox API docs.
