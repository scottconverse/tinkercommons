# TinkerCommons

Open-source management for shared workshops: makerspaces, hackerspaces, community media centers,
public-access TV stations, tool libraries, and any member-run building full of equipment.

One system instead of six. A person joins, signs the waiver, takes the orientation, earns a badge for
the laser or the TV studio, and from then on their fob or phone opens the door and powers the machine
only while they are paid up and certified. They book the shop or the studio on their phone under the
rules their tier allows. Every dollar, whether dues, laser minutes, clay, a class, a donation, or cash in
the box, lands in one ledger that reconciles to Stripe. Staff see who is active, who is overdue, what is
used, and what each shop earns.

**Status:** pre-alpha. Research and specification only. No runnable code yet. First target is v0.1
(members, tiers, dues, roles, export, one-command install); see the version ladder in the spec.

## Documents

- [docs/MASTER-SPEC.md](docs/MASTER-SPEC.md) — the canonical specification and version ladder, v0.1 through v3.0
- [docs/plans/v0.1-plan.md](docs/plans/v0.1-plan.md) — the plan for the first increment
- [docs/research/00-SYNTHESIS.md](docs/research/00-SYNTHESIS.md) — why this exists: the market, what exists, what does not
- `docs/research/01` to `04` — the underlying research on Proximity, commercial tools, open-source tools, door hardware, payments, migration and hosting

## Principles

1. One record per member, one ledger per space, one login.
2. Rules are data, never code paths.
3. Access follows state: paid up, waivered, badged, or the door stays shut.
4. No card numbers, ever. Stripe holds them.
5. Full export from day one. We are not the lock-in we replace.
6. Self-host in one command. Hosted is optional, never required.
7. Hardware is a plug-in behind a published protocol.
8. Every version ships with proof on real data at a real site.

## Stack

Python / Django, PostgreSQL, Redis, Stripe. Docker Compose install. Responsive web portal; no native apps
in 1.0.

## Licence

Apache-2.0. See [LICENSE](LICENSE).

## Reference sites

TinkerMill and Longmont Public Media, both 501(c)(3) nonprofits in Longmont, Colorado.
