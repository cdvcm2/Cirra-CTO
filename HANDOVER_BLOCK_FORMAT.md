# HANDOVER BLOCK FORMAT
This is the exact block every sub-CTO produces at the 
end of every session. No variations. No interpretation.
Clement copies this entire block and pastes it as the 
first message of the next chat. That is the handover.

HOW IT WORKS:
- Outgoing CTO produces this block as their last message
- Clement copies it exactly — no editing
- Clement opens new chat, uploads 4 files, pastes block
- Incoming CTO reads everything and confirms ready
- Done. No back and forth. No missing context.

THE 4 FILES CLEMENT UPLOADS EVERY NEW SESSION:
1. CLAUDE.md (project repo root)
2. HANDOVER.md (project repo root)
3. THE_CIRRA_WAY.md (~/Code/cirra-cto)
4. PATTERNS.md (~/Code/cirra-cto)

THE HANDOVER BLOCK (copy this structure exactly):

---
DATE: [DD Month YYYY]
TITLE: [Project Name] — Session Handover
TO: [Project Name] CTO (incoming session)
FROM: [Project Name] CTO (outgoing session)
CONTEXT: [one line — exact state of project right now]
---

RESUME IMMEDIATELY — FIRST TASK:
[exact task, brief reference if applicable,
zero ambiguity, one action only]

COMPLETED THIS SESSION:
- [item 1]
- [item 2]
- [item 3]

BROKEN RIGHT NOW:
- [issue — P0/P1/P2]
- [NONE if nothing broken]

BRIEF QUEUE (in order):
- [Brief XXX — title — status]

LAST COMMIT: [hash] — [message]
BRANCH: [branch name]
BUILD STATUS: [green / failing]
SCORE: [X/10]

HARD RULES — NEVER VIOLATE:
- [rule 1]
- [rule 2]
- [rule 3]

OPEN ITEMS FOR GROUP CTO:
- [item needing escalation]
- [NONE if nothing pending]

CURSOR OUTPUT FORMAT REMINDER:
Every Cursor task must return output in this format:
SUMMARY / TASK / FILES CREATED / FILES MODIFIED /
FILES DELETED / CHECKS RUN / SCHEMA CHANGES /
MIGRATION APPLIED / COMMITTED / PUSHED /
MERGE CONFLICTS / BUILD STATUS / OPEN ISSUES
If Cursor output is missing this format, reject it
and ask Cursor to re-run with the format applied.
---

INCOMING CTO FIRST RESPONSE FORMAT:
Confirm handover received with:
1. Score: [X/10]
2. First task: [exact action]
3. Blockers: [or NONE]
4. One question only: "Ready — confirm to proceed."

Do not touch any code until Clement replies: "Go."

---
