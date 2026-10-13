# Commercial SaaS tools used by makerspaces and PEG media centers — landscape as of 2026-10-12

Prepared by a research agent (Opus) under Fable coordination. Prices come from each vendor's own pricing
page as it loaded on 2026-10-12 unless a row says otherwise. "Not published" means the vendor page did not
state it. **(inf.)** / *i* marks inference. Downloaded source pages are in `raw-saas-agent-pages/`.

Agent cost: 222,610 tokens, 117 tool calls, 13m01s (estimate was 100k–150k).

## Main findings

- **No single commercial product covers the whole checklist** (tiers, certifications gating tools, booking rules tied to tier or certification, door/keyfob, one ledger, mobile portal).
- **Fabman comes closest for makerspaces.** $64, $129 or $259 a month (USD, monthly billing), plus $11–15 a month per machine wired to its hardware. Door access, training courses, REST API and webhooks on every tier.
- **PEG media centers mostly run Facil**, a desktop-licence product last updated 2022, $995–$2,695 one-time, "more than 245 organizations." Otherwise they build their own stack from Neon CRM, CiviCRM/Drupal, Booqable and MembershipWorks.
- **Real makerspaces run three to five tools stitched together.** TinkerMill: Wild Apricot + WaiverForever + Givebutter. Asmbly: Neon CRM + Skedda + Avigilon Alta. Pumping Station: One: WooCommerce + WaiverForever. Consumable payments fall back to cash boxes, a kiosk or Venmo.

## 1. Comparison table

Key: **Y** = vendor page says yes · **P** = partial / workaround · **N** = no · **?** = not published · *i* = inference

| Tool | Members, tiers, dues | Group memberships | Waivers | Orientation / attendance | Certs gate tools | Reservations w/ tier/cert rules | Paid classes/events | One ledger | Door/keyfob | Member mobile portal | Staff reports | Email / Slack | API / webhooks | Export |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Fabman** | Y | ? | ? | P (training courses) | **Y** (Bridge interlock) | Y / P (rules) | ? | Y (billing, credits) | **Y** (all tiers) | P (phone unlocks machines) | Y | ? | **Y / Y** | ? |
| **Zipper** | Y | ? | Y (join flow) | Y (inductions) | **Y** (prerequisite tags, expiring certs) | **Y** | Y | Y | P ("works alongside" door access) | Y (white-label app) | ? | Y (email, SMS) | Y (Growth tier) / ? | ? |
| **MakerOps** | Y | ? | **Y** (checked before machine powers on) | Y (LMS + sign-off) | **Y** ($349 controller) | P | P | P | N (machine power only) | Y (iOS/Android) | Y | ? | Y / Y | Y (SFTP) |
| **QReserve** | Y | ? | ? | ? | **Y** | **Y** | ? | Y (credits) | P ("access codes") | Y | ? | ? | Y ("API-first") | ? |
| **Wild Apricot** | Y | ? | N *i* | P | N *i* | N *i* | Y | Y *i* | P (custom API sync) | ? | Y *i* | Y / ? | **Y / Y** | ? |
| **Nexudus** | Y | Y (teams) | ? | ? | ? | Y | Y | Y | **Y** (Salto KS, Brivo, Kisi, Avigilon) | Y (Passport app) | Y (100+ reports) | Y / ? | Y | ? |
| **Optix** | Y | ? | P (SignNow, $0.86–$1/signature) | Y | ? | Y | ? | ? | Y (Kisi, Salto) | Y (add-on) | ? | ? / **Y** (Slack) | Y / Y (Pro+) | ? |
| **Cobot** | Y | ? | N | Y (Wi-Fi check-in add-on) | ? | Y | ? | Y (Xero, QuickBooks) | **Y** (Kisi, Sensorberg, Salto KS, Tapkey, Luckey, Dormakaba) | Y | Y | ? | Y / ? | ? |
| **Proximity** | Y | ? | ? | Y ("Wave") | ? | Y | Y (events) | Y (Stripe, QuickBooks) | **Y** (own hardware, Brivo, SALTO, Kisi) | Y (white-label) | Y | Y / Zapier | Zapier / ? | ? |
| **Skedda** | P ($99/mo add-on) | ? | ? | Y | ? | **Y** (quotas, windows, roles) | ? | P | P (Kisi) | ? | Y | ? | Y / ? | ? |
| **Booqable** | N | N | ? | N | N | Y (inventory booking) | N | Y (rental orders) | N | P | Y | ? | Y (Grow+) / ? | **Y** |
| **Cheqroom** | N | N | N | N | P (records certs) | **Y** | N | N | N | Y *i* | Y (Business) | ? / **Y** | **Y / Y** | ? |
| **Smartwaiver** | N | N | **Y** | N | N | N | N | N | N | Y | P | ? | **Y / Y** | Y *i* |
| **WaiverForever** | N | N | **Y** | N | N | N | N | N | N | Y (kiosk apps) | Y | ? | **Y / Y** (Standard) | **Y** |
| **Neon CRM** | Y (+10% module) | ? | ? | ? | N *i* | N *i* | Y (+20% module) | Y | N *i* | P | Y | Y / ? | **Y** / ? | ? |
| **MemberPlanet** | Y | **Y** | ? | ? | N | N | Y | Y | N | Y | Y | Y / ? | ? | **Y** |
| **Join It** | Y | ? | N | **Y** | N | N | Y | ? | N | Y | ? | Y / ? | **Y / Y** | ? |
| **Mindbody** | Y | ? | ? | Y | N | P | Y | Y | ? | Y (add-on) | Y | Y | Y (paid per call) / Y | ? |
| **Donorbox** | P (3.95%/2%) | ? | N | P | N | N | Y | P | N | N | Y | ? | Y (Pro or $17/mo) / ? | ? |
| **MembershipWorks** | Y | ? | ? | ? | ? | ? | Y | Y *i* | ? | ? | ? | Y (Mailchimp) | ? | ? |
| **Amilia** | Y | ? | ? | Y | ? | Y | Y | Y | ? | ? | Y | ? | Y (add-on) | ? |
| **Facil (PEG)** | Y | Y (orgs) | ? | ? | ? | Y | Y | P | N | N *i* | Y | Y / N | ? | ? |
| **Media Center Manager (PEG)** | Y | ? | ? | ? | ? | Y | Y | ? | N | ? | ? | Y | ? | ? |

