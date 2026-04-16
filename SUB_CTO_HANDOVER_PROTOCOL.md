# SUB-CTO HANDOVER PROTOCOL
**Version 1.0 | Cirra Edge | April 2026**
Mandatory when any sub-CTO session ends permanently or a new sub-CTO takes over.

---

## When This Applies

* A new Claude Project is created for a product (new sub-CTO)
* Clement says "hand over," "new CTO," or "CTO transfer"
* A product is being transitioned between build phases

---

## Outgoing Sub-CTO: 8 Mandatory Documents

The outgoing sub-CTO must produce ALL 8 before the handover is complete. Missing documents block the handover.

### Document 1: CLAUDE.md (updated)
* Must follow CLAUDE_md_TEMPLATE.md from cirra-cto
* All brackets filled. Zero remaining.
* Current status section reflects actual state, not planned state.
* Open issues section is complete and honest.
* Agent file ownership documented (if using 5-agent exception).

### Document 2: HANDOVER.md (final)
* Must follow HANDOVER_BLOCK_FORMAT.md from cirra-cto
* What is live and working (with URLs)
* What is broken (with severity)
* What is in progress (with brief numbers)
* What was never started (from the original plan)
* Every unverified brief listed explicitly

### Document 3: Codebase Health Check
* Run the full 10-point audit from CODEBASE_HEALTH_CHECK_SOP.md
* Paste raw command output. No cleaning, no excuses.
* Include: file sizes, dead code, type safety counts, console statements, lint errors, npm audit, build size, test count, edge function sizes

### Document 4: Production Gate Status
* Every gate from PRODUCTION_GATE_CHECKLIST.md
* Each gate marked MET or NOT MET
* Evidence for every MET gate (command output, screenshot, or URL)
* Explanation for every NOT MET gate

### Document 5: Architecture Decision Log
* Every significant decision made during tenure
* Format per decision: what was decided, date, rationale, alternatives considered, Group CTO approval status (approved / not sought / pending)
* Includes schema decisions, auth decisions, integration decisions

### Document 6: Known Debt Register
* Every known bug, shortcut, and tech debt item
* Format: description, severity (P0-P3), file/line if applicable, when introduced, why it was accepted, when it should be fixed
* Honest. If the codebase has 468 TS errors, say so.

### Document 7: Secrets and Infrastructure Map
* Every Supabase project ref and region
* Every Cloudflare Pages project and domain
* Every Sentry DSN location
* Every Resend domain
* Every API key location (which dashboard, which env var name)
* NO actual secrets in this document — only where to find them

### Document 8: Brief Tracker
* Every brief ever issued during tenure
* Status: SHIPPED (with commit hash) / NOT SHIPPED / UNVERIFIED / BROKEN / SUPERSEDED
* Any brief marked UNVERIFIED is a process failure and must be flagged prominently

---

## Incoming Sub-CTO: 4 Mandatory Steps

### Step 1: Read everything
Read ALL 8 documents from the outgoing sub-CTO before writing any code. No exceptions. Do not skim.

### Step 2: Independent health check
Run the 10-point codebase health check independently. Compare your results to the outgoing sub-CTO's numbers. Any discrepancy is a red flag — investigate before proceeding.

### Step 3: Produce Takeover Assessment
* Honest verdict: what's working, what's not
* Every claim from the outgoing handover verified or marked UNVERIFIED
* Every number verified against the actual repo (commit counts, brief counts, error counts)
* Proposed execution plan with phases
* Risk register
* Corrected CLAUDE.md if the outgoing version has errors

### Step 4: Submit for Group CTO approval
Submit the Takeover Assessment to Clement for relay to Group CTO. Wait for approval before executing any briefs.

Group CTO will issue:
* **FULL APPROVAL** — proceed with your execution plan
* **CONDITIONAL APPROVAL** — proceed with corrections required (listed)
* **BLOCKED** — specific issues must be resolved before any code runs

---

## Group CTO Role in Handover

* Reviews all 8 outgoing documents
* Runs Standards Inspector agent against the product repo
* Reviews incoming Takeover Assessment
* Cross-references against the Group-level portfolio audit
* Issues approval with corrections if needed
* Updates product-status.json in cirra-cto
* Ensures LEARNINGS.md and PATTERNS.md are updated with anything new from the outgoing tenure
* Broadcasts relevant learnings to all other sub-CTOs

---

## Standards Inspector Automated Checks

When a handover is triggered, the Standards Inspector agent checks:

* CLAUDE.md exists in repo root and matches template structure: Y/N
* HANDOVER.md committed with current date: Y/N
* Health check numbers present and recent: Y/N
* All briefs in tracker accounted for: Y/N
* No .env files in git history: Y/N
* RLS enabled on every table: Y/N
* Build passes: Y/N
* Test count greater than 0: Y/N
* No SUPABASE_ prefix in Edge Function secrets: Y/N

If any check fails, handover is BLOCKED until fixed.

---

## Timeline

| Step | Who | Time |
|------|-----|------|
| Outgoing produces 8 documents | Outgoing sub-CTO (final session) | 1-2 hours |
| Group CTO reviews + Standards Inspector runs | Group CTO via Clement | 30 minutes |
| Incoming reads all documents | Incoming sub-CTO (first session) | 30 minutes |
| Incoming produces Takeover Assessment | Incoming sub-CTO (first session) | 1-2 hours |
| Group CTO issues approval | Group CTO via Clement | 15 minutes |
| Incoming begins execution | Incoming sub-CTO (second session) | Ongoing |

Total handover time: 1-2 sessions. This is not overhead — it's insurance.

---

*SUB_CTO_HANDOVER_PROTOCOL.md | Version 1.0 | Cirra Edge | April 2026*
