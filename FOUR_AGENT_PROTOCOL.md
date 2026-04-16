# FOUR-AGENT PROTOCOL
**Version 1.0 | Cirra Edge | April 2026**
Replaces the two-agent Parallel Agent Protocol from THE_CIRRA_WAY v4.0.

---

## Overview

Every Cirra project uses four agents. Each agent has a specific role, specific file ownership, and specific triggers. Agents never mix roles. A Build Agent never audits. A QA Agent never writes code. A Document Agent never modifies source code. A Review Agent never implements fixes.

---

## Agent 1: BUILD AGENT

**Role:** Writes code. Executes briefs. Commits and pushes.

**Trigger:** Manual — Clement pastes a brief into Cursor.

**Before ANY code change:**
1. Read ALL relevant files — not just the one that looks broken
2. Grep the entire codebase for the affected field, function, or table
3. Map every file that needs changing — fix all of them in one pass
4. Check 2nd and 3rd order impacts (what calls this? what depends on this?)
5. Fill the Pre-Brief Decision Block (THE_CIRRA_WAY Section 02)

**Rules:**
* One brief at a time. Never stack unverified briefs.
* Blast radius check before writing. What else does this touch?
* Execute exactly what is specified. Never invent fields, tables, or components.
* If brief conflicts with codebase — STOP and report before proceeding.
* Architecture decisions are NOT yours to make — escalate to CTO.
* Build must pass before commit. No exceptions.
* Post the full Cursor Output Format when done (THE_CIRRA_WAY Section 14).

**Output triggers QA Agent immediately.**

---

## Agent 2: QA AGENT

**Role:** Verifies the Build Agent's work. Read-only. Never writes code.

**Trigger:** One line, pasted the moment Build Agent posts its SUMMARY:
```
QA AUDIT — run full combined audit on the brief that just completed.
Read all files in the last git commit before starting.
```

**16-Point Checklist:**

Security:
1. No service role key in frontend code
2. No PII in console.log or error messages
3. RLS on every affected table
4. No secrets in git history
5. npm audit — zero high/critical in production deps

Architecture:
6. Single Supabase client instance
7. No invented fields, tables, or components
8. No cross-portal imports
9. Types match — no new as any or : any introduced

UI Completeness:
10. Every data fetch has loading + empty + error states
11. Every form has submit spinner + error + success feedback
12. No silent failures on user actions

Data Integrity:
13. State transitions use RPC or typed enum — no raw status writes
14. Migrations use IF NOT EXISTS / IF EXISTS guards
15. Supabase .error checked before .data on every call

Verification:
16. Build passes (npm run build) + TypeScript passes (npx tsc --noEmit)

**Schema briefs additionally check:**
* Types regenerated after migration
* Rollback SQL exists
* SELECT counts run before and after

**Verdict:** PASS TO DEPLOY or BLOCKED. Nothing else.
For every BLOCKED item: exact file + line + what is wrong.
No merge without PASS TO DEPLOY. No exceptions.

---

## Agent 3: DOCUMENT AGENT

**Role:** Writes and updates .md files. Never modifies source code.

**Trigger:** End of every session, or after any brief that changes architecture.

**Rules:**
* Read the existing file before updating. Always.
* Remove stale content — never comment out, delete.
* One commit for all doc file updates.
* Files it maintains: CLAUDE.md, HANDOVER.md, ARCHITECTURE.md, CHANGELOG.md, and any other .md files in the project.
* It does NOT maintain cirra-cto files — those are Group CTO only.

**Mandatory updates at session end:**
* HANDOVER.md — what shipped, what broke, what's next, what's risky
* CLAUDE.md — open issues section, current status section
* Any doc affected by architecture changes this session

**Commit message format:** docs: [description]

---

## Agent 4: REVIEW AGENT

**Role:** Architecture health. Risk assessment. Pattern compliance. Does not implement fixes.

**Triggers (automatic, not judgment calls):**
* Before any major feature begins (new table, new portal, new integration)
* After any session with 2+ bugs in the same area
* Before any production deploy
* When requested by Group CTO

**Review scope:**
* Does the architecture still make sense?
* Are there hidden dependencies or coupling?
* Are Cirra patterns correctly applied? (check against PATTERNS.md)
* Is tech debt accumulating? (check against triggers in THE_CIRRA_WAY Section 05)
* Are there security gaps the Build + QA agents missed?
* Is the codebase health trending up or down?

**Output format:**
```
REVIEW REPORT
Project: [name]
Date: [date]
Trigger: [what triggered this review]

ARCHITECTURE HEALTH: [GREEN / AMBER / RED]
[assessment paragraph]

PATTERN COMPLIANCE: [list of patterns, APPLIED / DEVIATED / MISSING]

TECH DEBT STATUS: [any triggers firing?]

RISK ASSESSMENT: [what could go wrong in the next 2-3 sessions]

RECOMMENDATIONS: [numbered, prioritised]
```

---

## Cursor Rules Files

Every Cirra project must have these committed to the repo:

**File 1:** .cursor/rules/session-opener.mdc (alwaysApply: true)
* Copied from cirra-cto/cursor-rules-templates/session-opener.mdc
* Filled with project-specific values: Supabase ref, client path, doc paths
* Makes Agent 1 read CLAUDE.md and PATTERNS.md automatically

**File 2:** .cursor/rules/qa-agent.mdc (alwaysApply: false)
* Copied from cirra-cto/cursor-rules-templates/qa-agent.mdc
* Universal — no project-specific changes needed
* Expands the one-line QA trigger into the full 16-point audit

**Verify both are non-empty:**
```
wc -c .cursor/rules/session-opener.mdc
wc -c .cursor/rules/qa-agent.mdc
```

---

## Five-Agent Exception (LiftCore only)

LiftCore is granted an exception to run two Build Agents due to codebase size. Conditions:

* Build 1 and Build 2 have clearly separated file ownership documented in CLAUDE.md
* Build 2 NEVER touches Build 1's files without Group CTO approval
* If ownership overlaps, consolidate back to four agents immediately
* Exception reviewed at every SOP revision

No other product may use five agents without explicit Group CTO approval.

---

*FOUR_AGENT_PROTOCOL.md | Version 1.0 | Cirra Edge | April 2026*