## 2. Per-tool detail

### Fabman (Vienna; founded by the Happylab team)
- Makerspace operating system; "Bridge" hardware switches machines on by NFC card or phone, logs usage, creates charges.
- **Pricing, USD:**

  | Tier | Annual | Monthly | Per wired machine | Transaction fee | Members | Storage |
  |---|---|---|---|---|---|---|
  | Basic | $54/mo | $64/mo | $15/mo | 2.0% | 100 | 1 GB |
  | Standard | $109/mo | $129/mo | $13/mo | 1.0% | Unlimited | 3 GB |
  | Plus | $219/mo | $259/mo | $11/mo | 0.5% | Unlimited | 20 GB |

  EUR: €49/€99/€199 plus €14/€12/€10 per machine. Unwired machines free. Every tier: door access control, training courses, REST API, webhooks, credits, SEPA, multi-location. 30-day trial.
- Hardware: Bridge FB-V2 €249; NFC cards €2.90. Nonprofit discount not published.
- Adoption: Happylab 2,000 members; US logos NDSU Innovation Studio, Lacey MakerSpace (WA). Pilot customers per Feb 2026 Austrian funding record: Adidas Makerlab, Happylab, UnternehmerTUM, Goldsmiths. Total count not published.
- Sources: https://fabman.io/pricing?currency=USD · https://fabman.io/ · https://shop.fabman.io/products/fabman-bridge-v2 · https://projekte.ffg.at/projekt/3081742/pdf

### Zipper (joinzipper.com)
- Billed annually: Starter $118/mo (150 clients, 1% fee); Studio $254/mo (400 clients, 0%); Growth $424/mo (1,000 clients, API, second location); Multi custom. The $1,400/yr, $179/mo etc. on the page are sample member prices, not Zipper fees.
- Certification tags gate equipment booking and can expire; waiver, induction and first booking in one join flow. Door access "works alongside" door systems, no native integration. No customers named.
- Source: https://www.joinzipper.com/business-management-software/makerspaces

### MakerOps (Atlanticus Innovations)
- Starter $3,600/yr · Professional $11,500/yr · Campus from $24,000/yr. Machine controller $349, 15 A relay. Waiver and credential checked before power. REST API, webhooks, SSO, SFTP in nav. No customers named.
- Source: https://makerops.ai/

