# CLAUDE.md PROJECT TEMPLATE
**Version 5.0 | Cirra Edge | April 2026**
Copy this for every new project. Fill ALL [brackets] before first Cursor session.

**A CLAUDE.md with [brackets] remaining is not a CLAUDE.md — it is a liability.**

---

## Instructions

1. Copy this template into your project root as CLAUDE.md
2. Fill every [bracket] with real project content
3. Paste relevant patterns from cirra-cto/PATTERNS.md
4. Remove this Instructions section
5. Commit: "chore: add CLAUDE.md"

---

# COPY EVERYTHING BELOW INTO CLAUDE.md

## CLAUDE.md — [PROJECT NAME]

*You are the Cirra Senior Engineer on this project.*
*Read cirra-cto/THE_CIRRA_WAY.md before every session.*
*Read cirra-cto/PATTERNS.md and apply all patterns tagged for this stack.*
*Never guess. Never invent. Never assume. If something is missing — stop and ask.*

## Project

* [PROJECT NAME] — [one sentence description]
* Company: [LIFT Mobility / Never-2-Far / Cirra Edge]
* URL: [production URL or "not yet deployed"]
* Repo: [GitHub org/repo-name]

## Stack

* Frontend: [e.g. React 18 + TypeScript + Vite + Tailwind + shadcn/ui]
* Backend: [e.g. Supabase — PostgreSQL + Auth + Storage + Edge Functions]
* Hosting: [e.g. Cloudflare Pages]
* Auth: [e.g. Google OAuth implicit flow via Supabase]
* Payments: [e.g. Stripe / none]

## Environment Variables

Stored in .env.local — never .env, never committed. See SECRETS_AND_ENV_SOP.md.

* [VAR_NAME] — purpose — where to find it

## Database

* Project ref: [supabase ref]
* Region: [e.g. ap-southeast-1]
* URL: https://[ref].supabase.co

Tables:
| Table | Purpose | Safe to Delete | RLS |
|-------|---------|---------------|-----|
| [name] | [purpose] | YES/NEVER | [summary] |

## Auth Flow

* Provider: [Google / email / PIN / other]
* Pattern: [e.g. Cirra Google OAuth Pattern — see PATTERNS.md]
* Domain restriction: [e.g. @liftmobility.co — enforced in AuthContext]
* Callback route: /auth/callback (public, no ProtectedRoute)
* Loading states: loading (auth) + staffLoading (profile) — separate

## Roles

* [role] — [what they can do]

## Core Workflows

* [e.g. Staff submits expense claim via AI extraction]
* [e.g. Customer books grave visit via booking funnel]

## Navigation

* /route — Purpose — [public / role required]

## Key Files

* src/contexts/AuthContext.tsx — auth state, profile, role checks
* src/App.tsx — routing, ProtectedRoute
* [add project-specific key files]

## Four-Agent Protocol

This project uses the Cirra Four-Agent Protocol. See FOUR_AGENT_PROTOCOL.md.

**BUILD AGENT:** Writes code. One brief at a time. Never stacks unverified briefs.
**QA AGENT:** 16-point checklist. Verdict: PASS TO DEPLOY or BLOCKED.
**DOCUMENT AGENT:** .md files only. Reads before updating. Session-end updates mandatory.
**REVIEW AGENT:** Architecture health. Triggers before major features and before production deploys.

Cursor Rules files required:
* .cursor/rules/session-opener.mdc (alwaysApply: true)
* .cursor/rules/qa-agent.mdc (alwaysApply: false)

[If using 5-agent exception, document Build 1 vs Build 2 file ownership here:]
* Build 1 owns: [file list]
* Build 2 owns: [file list]

## Pre-Brief Decision Block

Before ANY brief, fill this (THE_CIRRA_WAY Section 02):
1. Problem: [one sentence]
2. Root cause / hypothesis: [CONFIRMED or HYPOTHESIS]
3. Severity: [P0/P1/P2/P3]
4. Files affected: [list]
5. Do NOT touch: [list]
6. Test / evidence required: [what proves success]
7. Rollback plan: [how to undo in under 5 min]
8. Definition of success: [observable outcome]
9. Known unknowns: [what you're not sure about]
10. Why now: [why this vs everything else]

## Tech Debt Triggers (THE_CIRRA_WAY Section 05)

* File over 500 lines → decompose before next feature in that file
* Same bug 2x → architectural fix, not another patch
* as any over 15 in a directory → type-fix sprint
* Console statements over 20 in a file → cleanup before features
* Unverified briefs exist → verify ALL before new briefs

## Critical Issue Protocol

Any CRITICAL issue in this file must include:
* The file path where the issue exists
* The line number or grep command confirming it exists in code

Format: CRITICAL — [description] | FILE: [path] | GREP: [command]

No CRITICAL without proof in code.

## Current Status

* Phase: [e.g. Phase 1 — Auth and core UI complete]
* Live on main: [describe]
* In progress: [describe]
* Next: [describe]

## Open Issues

* [issue — severity: P0/P1/P2/P3 | FILE: path | GREP: command if CRITICAL]

## NEVER

* Never commit .env or .env.local
* Never use service role key in any frontend file
* Never bypass RLS
* Never use raw subquery in RLS — use get_my_role()
* Never use SUPABASE_ prefix for Edge Function secrets
* Never push to a branch other than main without explicit instruction
* Never add DB columns without updating this file in the same commit
* Never add a CRITICAL issue without file path and grep reference
* Never start a session without checking CI status first
* Never merge without PASS TO DEPLOY verdict from QA Agent
* Never stack unverified briefs — verify first, then next
* [add project-specific nevers]

---

*CLAUDE.md Template | Version 5.0 | Cirra Edge | April 2026*
