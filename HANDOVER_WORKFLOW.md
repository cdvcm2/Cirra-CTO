# HANDOVER WORKFLOW
## How context is managed across all Cirra Edge Claude sessions

---

## THE THREE LAYERS

### Layer 1 — Group CTO/CPO (this repo)
Handover document: MASTER_CONTEXT.md
Session start: Upload 7 files + paste MASTER_CONTEXT.md
Session end: Update MASTER_CONTEXT.md if any decision 
was made, append DECISIONS_LOG.md, commit and push
Cadence: As needed — triggered by decisions, not schedule

### Layer 2 — Sub-CTO per project
Handover document: CLAUDE.md in each project repo
Session start: Upload CLAUDE.md + THE_CIRRA_WAY.md + 
PATTERNS.md + paste any relevant directives from Group CTO
Session end: Sub-CTO updates CLAUDE.md with decisions,
new patterns, and open issues. Commits to project repo.
Cadence: Every build session

### Layer 3 — Sub-CTO reports up
Format: SUB_CTO_REPORT_TEMPLATE.md (9 sections)
Frequency: Every 3-5 days
Recipient: Clement copy-pastes to Group CTO chat
Group CTO processes via GROUP_CTO_INTAKE_SOP.md

---

## THE RELAY (CLEMENT'S JOB)

You are the bridge between all layers.
Directives flow DOWN (Group → Sub-CTO).
Reports flow UP (Sub-CTO → Group).
You copy-paste. You do not summarise or interpret.
Paste the exact output from one chat into the next.

---

## DEGRADATION DETECTION

Hard limit: 40-50 exchanges per chat.
Degradation signals (in order of severity):
1. Claude asks a question it already answered
2. Claude ignores project-specific rules
3. Claude gives generic instead of portfolio-specific advice
4. Responses get longer and vaguer
5. Claude contradicts an earlier decision without flagging it

Quick check — paste this into any suspect chat:
"What is the naming convention for customer-facing records 
and what does Stanley's access look like?"
Wrong or vague answer = degraded = start fresh.

---

## END OF SESSION CHECKLIST

### Group CTO/CPO session:
- [ ] MASTER_CONTEXT.md updated (if trigger fired)
- [ ] DECISIONS_LOG.md appended
- [ ] Both committed and pushed to cdvcm2/Cirra-CTO
- [ ] Fresh chat ready for next session

### Sub-CTO session:
- [ ] CLAUDE.md updated with decisions + open issues
- [ ] New patterns appended to CLAUDE.md patterns section
- [ ] Committed and pushed to project repo
- [ ] Handover summary ready for next session start

---

## WHAT GOES WHERE

| Decision type | Document |
|---|---|
| Group architecture, pivots, deadlines | MASTER_CONTEXT.md |
| Group decision history | DECISIONS_LOG.md |
| Project-level technical decisions | Project CLAUDE.md |
| Reusable engineering patterns | PATTERNS.md |
| Cross-project failure lessons | LEARNINGS.md |
| Architecture decision rationale | STACK_DECISIONS.md |

---

## BROADCASTING TO SUB-CTOS

When Group CTO issues a directive affecting all projects,
paste it into each sub-CTO chat separately.
Clement is the relay — never assume sub-CTOs 
share context with each other.

Template for broadcasting handover workflow:

FROM: Group CTO
TO: [Project] CTO
DATE: [date]

DIRECTIVE: Adopt handover workflow standards.

Effective immediately your session end checklist is:
1. Update CLAUDE.md with all decisions and open issues
2. Append new patterns to CLAUDE.md patterns section  
3. Commit and push to project repo
4. Produce a 5-line handover summary:
   - Last completed task
   - Current open issues
   - Next action
   - Any blockers
   - Context Clement needs for next session

This summary goes to Clement at end of every session.
Clement pastes it into the next session as context.