### QReserve (Hamilton, Ontario)
- Locks machines to members with current certifications; floor-plan booking; prepaid credits; renewal notices; "API-first". Pricing not published. Customers: ASU Chandler Innovation Center, Center for Book Arts, Humber Polytechnic, Video Pool Media Arts Centre.
- Source: https://get.qreserve.com/makerspaces

### Wild Apricot (Personify)
- USD/month from the page's embedded price data (visible page shows only the 100-contact tier):

  | Contacts | Monthly | 1-yr prepay | 2-yr prepay |
  |---|---|---|---|
  | 100 | $66 | $59.40 | $56.10 |
  | 250 | $82 | $73.80 | $69.70 |
  | 500 | $154 | $138.60 | $130.90 |
  | 2,000 | $265 | $238.50 | $225.25 |
  | 5,000 | $485 | $436.50 | $412.25 |
  | 15,000 | $584 | $525.60 | $496.40 |
  | 50,000 | $992 | $892.80 | $843.20 |

  No per-transaction fees (processor fees apply). 60-day trial. Nonprofit discount not published.
- API: Admin API and Member API. Webhooks for contacts, memberships, events, invoices, payments, refunds. Paging required from 2025-11-01.
- Adoption: "over 32,000 organizations" (© 2025 page). Makerspaces: **TinkerMill** (application), **MakeICT** (orientation events), **Milwaukee Makerspace** (2017, door PIN/fob sync via API).
- Sources: https://www.wildapricot.com/pricing · https://gethelp.wildapricot.com/en/articles/1670-webhooks · https://gethelp.wildapricot.com/en/articles/182-using-wildapricot-s-api

### Nexudus
- "Coworking professional" **$150/month per location** at 25 active users (scaling not published). Growth (500+): contact sales. White-label app $150/mo up to 5 locations. Nonprofit discount not published.
- Door: Salto KS, Brivo, Kisi, Avigilon. Payments: Stripe, GoCardless, PayPal. "+3000 spaces, +90 countries." **Artisan's Asylum (MA)** uses Nexudus + Eventbrite.
- Sources: https://nexudus.com/coworking-pricing/ · https://artisansasylum.com/memberships

### Optix
- Essentials $229/mo ($197 yearly, 50 users); Pro $349/$299 (100); Grow $579/$498 (250); Scale custom. API + webhooks from Pro. Add-ons: extra user $1.70–2; extra location $68–129/mo; Automations+ $85–99; white-label app $85–99; other gateways $42–49; document signing $0.86–1/signature; brand removal $1,199. Door: Kisi, Salto (RemoteLock coming). Slack, SignNow, QuickBooks, Xero. "1,000+ spaces." No makerspaces named.
- Sources: https://www.optixapp.com/pricing · https://www.optixapp.com/integrations/

### Cobot
- Base plan "Custom", by paying members. Add-ons: External Bookings $19/mo, Radius Wi-Fi from $29/mo, Premium Support $129/mo. **25% nonprofit discount**; 12th month free annually; 15–20% off extra locations. Door: Kisi, Sensorberg, SaltoKS, Tapkey, Luckey, Dormakaba. Open API (dev.cobot.me), Zapier, Xero, QuickBooks. **MakerSpace NYC** case study.
- Sources: https://www.cobot.me/pricing · https://www.cobot.me/integrations

### Proximity
- See `01-REA-proximity.md` for the full teardown. $209 (≤40 members) → $679+ per location/month; door access $99/mo + hardware; 2.9% + 30¢ cards, 1% ACH; 14-day trial; nonprofit discount not published. Door: Proximity Open, Brivo, SALTO, Kisi. Stripe, QuickBooks, HubSpot, Mailchimp, Zapier, Meraki Wi-Fi. No public API published. No makerspaces named.
- Sources: https://proximity.space/pricing · https://proximity.space/integrations

### Skedda
- US/Canada, annual: Plus from $249/mo (35 spaces); Premier from $349/mo (45); Enterprise custom; AllBooked custom (adds payments + membership). EMEA/APAC: $99/$149/$199. Extra spaces $4.99. Add-ons: administrators $99/mo, membership subscriptions $99/mo. 14-day trial. "8,000 customers." **Asmbly (Austin)** = Skedda + Neon CRM + Avigilon Alta Open.
- Sources: https://www.skedda.com/pricing · https://asmbly.org/join/

