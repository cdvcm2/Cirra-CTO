# SUB_CTO_ONBOARDING.md — Day 1 (LIFT Mobility Core)

**Cirra Edge | Locked 2026-05-03**  
**Purpose:** Canonical tactical onboarding for incoming sub-CTOs. Project Instructions in Claude UI stay lean (~500 tokens) and **point here** for facts that would otherwise drift.

---

## 1. Day 1 Protocol

1. `bash scripts/sub-cto-bootstrap.sh > /tmp/boot.txt && cat /tmp/boot.txt`
2. `bash scripts/handover-snapshot.sh > /tmp/snap.txt && cat /tmp/snap.txt`
3. Compare snapshot **HEAD** (section **REPO STATE LOCK**) to **closing SHA** in latest `HANDOVER.md`.
4. Produce **Takeover Assessment** per `cirra-playbook/SUB_CTO_HANDOVER_PROTOCOL.md`.
5. Submit to Clement for Group CTO relay. Wait for **FULL** or **CONDITIONAL** approval before dispatching any briefs.

---

## 2. Reading Order (Day 1, ~2 hours)

1. `/HANDOVER.md`
2. `/CLAUDE.md`
3. `/docs/handover/HANDOVER_<latest>.md`
4. `/STATE_MACHINES.md`
5. `/TECH_DEBT.md`
6. `/docs/DECISIONS_LOG.md`
7. `/docs/handover/BRIEF_TRACKER.md`
8. `cirra-playbook/SUB_CTO_HANDOVER_PROTOCOL.md`
9. `cirra-playbook/HANDOVER_PROMPT.md`
10. `cirra-playbook/THE_CIRRA_WAY.md`

---

## 3. Lane Assignments (brief number blocks)

| Range | Theme |
|-------|--------|
| **700–739** | Pre-launch demo polish (LIFT-700 series, closed) |
| **740–799** | Table primitives (A2 lane, closed) |
| **800–819** | Handover state machine (A5 lane) |
| **820–849** | Feature 3 continuation (passenger pause / skip / resume) |
| **850–899** | Driver / vehicle POV (A1 lane) |
| **870–879** | Templates feature (closed) |
| **900–999** | Contracts / billing (A7 lane) |

Always run `scripts/next-brief-number.sh <lane>` before drafting any brief. Echo **`BRIEF NUMBER VERIFIED FREE: LIFT-NNN`** at the top of the brief.

---

## 4. Engineering Hard Rules (pointers, not copy)

- `cirra-playbook/THE_CIRRA_WAY.md` — **§05** tech debt / line gates, **§02** Pre-Brief Decision Block, **§13** evidence discipline, **§14.1** Universal Output Rule (one block for all relay pastes).
- Project-specific **NEVER** list: repo root **`CLAUDE.md`** (Permanent Cursor Rules / NEVER section).

---

## 5. Pre-Launch Smoke Path

**Demo pages:** Plan, Live, Passengers, Customers, Fleet, Drivers, Vehicles list.  
**Settings / Simulate / Planner** are post-launch unless a change there breaks a demo page.

---

## 6. Session End Protocol §09

All **five** protocol docs touched on close — not only `CLAUDE.md`:

`HANDOVER.md`, `docs/handover/BRIEF_TRACKER.md`, `docs/DECISIONS_LOG.md`, `TECH_DEBT.md`, `STATE_MACHINES.md`.  
Plus **`CLAUDE.md`** when doctrine or pointers change.

`scripts/handover-snapshot.sh` **§25–§27** gates surface drift (HANDOVER vs code; five-doc set; CLAUDE `LAST-SYNCED-SHA`). **Do not override gates — fix docs.**

---

## 7. Three Trigger Phrases (Clement)

| Phrase | Action |
|--------|--------|
| **`/handover`** | Run flow in `cirra-playbook/HANDOVER_PROMPT.md` |
| **`REDO IN ONE BLOCK`** | Re-output last relay per **§14.1**; no apology preamble |
| **`update all .mds`** | §09 five-doc commit (plus `CLAUDE.md` if doctrine moved) |

---

## 8. Session Capacity

Propose **`/handover`** at **~75%** of the token window. Use **token math**, not message count. Sub-CTO rotation is normal ops — see **D-29-09** in repo `docs/DECISIONS_LOG.md`.

---

*SUB_CTO_ONBOARDING | LIFT Mobility Core | Cirra Edge*
