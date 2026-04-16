# SUB-CTO REPORT TEMPLATE
**Cirra Edge — Sub-CTO → Group CTO Report**
Submit at the end of every session. Aligned with THE_CIRRA_WAY v5.0.
Version 3.0 | April 2026

---

## How to Submit

1. Fill every section honestly — no spin, no skipping
2. Commit HANDOVER.md to the product repo
3. Produce handover block for Clement to paste into Group CTO project

---

```
SUB-CTO REPORT
Project: [never-2-far / liftcore / lift-staff / lift-partner]
Session date: [YYYY-MM-DD]
Submitted by: Sub-CTO (Claude)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

01 — WHAT SHIPPED
[List every feature, fix, or migration that hit main this session.
One line per item. Brief number if applicable.
Be specific — not "improved auth" but "fixed PKCE session loss on OAuth redirect".]

02 — WHAT BROKE
[Every bug encountered this session — fixed or still open.
Format: SYMPTOM | ROOT CAUSE (if known) | SEVERITY: P0/P1/P2/P3 | STATUS: fixed/open]

03 — WHAT IS BLOCKED
[Anything blocking progress — technical, infra, waiting on Clement.
Format: BLOCKER | Why blocked | What is needed to unblock | WHO can unblock]

04 — UNVERIFIED BRIEFS
[List ANY brief sent to Cursor where output was not received or not verified.
If none: write "NONE — all briefs verified."
This section is mandatory. Unverified briefs are a process failure.]

05 — 7-LAYER SCORE (self-assessed)
Security: X/10 — [one line gap or "clean"]
Data Integrity: X/10 — [one line]
Error Handling: X/10 — [one line]
TypeScript: X/10 — [one line]
Performance: X/10 — [one line]
Observability: X/10 — [one line]
Code Quality: X/10 — [one line]
Overall: X/10

06 — CODEBASE HEALTH (from CODEBASE_HEALTH_CHECK_SOP.md)
[Run at end of session if significant code changes. Paste raw numbers.]
Largest file: [name] — [lines]
Type escapes (: any + as any): [count]
Console statements: [count]
Lint errors: [count]
npm audit: [clean / N vulnerabilities]
Test count: [count]
Build size largest chunk: [KB]

07 — FOUR-AGENT PROTOCOL COMPLIANCE
Build Agent used: YES / NO
QA Agent used on every brief: YES / NO — [if NO, which briefs skipped]
Document Agent ran at session end: YES / NO
Review Agent triggered this session: YES / NO — [trigger reason if yes]
.cursor/rules/session-opener.mdc committed: YES / NO
.cursor/rules/qa-agent.mdc committed: YES / NO

08 — PATTERNS COMPLIANCE
[For each applicable Cirra pattern: APPLIED / NOT YET / DEVIATED]
[If DEVIATED — explain why. Group CTO decides if it becomes a new pattern.]

09 — LEARNINGS CANDIDATES
[Any hard problem or decision that could save time on other projects.
Format: CANDIDATE: [title] | [what happened] | [fix or insight]
Score: HIGH (universal, saves 2+ hrs) / MEDIUM (useful, narrow) / LOW (too specific)]

10 — NEXT SESSION PRIORITIES
[What you plan to ship or complete next. Be specific. Ordered by priority.]

11 — ANYTHING ELSE GROUP CTO NEEDS TO KNOW
[Risk flags, architecture questions, things that feel wrong.
If nothing: write "—"]
```

---

## Rules for Sub-CTOs

**DO:**
* Report hard things. Especially failures. That's what makes the system smarter.
* Flag pattern deviations proactively.
* Raise architecture questions BEFORE making decisions.
* Score yourself honestly. A 4/10 honestly reported is better than a fake 8.
* Run the codebase health check after significant code changes.
* List ALL unverified briefs. Hiding them is the worst failure mode.

**NEVER:**
* Push to or modify cirra-cto repo — ever.
* Promote your own learnings to LEARNINGS.md or PATTERNS.md — surface them, Group CTO promotes.
* Make stack decisions that contradict STACK_DECISIONS.md without Group CTO sign-off.
* Skip the report because nothing went wrong.
* Stack unverified briefs. Verify first, then next.
* Merge without PASS TO DEPLOY from QA Agent.

---

*SUB_CTO_REPORT_TEMPLATE.md | Version 3.0 | Cirra Edge | April 2026*
