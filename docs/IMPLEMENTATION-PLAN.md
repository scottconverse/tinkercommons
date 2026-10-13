# TinkerCommons — implementation plan

Written 2026-10-13 by Fable, for the single Opus 5.5 (medium) session that will build it. Read with
`MASTER-SPEC.md`, which is the source of truth for scope; this file is the order of work and the rules
of engagement. When the two disagree, the spec wins and this file gets fixed.

## 0. How the work runs

- **One builder, sequential.** One Claude session on Opus 5.5 at medium effort does design, code, tests,
  review and docs, one unit at a time. No subagent swarms. A single helper agent is allowed only for an
  independent read-only task (a cold audit of a finished unit, a research lookup), never for parallel coding.
- **Nothing outward without approval.** Creating repos, pushing, merging, tagging, deleting, changing
  GitHub or Stripe settings, touching LPM's live systems: Scott says go first, every time. Local edits,
  local commits on a unit branch, and tests are free.
- **Branch per unit, PR per unit, Scott merges.** Branch name `unit/<number>-<slug>`. The PR body
  carries the acceptance checklist with each line ticked and the command plus output that proved it.
- **Lane per unit.** Standard by default. Critical when the unit touches money, access control,
  persisted member data, deletion, migration or install. Critical units get a cold audit by a fresh
  read-only agent before the PR, and the audit's findings are fixed or logged in the PR.
- **Proof language.** "wrote it" ≠ "ran it" ≠ "checked it's correct." Every PR says which of the three
  it reached for each acceptance line. A test that has never been seen failing is not evidence.
- **Reports to Scott.** Plain English, TL;DR first, what passed and failed with numbers, nothing hidden,
  full `C:\` paths for any file he might open. A report that needs his hands includes one self-contained
  prompt or click-path, never a shell command.
- **Durable before chat.** Every settled decision goes into `MASTER-SPEC.md` §12/§13 the same turn it is
  made. Session state that a future session needs goes into `docs/STATE.md` in the repo before it is said
  in chat.
- **Dev discipline.** `dev-rigor-stack-lite` skills at build, verify, review and merge steps; audit after
  every unit; walkthrough on every screen before a version is called done.

## 1. Environment (once, before unit 1)

| Need | Who | Status |
|---|---|---|
| Python 3.12+ on the dev box | builder, no admin needed (`uv` or scoop) | verify |
| Docker Desktop or Podman for Postgres and Redis | already on this box for CivicCast | verify |
| `uv` for dependency management, `ruff`, `pytest`, `pytest-django`, `mypy` | builder | install |
| Stripe test account the project controls | **Scott**: create at stripe.com with a project email, then paste the test-mode secret key into `.env` locally. Never into chat, never into the repo | owner's hands |
| LPM seed data | already in `docs/research/proximity-lpm-*.json` | done |
| Clean Ubuntu VM or Windows Sandbox for install proof | builder, on this box | verify |

Repo layout, fixed at unit 1 so later units do not reshuffle:

```
tinkercommons/
  pyproject.toml            # uv-managed; one project, two entry points
  compose.yaml              # postgres, redis, web, access, worker
  Dockerfile
  install.sh / install.ps1  # the one command
  tinkercommons/            # Django project
    settings/ (base, dev, prod)
    core/        # Space, Person, roles, audit log
    members/     # MembershipClass, Membership, Credential
    billing/     # Stripe, charges, invoices, offline payments
    documents/   # v0.2
    badges/      # v0.2
    resources/   # v0.3 resources, booking rules, bookings
    classes/     # v0.6
    reports/     # v0.7
    exports/     # CSV/JSON export, importers
    portal/      # member-facing views and templates
    staff/       # staff views and templates
  access/                   # FastAPI service, imports tinkercommons models
  firmware/                 # v0.5 reference ESP32 (separate build)
  docs/
    MASTER-SPEC.md, STATE.md, plans/, research/, adr/
  tests/
