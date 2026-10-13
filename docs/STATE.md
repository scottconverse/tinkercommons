# STATE — where TinkerCommons stands

Updated 2026-10-13 by the Fable planning session. Every build session reads this first and rewrites it
before ending. Keep it short: what is done, what is next, what is blocked, what the owner must do.

## Start here (every session)

Read in this order, then begin:
1. `docs/MASTER-SPEC.md` — canonical spec; check its Spec version line (0.2.0 as of this file)
2. `docs/IMPLEMENTATION-PLAN.md` — unit order and the rules of engagement in §0
3. `docs/research/00-SYNTHESIS.md` — why this exists
4. `docs/UNIFI-ACCESS-API.md` — first door provider, needed at v0.5

Rules that are not negotiable (from the owner, Scott Converse):
- Nothing outward without his explicit go in chat: no push, merge, tag, delete, GitHub or Stripe settings
  change, nothing touching Longmont Public Media's live systems. Local work and local commits on a unit
  branch are fine.
- Branch per unit (`unit/<n>-<slug>`), PR per unit, he merges. PR body = acceptance checklist with proof
  per line, each marked "wrote it", "ran it", or "checked it's correct".
- No LPM records anywhere: fixtures are synthetic (spec D6).
- Settled decisions go into `docs/MASTER-SPEC.md` §12/§13 the same turn, spec version bumped per its rule.
- Always version specs. Plan and protocol documents get a version line when first touched.
- Reports: plain English, TL;DR first, numbers, nothing hidden, full `C:\` paths. Anything needing his
  hands arrives as one self-contained prompt or click-path, never a shell command.
- One builder, sequential. No agent swarms. A helper agent only for a read-only cold audit.
- Use the dev-rigor-stack-lite skills at build, verify, review, merge. Audit after every unit.

## Done

- 2026-10-12: research (Proximity REA, commercial and open-source landscapes, doors/payments/migration/hosting).
- 2026-10-13: name (TinkerCommons), licence (Apache-2.0), stack (Django app + FastAPI access service,
  PostgreSQL, Redis, htmx), pilot (LPM first), builder (one Opus 5.5 medium session), no LPM data,
  door providers (UniFi Access, then Kisi, then Verkada or Genea). Spec 0.2.0 merged to main.
- Repo bootstrapped: README, LICENSE, .gitignore, docs/. **No code yet.**

## Next

**U1 Skeleton** (v0.1, Standard lane): Django project with settings split, Postgres and Redis via Compose,
health endpoint, one-command install for Ubuntu and Windows, ruff/mypy/pytest wired, GitHub Actions on
PR. First step: verify the environment table in `IMPLEMENTATION-PLAN.md` §1, report present/missing, then
propose the U1 acceptance checklist for the owner's approval before writing code.

## Blocked / waiting on owner

- Nothing for U1–U3.
- **U4 (Stripe dues)** needs a Stripe test account the project controls; the owner creates it and puts
  the test-mode secret key in the local `.env`. Ask when U4 is next, not before.

## Environment

Unverified as of this file. Dev box: Windows 11, this repo cloned at
`C:\Users\scott\Desktop\CODE\tinkercommons`. Python, Docker, uv: check before U1.
