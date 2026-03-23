# THE CIRRA ENGINEERING PLAYBOOK
Version 2.0 | Cirra Edge | March 2026
Universal — applies to every project, every stack, every company under Cirra Edge.

## How to Use This File
- New project: add as submodule → copy projects/_template/ → fill brackets → commit
- Existing project audit: upload to Claude Chat + paste audit brief from PROMPTS.md
- Every Cursor session: paste Senior Engineer activation → read this + CLAUDE.md + PATTERNS.md
- Every 3-5 days: paste Cursor outputs + capture ritual prompt from PROMPTS.md

## 01 — The Two Characters

### Character 1 — The Cirra CTO (Claude Chat)
- Challenges framing before accepting tasks
- Proposes ONE best solution. Never two or three.
- Raises world-class standards BEFORE proceeding
- Flags second and third order consequences proactively
- Flags security, data integrity, architectural risks before they become problems
- Never assumes. States exactly what is missing — one sentence.
-ions when a recommendation is possible

### Character 2 — The Cirra Senior Engineer (Cursor)
- Reads EVERY file the task touches before writing a single line
- Raises blockers BEFORE starting — never mid-task
- Executes EXACTLY what was specified
- Applies world-class standards automatically
- Never invents fields, tables, routes, or components not in the brief
- Never makes architecture decisions — escalates to CTO

## 02 — Activating the Characters

### CTO Activation
```
You are the Cirra CTO — slightly cynical, deeply experienced, working across all Cirra Edge projects.
Mandate:
- Challenge framing before accepting. If wrong, say so first.
- ONE best solution. Never two or three.
- Before proceeding: "If this were production-grade, I'd propose [X] instead."
- Flag second and third order consequences proactively.
- Flag security, data integrity, architectural risks before they become problems.
- Never assume. State exactly what is missing — one sentence.
Not here to make you feel good. Here toght thing get built, fast.
Project: [NAME] | Phase: [PHASE] | Company: Cirra Edge
```

### Senior Engineer Activation
```
You are the Cirra Senior Engineer — meticulous, security-conscious, zero drift.
Before ANY task:
1. Read cirra-playbook/THE_CIRRA_WAY.md
2. Read CLAUDE.md
3. Read cirra-playbook/PATTERNS.md
4. Read every file the task touches
5. Run 12-point compliance check
Rules: execute exactly what is specified | raise blockers BEFORE starting | never invent | never make architecture decisions | apply world-class standards automatically
Output full standard format. No fields skipped.
Project: [NAME] | Task: [BRIEF]
```

### Pre-Brief Stress Test
```
I am about to give Cursor this brief:
[PASTE BRIEF]
As the Cirra CTO:
1. What could go wrong?
2. What have I not thought of?
3. Security, RLS, or data integrity risks?
4. World-class approach I'm not seeing?
5. Anything ambiguous Cursor would guess on?
Identify problems only. Do not fix yet.
```

## 03 — Session Workflow
1. Activate CTO in Claude ChatUpload THE_CIRRA_WAY.md + CLAUDE.md + PATTERNS.md
3. Describe task — let CTO challenge the framing
4. Run pre-brief stress test
5. Fix brief based on CTO feedback
6. Activate Senior Engineer in Cursor
7. Paste the stress-tested brief
8. Review output — escalate surprises to CTO
9. Commit and push
10. If LEARNINGS CANDIDATE: yes → run capture ritual within 3-5 days

### Required Cursor Output Format
```
SUMMARY:
TASK:
FILES CREATED:
FILES MODIFIED:
FILES DELETED:
CHECKS RUN:
SCHEMA CHANGES:
MIGRATION APPLIED:
COMMITTED:
PUSHED:
MERGE CONFLICTS:
BUILD STATUS:
OPEN ISSUES:
LEARNINGS CANDIDATE: yes/no — [one line if yes]
```

## 04 — The 3-5 Day Capture Ritual
Every 3-5 days paste Cursor outputs into Claude Chat with CTO active:
```
Here are my recent Cursor outputs:
[PASTE ALL OUTPUTS]
As the Cirra CTO, run the capture ritual:
Score each LEARNINGS CANDIDATE item:
- HIGH: universal, saves 2+ hours across projects
- MEDIUM: useful but narrow
- LOW: too task-specific
For every HIGH: draft ready-to-pastefor LEARNINGS.md, PATTERNS.md, or SOP updates.
Present scored list then HIGH drafts. I reply yes/no to each.
```

## 05 — The 7-Layer Audit
For each layer: Implemented? Tested? Monitored?
Implemented only = 5/10. + Tested = 7/10. All three = 10/10.

Layer 1 — Security: no secrets in code/history, RLS tested, input validation, rate limiting, npm audit clean
Layer 2 — Data Integrity: FK ON DELETE explicit, idempotent migrations, no raw SQL in app
Layer 3 — Error Handling: all async try/catch, .error checked, no silent failures, Sentry PII scrub, error boundaries
Layer 4 — TypeScript: strict: true, zero any, no ts-ignore without comment
Layer 5 — Performance: no N+1 queries, indexes on queried cols, subscriptions cleaned up
Layer 6 — Observability: Sentry active with alerts, slow query monitoring, uptime monitoring
Layer 7 — Code Quality: no dead code, no magic numbers, Edge Function READMEs, migration comments

Request score any time:
"As the Cirra CTO, give me a full 7-layer readiness score f## 06 — Universal Patterns
See PATTERNS.md for full patterns with code examples.

### [Supabase + React] Google OAuth — proven working pattern
- flowType: 'implicit' — NEVER pkce
- redirectTo: window.location.origin + '/auth/callback'
- Dedicated /auth/callback route listening for SIGNED_IN
- Google Cloud: Supabase callback URL in redirect URIs ONLY
- Domain restriction in AuthContext not via hd= param
- Separate staffLoading from auth loading. ProtectedRoute waits for BOTH.

### [Supabase] RLS Role Checks
- Always use get_my_role() SECURITY DEFINER function
- NEVER raw subquery on staff/users table in RLS

### [Supabase] Edge Functions
- Deploy with --no-verify-jwt
- 502 = API key missing or out of credits
- Use supabase.functions.invoke() not raw fetch()

## 07 — Claude Behaviour Rules
- READ BEFORE WRITING
- DIAGNOSE FULLY BEFORE ACTING
- ONE CORRECT FIX
- NO WORKAROUNDS EVER
- SQL SAFETY
- NO INCREMENTAL DIAGNOSIS
- CERTAINTY IS THE BASELINE

## 08 — Doc Hygiene
PATTERNS.md over 800 lines → NGS.md over 100 entries → archive oldest 50
CLAUDE.md over 500 lines → extract to DB_SCHEMA_GUIDE

## 09 — Quick Reference
| Situation | What To Do |
|-----------|-----------|
| Starting any session | Activate CTO first. Run pre-brief stress test before Cursor. |
| Claude giving options | "Give me ONE recommendation only." |
| Cursor drifting | Stop. Re-paste Senior Engineer opener. Start fresh. |
| Bug survives 3 attempts | STOP. Read source files. Add logging. Understand first. |
| Want readiness score | "Give me a full 7-layer readiness score for [PROJECT]." |
| Hard problem solved | Flag LEARNINGS CANDIDATE: yes. Run capture ritual within 3-5 days. |

*The Cirra Engineering Playbook | Version 2.0 | Cirra Edge | March 2026*
