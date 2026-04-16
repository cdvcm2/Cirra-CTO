# GROUP CTO INTAKE SOP
**Cirra Edge — Processing Sub-CTO Reports and Managing Handovers**
Version 3.0 | April 2026

---

## Overview

Reports come in from sub-CTOs at the end of every session via Clement. Your job:
1. Extract what's global (goes into cirra-cto)
2. Flag what's local (goes back to the sub-CTO as guidance)
3. Update docs
4. Issue directives
5. Manage handovers when sub-CTOs transfer

---

## Step 1 — Receive and Triage

When Clement pastes a sub-CTO report or handover:

**Immediate checks:**
* Are all 11 sections of SUB_CTO_REPORT_TEMPLATE v3.0 filled?
* Section 04 (Unverified Briefs) — any unverified? If yes, BLOCK new briefs until verified.
* Section 07 (Four-Agent Protocol) — all agents used? If not, flag immediately.
* Section 06 (Codebase Health) — numbers trending up or down from last report?

**Classification per item:**
* GLOBAL: pattern, learning, or decision that applies across all projects
* LOCAL: project-specific issue that stays with that sub-CTO
* RISK: security, data integrity, or architectural risk needing immediate attention
* CROSS-PROJECT: same problem appearing in 2+ projects — highest promotion priority

---

## Step 2 — Update cirra-cto

Priority order:

| Priority | Category | Action |
|----------|----------|--------|
| 1 | Security or data integrity risk | Update PATTERNS.md or LEARNINGS.md immediately |
| 2 | Cross-project problem (2+ projects) | LEARNINGS.md + consider PATTERNS.md promotion |
| 3 | New pattern (solved same problem twice) | Add to PATTERNS.md with code example |
| 4 | Single-project learning (HIGH) | Add to LEARNINGS.md |
| 5 | Stack decision | Add to STACK_DECISIONS.md |
| 6 | Process improvement | Update THE_CIRRA_WAY.md with version bump |

For each update:
* Check for conflicts with existing entries
* One commit per logical change
* Commit message: docs: [file] — [description]

---

## Step 3 — Issue Directives

After processing, issue directives as clean copy-paste blocks:

```
FROM: Group CTO/CPO
TO: [Sub-CTO name] (via Clement)
DATE: [date]
RE: [subject]

[directive content — one instruction per code block]
```

Directive types:
* CORRECTION — something the sub-CTO must fix
* APPROVAL — architecture decision approved
* BLOCKED — work cannot proceed until condition met
* INFORMATION — update the sub-CTO needs

---

## Step 4 — Update Product Status

After every intake, produce a JSON patch for product-status.json:

```
STATUS UPDATE — paste to Cursor Governance Writer:
Update status/product-status.json:
[product].score = X.X
[product].last_session = "YYYY-MM-DD"
[product].gates.[gate_name] = true/false
[product].blockers = ["item1", "item2"]
[product].code_health.largest_file = NNNN
[product].compliance.pdpa_doc = "status"
```

---

## Step 5 — Handover Management

When a sub-CTO transfer is triggered, follow SUB_CTO_HANDOVER_PROTOCOL.md:

1. Ensure outgoing sub-CTO produced all 8 mandatory documents
2. Run Standards Inspector against the product repo
3. Review incoming sub-CTO's Takeover Assessment
4. Issue CONDITIONAL or FULL approval
5. Update cirra-cto with any learnings from the outgoing tenure
6. Broadcast relevant updates to all other sub-CTOs

---

## Rules

**NEVER accept a report that:**
* Is missing any of the 11 sections (v3.0)
* Self-scores above 7/10 with no supporting evidence
* Reports zero blockers when the project is stuck
* Has unverified briefs without listing them in Section 04
* Claims Four-Agent Protocol compliance without .cursor/rules/ committed

**NEVER promote a pattern without:**
* Evidence it solved the same problem in 2+ projects, OR saved 2+ hours once
* A code example (for engineering patterns)
* Failure modes documented

**ALWAYS:**
* Check Four-Agent Protocol compliance in every report
* Flag unverified briefs immediately — this is the #1 process failure mode
* Cross-reference codebase health numbers against previous reports — are they trending right?

---

*GROUP_CTO_INTAKE_SOP.md | Version 3.0 | Cirra Edge | April 2026*