### Booqable
- Start $35/$29; Grow $87/$69 (API, e-signatures); Scale $187/$149; Custom from $349. Add-ons: website $19–24, POS $9–12/user, barcode $9–12/user, multi-location $29–37, deliveries $9. **20% off nonprofits and universities.** Data export listed. "8,500+ rental businesses." **Vidwest Studios** (Columbia, MO community media) = Booqable + MembershipWorks.
- Sources: https://booqable.com/pricing/ · https://www.vidwest.org/reservations

### Cheqroom
- Per admin/month, annual only: Core $184 (1 location); Business $275 (3 locations, equipment access control, reporting); Enterprise $367 (10 locations, booking portal, RFID); ELA custom. Slack, API, webhooks, Zapier on every tier. "1,400+ organizations."
- Source: https://www.cheqroom.com/pricing/

### Smartwaiver
- Basic $22/mo ($210/yr, 100 waivers); Starter $60 ($575, 300); Business $160 ($1,580, 1,000); Premium $205 ($2,029, 2,500). Storage-only $7/mo. Event passes $100/$250/$500. REST API + webhooks. **MakeICT** uses it. Nonprofit discount not published.
- Sources: https://www.smartwaiver.com/pricing · https://www.smartwaiver.com/integrations

### WaiverForever (waiver.fr short links redirect here — confirmed; this is TinkerMill's waiver tool)
- Essential $19 · Standard $39 · Scale $129 per month. First 50 signatures free, then $0.10 → $0.05 sliding. API, webhooks, spreadsheet export on Standard. "40,000+ customers." **TinkerMill** and **Pumping Station: One** use it.
- Sources: https://www.waiverforever.com/pricing · https://tinkermill.org/membership/ · https://pumpingstationone.org/join/

### Neon CRM (Neon One)
- "Starts at $99/month," scaling with org revenue. Modules: Memberships +10%, Volunteers +10%, Events +20%. Open API. "7,000+ nonprofits." Makerspaces: **Asmbly**, **Decatur Makers**. PEG: **PhillyCAM**, **SPNN**, **Amherst Media**.
- Source: https://neonone.com/solutions/neon-crm-overview/neon-crm-pricing/

### MemberPlanet
- Essentials $50/mo (2% platform fee); Pro $100 (1%); Premium $175 (1%); plus 3% + $0.30 processing. Unlimited admins/members, export included, organizational memberships. Add-ons: migration $1,500, theme $500, etc. API not published.
- Source: https://www.memberplanet.com/pricing

### Join It
- At 250 members: Starter $29/mo (3% fee); Total $99 (2%, API, check-in, digital cards); Extra $199 (1.5%); Enterprise custom. Annual 10% off; **extra 10% off nonprofits** (~19% stacked). "4,000+ organizations." Webhooks + REST API.
- Source: https://joinit.com/pricing

### Mindbody
- Starter from $79/mo per location; Accelerate/Ultimate "Let's talk." Public API $0.002/call after <5,000 free. "40K+ businesses." No makerspace use found.
- Sources: https://www.mindbodyonline.com/business/pricing · https://developers.mindbodyonline.com/

### Donorbox
- Standard $0 (2.95% forms, 3.95% memberships/tickets); Pro $150/mo (1.75% / 2%); Premium custom (1.6% / 2%). Zapier/API $17/mo on Standard. Live Kiosk $80/mo first device. Recurring-donor migration $195. "200,000+ nonprofits." **MNN** uses it.
- Source: https://donorbox.org/pricing

