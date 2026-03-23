# GROUP CTO INTAKE SOP
**Cirra Edge — Processing Sub-CTO Reports and Updating the Playbook**
Version 1.0 | March 2026

## Overview

Every 3-5 days, three reports come in. Your job:
1. Extract what is global (goes into cirra-cto)
2. Flag what is local (goes back to the sub-CTO as guidance)
3. Update docs
4. Broadcast updates to sub-CTOs

## Step 1 — Receive and Triage Reports

Paste all three sub-CTO reports into your Group CTO Claude session. Then run:

GROUP CTO INTAKE PROMPT:

I have received sub-CTO reports from: [never-2-far / liftcore / lift-staff]
Period: [dates]

[PASTE ALL THREE REPORTS]

As the Cirra Group CTO:

1. GLOBAL vs LOCAL SPLIT
   For every item, classify:
   - GLOBAL: pattern, learning, or decision that applies across all projects
   - LOCAL: project-specific issue that stays with that sub-CTO

2. RISK FLAGS
   Any security, data integrity, or architectural risk needing immediate attention?
   Score severity: CRITICAL / HIGH / MEDIUM / LOW

3. PATTERN COMPLIANCE GAPS
   Any sub-CTO reporting DEVIATED on a Cirra pattern?
   Is the deviation justified (new pattern candidate) or a correction needed?

4. PLAYBOOK UPDATE CANDIDATES
   Score each LEARNINGS and PATTERN candidate: HIGH / MEDIUM / LOW

5. CROSS-PROJECT SIGNALS
   Any problem appearing in 2+ projects? Flag explicitly — highest promotion priority.

Output as structured sections. One recommendation per issue.

## Step 2 — Update cirra-cto Repo

Priority order:
1. Security or data integrity risk — update immediately
2. Cross-project problem (2+ projects hit it) — LEARNINGS.md + PATTERNS.md
3. New pattern (solved same class twice) — add to PATTERNS.md with code example
4. Single-project learning (HIGH) — add to LEARNINGS.md
5. Stack decision made — add to STACK_DECISIONS.md
6. Prompt update needed — update PROMPTS.md
7. THE_CIRRA_WAY.md change — update with version bump

Commit format: docs: [file] — [short description of what changed and why]
Never batch multiple concerns into one commit.

## Step 3 — Broadcast to Sub-CTOs

After committing, send this to each sub-CTO Claude Project:

CIRRA PLAYBOOK UPDATE — [YYYY-MM-DD]

FILES UPDATED: [list]
KEY CHANGES: [one line per change]
ACTION REQUIRED: [patterns to implement, corrections to deviations]
LOCAL FEEDBACK FOR [PROJECT]: [project-specific guidance]

## Step 4 — Rules

NEVER accept a report that is:
- Missing any of the 9 sections
- Self-scoring above 7/10 with no supporting evidence
- Reporting zero blockers when the project is visibly stuck

NEVER promote a pattern without:
- Evidence it solved the same problem in 2+ projects, OR saved 2+ hours in one incident
- A code example
- Failure modes documented

NEVER let a cross-project signal stay local. If two sub-CTOs hit the same problem, it goes global.

*GROUP_CTO_INTAKE_SOP.md | Cirra Edge | Version 1.0 | March 2026*
