# SUB-CTO REPORT TEMPLATE
**Cirra Edge — Sub-CTO to Group CTO Report**
Submit every 3-5 days. Aligned with the capture ritual.

## How to Submit

1. Paste this template into your Claude Project (with CTO active)
2. Fill every section honestly
3. Send to Group CTO channel

---

SUB-CTO REPORT
Project: [never-2-far / liftcore / lift-staff]
Period: [YYYY-MM-DD] to [YYYY-MM-DD]

01 — WHAT SHIPPED
[List every feature, fix, or migration that hit main or staging this period. One line per item.]

02 — WHAT BROKE
[Every bug encountered. Format: SYMPTOM | ROOT CAUSE | STATUS: fixed/open/investigating]

03 — WHAT IS BLOCKED
[Format: BLOCKER | Why blocked | What is needed to unblock]

04 — 7-LAYER SCORE (self-assessed)
Security: X/10 — [one line gap or clean]
Data Integrity: X/10 — [one line gap or clean]
Error Handling: X/10 — [one line gap or clean]
TypeScript: X/10 — [one line gap or clean]
Performance: X/10 — [one line gap or clean]
Observability: X/10 — [one line gap or clean]
Code Quality: X/10 — [one line gap or clean]
Overall: X/10

05 — PATTERNS COMPLIANCE
[For each Cirra pattern applicable to your stack: APPLIED / NOT YET / DEVIATED]
[If DEVIATED — explain why.]

06 — LEARNINGS CANDIDATES
[Format: CANDIDATE: [title] | [what happened] | [fix or insight]]
[Score: HIGH = universal, saves 2+ hrs | MEDIUM = useful but narrow | LOW = too specific]

07 — PATTERN PROMOTION CANDIDATES
[Any solution that solved a problem twice or belongs in PATTERNS.md. Group CTO decides.]

08 — NEXT 3-5 DAYS
[What you plan to ship or complete. Be specific.]

09 — ANYTHING ELSE GROUP CTO NEEDS TO KNOW
[Risk flags, architecture questions, things that feel wrong. If nothing: write —]

---

## Rules for Sub-CTOs

DO:
- Report hard things. Especially failures.
- Flag pattern deviations proactively.
- Raise architecture questions before deciding, not after.
- Score yourself honestly.

NEVER:
- Push to or modify cirra-cto repo.
- Promote your own learnings to LEARNINGS.md or PATTERNS.md.
- Make stack decisions that contradict STACK_DECISIONS.md without Group CTO sign-off.
- Skip the report because nothing went wrong.

*SUB_CTO_REPORT_TEMPLATE.md | Cirra Edge | Version 1.0 | March 2026*