### Found along the way
- **MembershipWorks:** free ≤50 accounts, then $35–$259/mo (300–10,000 accounts). No transaction fee. Used by Vidwest. https://membershipworks.com/pricing/
- **Amilia:** Standard $99/mo (2.85% + 30¢ + 1% service, implementation from $899); Advanced $499/mo. "Preferred rates" for nonprofits. API premium add-on. Used by **Maker Nexus (CA)**. https://www.amilia.com/pricing
- **Glue Up:** Professional $3,000–6,500/yr; Advanced $8,000–18,500/yr. Used by **Generator (VT)**. https://www.glueup.com/pricing
- **Zeffy:** $0 platform fees, donor-tip funded; memberships and ticketing. Used by **HeatSync Labs, Synergy Mill, Nova Labs**. https://www.zeffy.com/pricing
- **Fab Manager (Sleede):** open source; cloud from €45/mo. See report 03. https://www.fab-manager.com/
- **Omnify:** "from $29", waivers, Zapier; names TinkerMill as a customer. https://www.getomnify.com/use-case/makerspace-management-software
- **Makerspace Platform:** domain does not resolve; search snippet gave Free/C$34.99/C$49.99 — unverified.
- **Facil (Becker Software, Tucson):** PEG-specific; members, equipment/facility reservations, classes, channel scheduling, payments. Licences $995–$2,695 one-time. "More than 245 organizations." Page last updated 2022-04-28. https://beckersoftware.com/facil/
- **Media Center Manager (ZeetaPro):** PEG-specific; reservations, classes, members, upload QC. Pricing not published. Customers PADNET.tv, Berkeley Community Media. http://mymediacentermanager.com/
- **Open Media Project / CiviCRM (Drupal):** dormant since ~2012; **PhillyCAM still loads `cm-contrib/reservations` and CiviCRM**. https://phillycam.org/
- **WebCheckout:** equipment checkout for higher-ed/media/makerspaces, cert restrictions; pricing not published. https://webcheckout.net/

## 3. Tool stacks seen on real makerspace sites (page code, 2026-10-12)

| Space | Stack observed |
|---|---|
| TinkerMill (Longmont, CO) | Wild Apricot + WaiverForever + Givebutter + Square/PayPal/Venmo + cash boxes + iPad kiosk |
| MakeICT (Wichita) | Wild Apricot + Smartwaiver |
| Asmbly (Austin) | Neon CRM + Skedda + Avigilon Alta Open + Calendly |
| Artisan's Asylum (MA) | Nexudus + Eventbrite |
| Pumping Station: One (Chicago) | WooCommerce + WaiverForever + own member portal (Stripe) |
| Dallas Makerspace, Seattle Makers, Sector67, Make Santa Fe | WooCommerce/Stripe on WordPress |
| Maker Nexus (CA) | Amilia |
| Generator (VT) | Glue Up + Jotform + Patreon |
| Decatur Makers | Neon CRM |
| HeatSync, Synergy Mill, Nova Labs | Zeffy |
| Lacey MakerSpace, Makerspace Charlotte | Eventbrite for classes (Lacey also on Fabman's homepage) |

Only public-page tools are visible; back-office tools are not.

## 4. What makerspaces say (short quotes)

- **TinkerMill:** dues "really only covers our entrance to the building and Slack access"; shop fees by cash/check in "blue boxes", the iPad kiosk or Venmo. https://tinkermill.org/shop-fees
- **Milwaukee Makerspace (2017, Wild Apricot):** "our cash and check processing is still manual"; "The handling of prepayments of multiple months is clunky"; door PINs and fobs synced via the API. https://groups.google.com/g/milwaukeemakerspace/c/8jG0bjm8zDo
- **r/makerspaces (2021-01-04), Texas makerspace VP:** "the cost ended up being the biggest deterrent for us"; went DIY. https://www.reddit.com/r/makerspaces/comments/kq49bl/makerspace_management_system_made_and_tested_by_a/
- **Dallas Makerspace forum:** thread on connecting membership software to door/scanner hardware (403, snippet only). https://talk.dallasmakerspace.org/t/looking-for-advice-on-digital-membership-card-software-integration-for-makerspace-systems/126034
- **MakeICT** built MESSforMakers after "fighting with" existing tools; Dallas runs home-grown MakerManager. https://wiki.dallasmakerspace.org/wiki/Software_Needs

## 5. Inferences
- Booking rules tied to both tier and certification are clearly described only by Zipper, QReserve, MakerOps and partly Fabman. The coworking platforms (Nexudus, Optix, Cobot, Proximity) have strong door access but no certification gating.
- The PEG field is poorly served: Facil unchanged since 2022, Open Media/CiviCRM dormant, stations building Neon + Booqable + MembershipWorks stacks.
- Pain points are consistent: manual cash and consumable payments, three to five tools per space, door sync via custom scripts.

## Gaps and limits
- Reddit coverage partial (429 rate limits after first query).
- Not found: Hackaday/Nation of Makers software surveys; "MakerPass"/"Fabhub"/"Makerspace CRM" products; an Alliance for Community Media vendor list (ACM: 3,000+ PEG orgs).
- smartwaiver.com and memberplanet.com returned 403 to fetch; pricing taken from raw page download and browser.