```

## 2. v0.1 Foundation — units in order

Each unit: scope, files, tests, proof, lane. Estimates are session-turns of focused work, not hours.

**U1 Skeleton** (Standard, ~1 session)
Django project with `settings/` split, Postgres and Redis via Compose, health endpoint, `install.sh`
that brings it up on a clean Ubuntu box and `install.ps1` for Windows, `ruff`/`mypy`/`pytest` wired,
GitHub Actions running tests on PR. *Proof:* fresh VM, one command, login page in a browser;
screenshot in PR.

**U2 Core models** (Critical: persisted member data, ~2 sessions)
`Space`, `Person`, `Role` with scopes (owner, staff, captain-per-shop, member, board), `AuditLog`.
`MembershipClass` with the full field list from spec §5, `Membership`, `Credential` (record only).
Migrations. Django admin registered for all. A management command that loads LPM's 17 classes from
`docs/research/proximity-lpm-space-config.json`. *Proof:* `loaddata`-style command runs; admin lists 17
classes with the right prices; model tests for every constraint; a test proves audit coverage fails
when a new model is added without it.

**U3 Auth and roles** (Critical: access to data, ~1 session)
Email+password login, password strength, 2FA optional (required for owner/staff from v0.8), role
checks as reusable decorators, scoped captain permissions enforced in queries not templates.
*Proof:* permission matrix test: every role × every view = expected allow/deny.

**U4 Stripe dues** (Critical: money, ~3 sessions)
Stripe Connect onboarding for the space; customer per person; subscription per membership; webhook
endpoint with signature check and idempotent event handling; grace period; overdue → inactive state flip
and back; Stripe test clocks in the test suite. *Proof:* 50 synthetic members advanced one month on a
test clock; invoice count, amounts and membership states match Stripe to the cent; webhook replay test;
cold audit before PR.

**U5 Offline payments and the ledger core** (Critical: money, ~1 session)
`Charge` with the type enum from spec §5, `Invoice`, offline payment record (cash/check/Venmo) by staff
with audit. Reconciliation report: Stripe + offline = ledger. *Proof:* reconciliation test on U4's data
plus offline entries.

**U6 Export** (Critical: migration, ~1 session)
Owner-only streamed CSV and JSON of every table, manifest with row counts and schema version, and a
`reimport` command. *Proof:* export → fresh install → reimport → identical row counts and a sample of
row hashes.

**U7 Backup and restore** (Critical: persisted data, ~1 session)
`backup` command (pg_dump + media, encrypted, to local path or S3-compatible target), `restore`
command, nightly schedule in Compose. *Proof:* restore on a fresh machine timed under 15 minutes,
timings in the PR.

**U8 Screens** (Standard, ~2 sessions)
Staff: member list with search, member detail, membership change, record offline payment, audit view.
Member: my membership, next charge, payment method via Stripe hosted page. Django templates + htmx,
responsive. *Proof:* walkthrough skill on every screen, phone width and desktop, defects fixed.

**U9 Version close** (Critical, ~1 session)
`docs/STATE.md` updated, CHANGELOG, install docs, the v0.1 acceptance list from the spec ticked with
evidence, cold audit of the whole, tag proposal to Scott. Scott tags.

Total: roughly 13 focused sessions for v0.1.

## 3. v0.2 → v0.9, in order, one line each (detail is written at the start of each version)

- **v0.2 Documents and badges.** Versioned documents, in-app signing with stored PDF (WeasyPrint),
  signature audit, `Badge`, `BadgeGrant` with expiry, required-documents and grants-badges on classes,
  "cannot activate without signing." ~6 sessions.
- **v0.3 Resources and bookings.** `Resource`, `BookingRule` seeded from `proximity-lpm-resources.json`
  with `required_badges` added, `Booking`, recurring patterns, approval, included-minutes accounting,
  member calendar (FullCalendar or similar, phone-first). ~8 sessions. Critical for the rule engine.
- **v0.4 Ledger and payments.** Full charge types, taxes, credits and deposits, Stripe meters for metered
  use, Stripe Terminal reader flow, monthly statement. ~6 sessions. Critical.
- **v0.5 Access.** Protocol spec published under `docs/protocol/` (Apache-2.0), FastAPI `access/`
  service sharing models, `AccessDevice`, `AccessEvent`, `ToolSession`, offline cache tokens, MQTT
  broker in Compose, reference ESP32 firmware (ESPHome-based first, custom later), Kisi and UniFi
  adapters, overdue-revokes-within-a-minute. 30-day live run on one LPM door and one tool. ~10 sessions.
  Critical, proof gate.
- **v0.6 Classes and events.** `Class`, `Session`, `Enrollment`, capacity, member pricing, payment via
  ledger, attendance, grants-badge; orientation as a class. ~5 sessions.
- **v0.7 Reports, notices, importers, API.** Reports, transactional email with templates, Slack/Discord
  webhooks, Wild Apricot importer via API, CSV importer with diff report, public JSON API with personal
  access tokens, outbound webhooks. ~6 sessions.
- **v0.8 Hardening.** Privacy (retention, self-delete), 2FA required for staff, rate limits, upgrade
  path tested across three versions, security review, accessibility pass. ~5 sessions. Critical.
- **v0.9 Pilot at LPM.** Parallel run for one billing cycle, old tools read-only, newcomer walkthrough,
  defects only. Scott signs the pilot report. ~ one calendar month, low session load.
- **v1.0.** Docs, installer, migration guide, release. Scott tags.

## 4. What Scott does (and nothing else)

1. Create the Stripe test account and put the key in the local `.env` (one-time, before U4).
2. Merge PRs after reading the acceptance checklist. Reject any PR whose proof line says "wrote it" where
   it should say "ran it."
3. Approve anything outward: pushes, tags, LPM-facing changes, hardware on an LPM door.
4. Tag v0.1 and every version after.
5. Decide the questions that come up that the spec does not answer; the builder records them in §12.

## 5. Risks, named now

- **Stripe test clocks and Connect** are the most likely place for a confident-but-wrong "done." U4 gets
  the cold audit and a replay test, no exceptions.
- **Access hardware** (v0.5) is where software people underestimate. Budget the 30-day live run as real
  time, not a checkbox.
- **Scope creep from LPM's real needs** during pilot. Defects fix; features go to the next version.
- **Single-builder continuity.** `docs/STATE.md` is the handoff; every session ends by updating it.

## 6. First message for the Opus session

See `OPUS-KICKOFF-PROMPT.md` in this folder.
